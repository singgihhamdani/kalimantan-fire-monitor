# Product Requirements Document (PRD)
# Kalimantan Fire Situation Monitor — Phase 1

| Field | Value |
|---|---|
| **Version** | 1.0 |
| **Date** | 2026-08-20 |
| **Status** | Approved |
| **Platform** | Google Colab |
| **Language** | Python |

---

## 1. Problem Statement

Indonesian Kalimantan experiences recurring fire events that threaten ecosystems, public health, and regional air quality. There is no readily accessible, reproducible, and methodologically transparent tool for technical users to obtain a current-situation snapshot of active-fire detections across all five Kalimantan provinces.

Existing tools (e.g., NASA FIRMS web map, SiPongi) provide pre-built interfaces but lack:
- Reproducibility (no shareable analytical pipeline)
- Configurable filtering (fixed confidence thresholds)
- Structured data export (no GeoJSON + CSV with administrative attribution)
- Spatial clustering and density analysis in a single workflow

---

## 2. Product Overview

**Kalimantan Fire Situation Monitor** is a Google Colab notebook that provides a **reliable, reproducible snapshot** of current active-fire detections across Indonesian Kalimantan.

It is a data pipeline, not a dashboard. The output is structured data, maps, and summary tables that a technical user can execute, audit, and extend.

### 2.1 Target Users

| User | Need |
|---|---|
| GIS analysts | Current fire situation with spatial context |
| Environmental researchers | Reproducible, exportable detection data |
| Government/NGO responders | Province and regency-level situation summary |
| Data engineers | Clean, structured data for downstream systems |

### 2.2 Key Questions the Product Must Answer

| # | Question | Output |
|---|---|---|
| Q1 | Where are active-fire detections occurring right now? | Map + coordinates |
| Q2 | How many detections in the last 24 hours? | Count + table + map |
| Q3 | Which areas show persistent activity (72 h)? | Repeated-activity analysis + clusters |
| Q4 | Where is the highest concentration (7 days)? | Density grid + cluster analysis |
| Q5 | Which provinces have the most detections? | Province ranking table |
| Q6 | Which regencies have the most detections? | Regency ranking table |
| Q7 | Where are significant spatial clusters? | DBSCAN cluster table + map |

---

## 3. Goals and Non-Goals

### 3.1 Goals (Phase 1)

- [ ] **G1** — Extract VIIRS active-fire detections from both SNPP and NOAA-20 via Google Earth Engine
- [ ] **G2** — Provide three temporal views: 24 h, 72 h, 7 days
- [ ] **G3** — Assign every detection to a province and regency
- [ ] **G4** — Apply configurable confidence filtering (ALL vs OPERATIONAL)
- [ ] **G5** — Identify spatial clusters of detections using DBSCAN
- [ ] **G6** — Produce a grid-based spatial density for the 7-day window
- [ ] **G7** — Generate interactive folium maps for each temporal window
- [ ] **G8** — Export structured data (CSV + GeoJSON) to Google Drive
- [ ] **G9** — Run a comprehensive validation suite
- [ ] **G10** — Document methodology, limitations, and data dictionary

### 3.2 Non-Goals (Phase 1)

| ID | Item | Phase |
|---|---|---|
| NG1 | Burned-area mapping (Sentinel-2, Landsat, NBR/dNBR) | Phase 2 |
| NG2 | Environmental context (rainfall, temperature, wind, drought) | Phase 3 |
| NG3 | Smoke / aerosol dispersion modelling | Phase 3 |
| NG4 | Peatland / land-cover overlay | Phase 3 |
| NG5 | Fire-risk scoring or prediction | Phase 4 |
| NG6 | Machine learning / AI models | Phase 4 |
| NG7 | Interactive web dashboard (Next.js) | Phase 5 |
| NG8 | Automated scheduling / daily runs | Phase 5 |
| NG9 | Public-facing application | Phase 5 |

---

## 4. Data Requirements

### 4.1 Primary Data — VIIRS Active Fire

| Property | Suomi NPP (SNPP) | NOAA-20 |
|---|---|---|
| **Collection ID** | `NASA/LANCE/SNPP_VIIRS/C2` | `NASA/LANCE/NOAA20_VIIRS/C2` |
| **Product** | VNP14IMGTDL_NRT | VJ114IMGTDL_NRT |
| **Type** | ImageCollection (raster) | ImageCollection (raster) |
| **Resolution** | 375 m | 375 m |
| **Cadence** | Daily | Daily |
| **Quality** | NRT (not science-quality) | NRT (not science-quality) |

#### Required Bands

| Band | Description | Units | Required |
|---|---|---|---|
| `bright_ti4` | I-4 brightness temperature | Kelvin | ✅ |
| `bright_ti5` | I-5 brightness temperature | Kelvin | ✅ |
| `confidence` | Detection confidence (0/1/2) | Categorical | ✅ |
| `frp` | Fire Radiative Power | MW | ✅ |

#### Data Contract

- Both satellites must be queried independently and merged
- Each detection must preserve its `satellite` source
- Confidence encoding must be verified **programmatically** before filtering
- No fabricated collection IDs, band names, or metadata

### 4.2 Administrative Boundaries

| Level | Collection ID | Filter |
|---|---|---|
| Province (ADM1) | `WM/geoLab/geoBoundaries/600/ADM1` | `shapeGroup='IDN'`, `shapeName` in Kalimantan list |
| Regency (ADM2) | `WM/geoLab/geoBoundaries/600/ADM2` | `shapeGroup='IDN'`, spatial filter to Kalimantan |

#### Expected Kalimantan Provinces

| Indonesian Name | English Name |
|---|---|
| Kalimantan Barat | West Kalimantan |
| Kalimantan Tengah | Central Kalimantan |
| Kalimantan Selatan | South Kalimantan |
| Kalimantan Timur | East Kalimantan |
| Kalimantan Utara | North Kalimantan |

### 4.3 Raster-to-Point Conversion

VIIRS data in GEE is rasterised. The pipeline must:

1. Filter ImageCollection by date and study area
2. Extract fire pixels as point features via `ee.Image.sample()`
3. Each point = centroid of a 375 m pixel (NOT precise GPS)
4. Preserve all band values + acquisition date + satellite source
5. Keep raw detections before any aggregation/clustering

---

## 5. Functional Requirements

### FR1 — Configuration

| ID | Requirement |
|---|---|
| FR1.1 | All tuneable parameters in a single Configuration section |
| FR1.2 | Temporal windows calculated dynamically from `datetime.now(timezone.utc)` |
| FR1.3 | Never hardcode analysis dates |
| FR1.4 | `CONFIDENCE_THRESHOLD` configurable (default: 1) |
| FR1.5 | DBSCAN `eps_km` and `min_samples` configurable |
| FR1.6 | Grid size configurable (default: 0.1°) |
| FR1.7 | Google Drive base path configurable |

### FR2 — Dataset Inspection (Mandatory)

| ID | Requirement |
|---|---|
| FR2.1 | Print collection metadata for both SNPP and NOAA-20 |
| FR2.2 | Verify band names match expected set |
| FR2.3 | Print date range of available data |
| FR2.4 | Sample fire pixels and display values |
| FR2.5 | Verify confidence encoding (0/1/2 categorical) programmatically |
| FR2.6 | Warn if encoding differs from expected |

### FR3 — Fire Detection Extraction

| ID | Requirement |
|---|---|
| FR3.1 | Query both SNPP and NOAA-20 collections |
| FR3.2 | Filter spatially to Kalimantan boundary |
| FR3.3 | Filter temporally to specified window |
| FR3.4 | Convert fire raster pixels to point features |
| FR3.5 | Preserve: lat, lon, bright_ti4, bright_ti5, confidence, frp, acq_date, satellite |
| FR3.6 | Merge both satellite results into single FeatureCollection |
| FR3.7 | Convert to GeoDataFrame for local analysis |
| FR3.8 | Handle empty results gracefully (return empty GeoDataFrame, not crash) |
| FR3.9 | Apply `MAX_FEATURES` safety limit |

### FR4 — Confidence Filtering

| ID | Requirement |
|---|---|
| FR4.1 | Produce two datasets: ALL detections, OPERATIONAL detections |
| FR4.2 | OPERATIONAL = confidence >= `CONFIDENCE_THRESHOLD` |
| FR4.3 | Print counts for both sets |
| FR4.4 | Use OPERATIONAL for analysis; retain ALL for audit |

### FR5 — Administrative Assignment

| ID | Requirement |
|---|---|
| FR5.1 | Spatial join (geopandas `sjoin`) with province polygons |
| FR5.2 | Spatial join with regency polygons |
| FR5.3 | Points that don't match get `province='Unassigned'` / `regency='Unassigned'` |
| FR5.4 | Validate: province sum ≈ total count |

### FR6 — 24-Hour Analysis

| ID | Requirement | Output |
|---|---|---|
| FR6.1 | Extract detections for last 24 h | GeoDataFrame |
| FR6.2 | Detection count (all + operational) | Print |
| FR6.3 | Confidence distribution chart | Bar chart |
| FR6.4 | Satellite breakdown chart | Bar chart |
| FR6.5 | Province statistics table | Table |
| FR6.6 | Top 20 regency statistics table | Table |
| FR6.7 | Interactive folium map (points by confidence) | Map |

### FR7 — 72-Hour Analysis

| ID | Requirement | Output |
|---|---|---|
| FR7.1 | Extract detections for last 72 h | GeoDataFrame |
| FR7.2 | Daily breakdown table | Table |
| FR7.3 | Satellite × day breakdown | Table |
| FR7.4 | Repeated-activity analysis (grid cells with multi-day detections) | Count + flag |
| FR7.5 | DBSCAN clustering | cluster_id column |
| FR7.6 | Cluster statistics table (top 15) | Table |
| FR7.7 | Interactive map with cluster outlines | Map |

### FR8 — 7-Day Analysis

| ID | Requirement | Output |
|---|---|---|
| FR8.1 | Extract detections for last 7 days | GeoDataFrame |
| FR8.2 | Grid-based density (0.1° cells) | GeoDataFrame of grid cells |
| FR8.3 | Province ranking with total FRP | Table + bar chart |
| FR8.4 | Top 30 regency ranking | Table + bar chart |
| FR8.5 | DBSCAN clustering | cluster_id column |
| FR8.6 | Cluster statistics table (top 20) | Table |
| FR8.7 | Interactive map with density grid + cluster outlines | Map |

### FR9 — Spatial Clustering

| ID | Requirement |
|---|---|
| FR9.1 | Method: DBSCAN with haversine metric |
| FR9.2 | Default: eps=2.0 km, min_samples=3 |
| FR9.3 | Noise points: cluster_id = -1 |
| FR9.4 | Per-cluster stats: n_detections, centroid, extent, avg_confidence, max_frp, province, regency, first/last date |
| FR9.5 | Label: "active-fire detection cluster" (NOT "fire cluster") |
| FR9.6 | Handle edge case: fewer points than min_samples |

### FR10 — Spatial Density

| ID | Requirement |
|---|---|
| FR10.1 | Regular grid over Kalimantan |
| FR10.2 | Default cell size: 0.1° (≈ 11 km) |
| FR10.3 | Count detections per cell |
| FR10.4 | Output as GeoDataFrame with cell geometry + count |
| FR10.5 | Visualise as choropleth on folium map |

### FR11 — Administrative Summary

| ID | Requirement |
|---|---|
| FR11.1 | Combined province table: province, 24h, 72h, 7d |
| FR11.2 | Combined regency table: regency, province, 24h, 72h, 7d |
| FR11.3 | Sort descending by 7d count |

### FR12 — Visualisation

| ID | Requirement |
|---|---|
| FR12.1 | Map 1: 24 h detections (points by confidence) |
| FR12.2 | Map 2: 72 h detections (cluster outlines) |
| FR12.3 | Map 3: 7-day density grid + clusters |
| FR12.4 | All maps include province boundaries, legend |
| FR12.5 | Dark tile basemap (CartoDB dark_matter) |
| FR12.6 | Point popups with: date, satellite, confidence, FRP, province, regency |
| FR12.7 | Province bar chart (7d) |
| FR12.8 | Top 15 regency bar chart (7d) |

### FR13 — Data Export

| ID | Requirement | Destination |
|---|---|---|
| FR13.1 | Mount Google Drive | Drive |
| FR13.2 | Create dated directory structure | Drive |
| FR13.3 | Raw CSV per window (all detections) | `raw/` |
| FR13.4 | Operational CSV per window | `processed/` |
| FR13.5 | GeoJSON point files (24h, 7d) | `processed/` |
| FR13.6 | Cluster centroid GeoJSON (72h, 7d) | `processed/` |
| FR13.7 | Density grid GeoJSON (7d) | `processed/` |
| FR13.8 | Province summary CSV | `reports/` |
| FR13.9 | Regency summary CSV | `reports/` |
| FR13.10 | Cluster statistics CSV | `reports/` |
| FR13.11 | Analysis metadata JSON | `metadata/` |

#### CSV Fields

```
latitude, longitude, bright_ti4, bright_ti5, confidence, frp,
acq_date, satellite, province, regency, cluster_id
```

Only include fields that actually exist after extraction. Do not fabricate fields.

#### Google Drive Structure

```
Kalimantan-Fire-Monitor/
  YYYY-MM-DD/
    raw/                    ← all detections CSV
    processed/              ← operational CSV, GeoJSON, density
    reports/                ← summary tables, cluster stats
    metadata/               ← analysis_metadata.json
```

### FR14 — Validation

| ID | Check | Criteria |
|---|---|---|
| FR14.1 | Collection exists | size > 0 for both SNPP and NOAA-20 |
| FR14.2 | Expected bands present | All FIRE_BANDS found in both collections |
| FR14.3 | Province count | == 5 |
| FR14.4 | Temporal windows correct | 24h=86400s, 72h=259200s, 7d=604800s |
| FR14.5 | Points within Kalimantan | All lat/lon within bounding box (± 0.1° tolerance) |
| FR14.6 | Confidence filter valid | operational ≤ total for each window |
| FR14.7 | Province sums consistent | sum(province_counts) ≈ total |
| FR14.8 | Monotonicity | 24h ≤ 72h ≤ 7d (± small tolerance) |
| FR14.9 | Validation report | Print pass/fail/warning summary |

### FR15 — Methodological Limitations

The notebook must explicitly document all of these:

1. Active-fire detection ≠ confirmed forest fire
2. Cloud/smoke/timing → missed detections possible
3. One fire → multiple detections possible
4. One detection ≠ total burned area
5. 375 m resolution limits positional precision
6. NRT products have latency, not science-quality
7. Hotspot counts ≠ burned-area measurements
8. SNPP + NOAA-20 detections are not independent
9. geoBoundaries is not an official government boundary product

### FR16 — Analysis Metadata

| ID | Requirement |
|---|---|
| FR16.1 | Print analysis timestamp (UTC) |
| FR16.2 | Print all temporal windows |
| FR16.3 | Print dataset IDs |
| FR16.4 | Print confidence threshold |
| FR16.5 | Print DBSCAN parameters |
| FR16.6 | Print software versions (ee, geemap, pandas, geopandas, numpy, sklearn, folium, matplotlib) |
| FR16.7 | Print Drive export directory listing with file sizes |
| FR16.8 | Export metadata as JSON to Drive |

---

## 6. Notebook Structure

The notebook must follow this exact section ordering:

| Section | Title | Type |
|---|---|---|
| 01 | Project Overview | Markdown |
| 02 | Environment Setup | Code |
| 03 | Configuration | Code |
| 04 | Earth Engine Authentication | Code |
| 05 | Define Study Area | Code + Map |
| 06 | Inspect VIIRS Dataset | Code (mandatory) |
| 07 | Core Extraction Functions | Code |
| 08 | Last 24 Hours | Code + Charts + Map |
| 09 | Last 72 Hours | Code + Tables + Map |
| 10 | Last 7 Days | Code + Charts + Map |
| 11 | Administrative Summary | Code + Tables |
| 12 | Data Export | Code |
| 13 | Validation | Code |
| 14 | Methodological Limitations | Markdown |
| 15 | Analysis Metadata | Code |

---

## 7. Error Handling

| Scenario | Expected Behaviour |
|---|---|
| Zero detections in a temporal window | Return empty GeoDataFrame, print warning, skip analysis gracefully |
| GEE API error / timeout | Try/except, print error, suggest retry or reduce tileScale |
| Missing band in collection | Warn and list available bands |
| Points outside Kalimantan | Flag in validation, print count |
| Collection not found | Fail with clear message and collection ID |
| Too many features (> MAX_FEATURES) | Limit and warn |
| Google Drive not mounted | Prompt user, skip Drive export gracefully |
| DBSCAN with too few points | Skip clustering, set all cluster_id = -1 |

---

## 8. Terminology Rules

| Context | Use | Do NOT Use |
|---|---|---|
| Describing a detected pixel | active-fire detection | confirmed fire |
| Describing a detected pixel | thermal anomaly | forest fire |
| Describing a detected pixel | hotspot detection | wildfire |
| Describing a DBSCAN cluster | active-fire detection cluster | fire cluster, fire event |
| Counting detections | detection count | fire count |
| Describing the dataset | NRT fire product | verified fire dataset |

---

## 9. Deliverables

| # | Deliverable | Format |
|---|---|---|
| 1 | Main notebook | `kalimantan_fire_monitor.ipynb` |
| 2 | Project rules | `AGENTS.md` |
| 3 | Project overview | `README.md` |
| 4 | Field descriptions | `DATA_DICTIONARY.md` |
| 5 | Analysis methodology | `METHODOLOGY.md` |
| 6 | Validation results template | `VALIDATION_REPORT.md` |

---

## 10. Success Criteria

| Criteria | Measurement |
|---|---|
| Notebook runs end-to-end | All cells execute without error in Google Colab |
| Data correctness | Validation suite passes (all checks ✓) |
| Dual satellite | Both SNPP and NOAA-20 detections present with source labels |
| Confidence filtering | ALL vs OPERATIONAL counts printed and verified |
| Administrative accuracy | Province sum ≈ total count (± small tolerance) |
| Temporal accuracy | Window durations match configuration exactly |
| Spatial accuracy | All points within Kalimantan bounding box |
| Export completeness | All CSV, GeoJSON, and metadata files written to Drive |
| Documentation | README, DATA_DICTIONARY, METHODOLOGY, VALIDATION_REPORT present |

---

## 11. Phase 2 — Burned-Area Confirmation & Severity Assessment

### 11.1 Problem Statement (Phase 2)
While Phase 1 provides real-time active-fire detections from VIIRS thermal anomalies, thermal detections alone cannot measure the actual physical area of burned vegetation, nor assess the severity of ecological damage. Phase 2 leverages high-resolution optical imagery (Sentinel-2 at 10–20m and Landsat 8/9 at 30m) to confirm surface change and calculate burn severity for prioritized active-fire detection clusters.

### 11.2 Goals (Phase 2)
- [ ] **G11** — Ingest Phase 1 DBSCAN cluster outputs (`cluster_statistics_7d.csv`, `kalimantan_clusters_7d.geojson`)
- [ ] **G12** — Compute **Cluster Priority Scores** to rank and select Top 20 priority clusters
- [ ] **G13** — Retrieve Sentinel-2 L2A (`COPERNICUS/S2_SR_HARMONIZED`) imagery for pre-fire (30d default, 60d fallback) and post-fire periods
- [ ] **G14** — Provide automated fallback to Landsat 8/9 L2 (`LANDSAT/LC09/C02/T1_L2`, `LANDSAT/LC08/C02/T1_L2`) if S2 cloud cover exceeds threshold (>50%)
- [ ] **G15** — Apply rigorous cloud and cloud-shadow masking (S2 QA60/SCL, Landsat QA_PIXEL)
- [ ] **G16** — Compute Normalized Burn Ratio (NBR) for pre-fire and post-fire composite imagery
- [ ] **G17** — Compute Differenced Normalized Burn Ratio (dNBR) and classify into 5 USGS standard severity classes
- [ ] **G18** — Vectorize burned areas (dNBR ≥ 0.10) into polygon perimeters (GeoJSON) and calculate area in hectares
- [ ] **G19** — Export dNBR rasters as GeoTIFF files for GIS workflows
- [ ] **G20** — Cross-validate delineated burned areas against MODIS MCD64A1 Monthly Burned Area product
- [ ] **G21** — Export structured Phase 2 datasets (GeoJSON, GeoTIFF, CSV, JSON metadata)

### 11.3 Functional Requirements (Phase 2)

#### FR17 — Phase 1 Ingestion & Priority Scoring
| ID | Requirement | Output |
|---|---|---|
| FR17.1 | Load Phase 1 `cluster_statistics_7d.csv` and `kalimantan_clusters_7d.geojson` | DataFrame & GeoDataFrame |
| FR17.2 | Compute Cluster Priority Score = $w_1 \tilde{N}_{\text{det}} + w_2 \widetilde{\text{FRP}}_{\text{max}} + w_3 \tilde{C}_{\text{avg}} + w_4 \tilde{D}_{\text{persist}}$ | Column `priority_score` |
| FR17.3 | Sort clusters descending by priority score and select Top 20 | GeoDataFrame `top20_clusters` |
| FR17.4 | Generate buffer zone around each cluster centroid (default: 5.0 km) | Buffer geometries |

#### FR18 — Optical Sensor Query & Fallback Strategy
| ID | Requirement | Output |
|---|---|---|
| FR18.1 | Query Sentinel-2 L2A for pre-fire (30 days prior to cluster `first_date`) and post-fire | S2 ImageCollection |
| FR18.2 | Assess cloud contamination in S2 collection; if valid pixel ratio < 50%, expand baseline to 60 days | Expanded pre-fire window |
| FR18.3 | If S2 still insufficient, trigger automated fallback to Landsat 8/9 L2 | Landsat ImageCollection |
| FR18.4 | Explicitly record `sensor_used` ('Sentinel-2' or 'Landsat-8/9') in metadata | Column `sensor_used` |

#### FR19 — Preprocessing & Spectral Index Computation
| ID | Requirement | Output |
|---|---|---|
| FR19.1 | Mask clouds & shadows: S2 via `QA60` + `SCL`; Landsat via `QA_PIXEL` | Cloud-free ImageCollections |
| FR19.2 | Compute median composite for pre-fire and post-fire windows | `pre_fire_img`, `post_fire_img` |
| FR19.3 | Compute NBR = (NIR - SWIR2) / (NIR + SWIR2) | Bands `NBR_pre`, `NBR_post` |
| FR19.4 | Compute dNBR = NBR_pre - NBR_post | Image `dNBR` |

#### FR20 — Burn Severity Classification & Delineation
| ID | Requirement | Output |
|---|---|---|
| FR20.1 | Classify dNBR using USGS/Key & Benson (2006) thresholds | Classified raster (1–5) |
| FR20.2 | Delineate burned perimeters (dNBR ≥ 0.10) into vector polygons | GeoJSON per cluster |
| FR20.3 | Calculate burned area in hectares for each severity class | Hectare metrics per class |
| FR20.4 | Compute cluster summary statistics (total burned ha, high severity ha, % burned) | DataFrame `burned_area_summary` |

#### FR21 — MODIS MCD64A1 Cross-Validation
| ID | Requirement | Output |
|---|---|---|
| FR21.1 | Query `MODIS/061/MCD64A1` for the corresponding month and cluster area | MODIS BurnDate raster |
| FR21.2 | Compute spatial intersection between optical dNBR burned area and MODIS burned area | Overlap % and area |
| FR21.3 | Report validation metrics (agreement rate, commission/omission differences) | DataFrame `modis_validation` |

#### FR22 — Phase 2 Data Export
| ID | Requirement | Destination |
|---|---|---|
| FR22.1 | Export Top 20 priority clusters GeoJSON | `phase2/clusters/` |
| FR22.2 | Export delineated burned perimeter GeoJSON per cluster | `phase2/burned_area/` |
| FR22.3 | Export dNBR GeoTIFF raster per cluster | `phase2/dnbr_rasters/` |
| FR22.4 | Export burned area summary CSV | `phase2/reports/` |
| FR22.5 | Export MODIS cross-validation CSV | `phase2/reports/` |
| FR22.6 | Export Phase 2 analysis metadata JSON | `phase2/metadata/` |

---

## 12. Phase 3 — Environmental Context & Fire Weather Assessment

### 12.1 Problem Statement (Phase 3)
While Phase 1 provides real-time active-fire detections and Phase 2 confirms physical burned area and severity, decision-makers lack the biophysical and atmospheric context explaining the environmental conditions surrounding fire events. Understanding precipitation deficits, drought indices, ambient weather (temperature and wind), land cover types, and peatland exposure is critical for post-disaster evaluation, habitat vulnerability assessments, and resource planning.

### 12.2 Goals (Phase 3)
- [ ] **G22** — Ingest Phase 1 and Phase 2 outputs (`top20_burned_area_summary.csv`, `top20_priority_clusters.geojson`, burned perimeter polygons)
- [ ] **G23** — Extract dual-window precipitation (30-day recent and 90-day cumulative) from CHIRPS (`UCSB-CHG/CHIRPS/DAILY`) across all 5 Kalimantan provinces and per priority cluster
- [ ] **G24** — Compute 30-day and 90-day precipitation anomalies relative to long-term climatology (1981–2025)
- [ ] **G25** — Extract weather variables (air temperature, dew point, wind speed and direction) from ERA5-Land (`ECMWF/ERA5_LAND/DAILY_AGGR`) and Land Surface Temperature from MODIS (`MODIS/061/MOD11A1`)
- [ ] **G26** — Compute the Keetch-Byram Drought Index (KBDI) as an objective measure of soil moisture deficit
- [ ] **G27** — Overlay ESA WorldCover v200 (10m) to identify land cover composition and vegetation types affected
- [ ] **G28** — Overlay Global Peatland Map 2.0 (1km) to determine peatland spatial association and quantify burned area on peat vs mineral soils
- [ ] **G29** — Construct an Integrated Environmental Profile for each of the Top 20 priority clusters
- [ ] **G30** — Export structured Phase 3 datasets (GeoJSON, GeoTIFF, CSV, JSON metadata) and execute automated validation

### 12.3 Functional Requirements (Phase 3)

#### FR23 — Precipitation & Anomaly Analysis
| ID | Requirement | Output |
|---|---|---|
| FR23.1 | Query CHIRPS Daily for 30-day and 90-day windows prior to analysis date | ImageCollections |
| FR23.2 | Calculate 30-day and 90-day cumulative precipitation rasters for Kalimantan | `precip_30d`, `precip_90d` |
| FR23.3 | Compute 30d and 90d precipitation anomalies relative to historical mean | `precip_anomaly_30d`, `90d` |
| FR23.4 | Extract zonal precipitation statistics (mean, min, max, dry days) for Top 20 clusters (5 km buffer) | DataFrame `cluster_precip` |

#### FR24 — Ambient Weather & Temperature Analysis
| ID | Requirement | Output |
|---|---|---|
| FR24.1 | Query ERA5-Land Daily for air temperature at 2m, dewpoint, and 10m wind components (u, v) | ImageCollection |
| FR24.2 | Calculate maximum daily air temperature (°C) and daily wind speed ($\sqrt{u^2 + v^2}$) | Images `tmax_daily`, `wind_speed` |
| FR24.3 | Compute prevailing wind direction ($\text{atan2}(-u, -v)$) during active fire period | Image `wind_dir` |
| FR24.4 | Extract MODIS Land Surface Temperature (LST Day & Night) over cluster buffers | DataFrame `cluster_weather` |

#### FR25 — Soil Moisture Deficit & KBDI Computation
| ID | Requirement | Output |
|---|---|---|
| FR25.1 | Implement daily Keetch-Byram Drought Index (KBDI) formula using ERA5 Tmax and CHIRPS precipitation | Image `KBDI` (0–800) |
| FR25.2 | Classify KBDI into standard classes (Wet: 0-200, Moderate: 200-400, Dry: 400-600, Severe: 600-800) | Classified raster |
| FR25.3 | Extract mean and max KBDI for each priority cluster buffer | DataFrame `cluster_kbdi` |

#### FR26 — Land Cover Classification Overlay
| ID | Requirement | Output |
|---|---|---|
| FR26.1 | Query ESA WorldCover v200 (10m) over Kalimantan | Image `worldcover` |
| FR26.2 | Compute percentage composition of 11 land cover classes within 5 km buffer of each cluster | DataFrame `cluster_landcover` |
| FR26.3 | Overlay land cover with Phase 2 burned perimeters to calculate exact hectares burned per ecosystem type | Hectare breakdown |

#### FR27 — Peatland Spatial Association Overlay
| ID | Requirement | Output |
|---|---|---|
| FR27.1 | Query Global Peatland Map 2.0 (`projects/sat-io/open-datasets/GLOBAL_PEATLAND_MAP`) in GEE | Image `peatland_map` |
| FR27.2 | Classify each cluster as Peatland vs Mineral soil | Column `is_peatland`, `peat_pct` |
| FR27.3 | Calculate total confirmed burned area on peatland vs mineral soils | DataFrame `peatland_overlay` |

#### FR28 — Phase 3 Data Export & Integrated Profile
| ID | Requirement | Destination |
|---|---|---|
| FR28.1 | Export 30d & 90d precipitation GeoTIFF rasters | `phase3/precipitation/` |
| FR28.2 | Export KBDI drought index GeoTIFF raster | `phase3/drought/` |
| FR28.3 | Export Integrated Environmental Summary CSV | `phase3/reports/` |
| FR28.4 | Export Provincial Environmental Context CSV | `phase3/reports/` |
| FR28.5 | Export Phase 3 analysis metadata JSON | `phase3/metadata/` |

---

## 13. Phase 4 — Fire Intelligence & Risk Modeling

### 13.1 Problem Statement (Phase 4)
While Phases 1–3 provide retrospective and contextual understanding of fire occurrences, emergency responders and decision-makers require actionable intelligence regarding smoke dispersion, air quality exposure, active cluster trajectory/persistence, and continuous landscape-level fire susceptibility. Transforming satellite observations into predictive risk models enables proactive resource positioning and targeted early warnings.

### 13.2 Goals (Phase 4)
- [ ] **G31** — Ingest Phase 1, 2, and 3 outputs (`fire_weather_integrated_summary.csv`, `top20_burned_area_summary.csv`, `kalimantan_kbdi.tif`, `top20_priority_clusters.geojson`)
- [ ] **G32** — Extract Sentinel-5P TROPOMI UV Aerosol Index (AAI) and Carbon Monoxide (CO) to map atmospheric smoke plume dispersion across Kalimantan and over priority clusters
- [ ] **G33** — Model active-fire cluster dynamics and persistence probability (24h/48h) using multi-temporal FRP trends, spatial expansion vectors, and dryness indices
- [ ] **G34** — Compute the Kalimantan Fire Susceptibility Index (KFSI) via Multi-Criteria Decision Analysis integrating fuel, dryness, peatland, topography (SRTM), and historical hotspot density
- [ ] **G35** — Generate an Integrated Cluster Risk & Early Warning Profile for the Top 20 priority clusters
- [ ] **G36** — Export structured Phase 4 datasets (GeoJSON trajectories, GeoTIFF risk rasters, CSV risk summaries, JSON metadata) and execute automated validation

### 13.3 Functional Requirements (Phase 4)

#### FR29 — Smoke & Atmospheric Dispersion Mapping
| ID | Requirement | Output |
|---|---|---|
| FR29.1 | Query Sentinel-5P TROPOMI UV Aerosol Index (`COPERNICUS/S5P/OFFL/L3_AER_AI` with NRTI fallback) | Image `aerosol_index` |
| FR29.2 | Query Sentinel-5P TROPOMI Carbon Monoxide (`COPERNICUS/S5P/OFFL/L3_CO` with NRTI fallback) | Image `co_column` |
| FR29.3 | Extract zonal mean & max AAI and CO concentrations for Top 20 clusters (10 km smoke buffer) | DataFrame `cluster_smoke` |
| FR29.4 | Classify smoke plume severity (Low: AAI < 1.0, Moderate: 1.0–2.0, Dense: 2.0–3.0, Hazardous: ≥ 3.0) | Column `smoke_class` |

#### FR30 — Active-Fire Persistence & Trajectory Forecasting
| ID | Requirement | Output |
|---|---|---|
| FR30.1 | Compute multi-temporal FRP rate of change ($\Delta \text{FRP} / \Delta t$) across detection timeline | Column `frp_trend` |
| FR30.2 | Calculate cluster centroid displacement vector (distance, heading, velocity in km/day) | GeoJSON `cluster_trajectories` |
| FR30.3 | Model 24h and 48h persistence probability based on FRP trend, cluster density, KBDI, and fuel factor | Column `persistence_prob_24h`, `48h` |
| FR30.4 | Categorize persistence risk (Low: <0.30, Moderate: 0.30–0.60, High: 0.60–0.80, Extreme: ≥0.80) | Column `persistence_category` |

#### FR31 — Spatial Fire Susceptibility Modeling (KFSI)
| ID | Requirement | Output |
|---|---|---|
| FR31.1 | Process 5 biophysical & environmental criteria: Fuel (WorldCover), Dryness (KBDI + Rain Anomaly), Peatland (GPM 2.0), Topography (SRTM Slope), Historical Hotspot Density | Normalized rasters (0–1) |
| FR31.2 | Execute Weighted Linear Combination (WLC) in GEE to generate continuous KFSI raster (0–1) | Image `kalimantan_kfsi` |
| FR31.3 | Classify KFSI into 5 standard hazard zones: Very Low (<0.20), Low (0.20–0.40), Moderate (0.40–0.60), High (0.60–0.80), Very High (≥0.80) | Classified raster |
| FR31.4 | Calculate zonal mean KFSI and high-risk area (ha) per regency across all 5 provinces | DataFrame `regional_risk` |

#### FR32 — Integrated Cluster Risk Profiling & Early Warning
| ID | Requirement | Output |
|---|---|---|
| FR32.1 | Synthesize Phase 1 (Hotspots), Phase 2 (Burned Area), Phase 3 (Weather & Peat), and Phase 4 (Smoke & KFSI) | Unified risk table |
| FR32.2 | Assign Actionable Early Warning Level (Level 1: Monitor, Level 2: Alert, Level 3: High Priority Action, Level 4: Critical Emergency) | Column `early_warning_level` |
| FR32.3 | Generate Early Warning Bulletin table for operational responders | DataFrame `early_warning_bulletin` |

#### FR33 — Phase 4 Data Export Pipeline
| ID | Requirement | Destination |
|---|---|---|
| FR33.1 | Export Kalimantan Fire Susceptibility GeoTIFF raster | `phase4/risk/` |
| FR33.2 | Export Sentinel-5P Aerosol Index GeoTIFF raster | `phase4/smoke/` |
| FR33.3 | Export Cluster Trajectories GeoJSON | `phase4/persistence/` |
| FR33.4 | Export Smoke Dispersion Summary CSV | `phase4/smoke/` |
| FR33.5 | Export Cluster Persistence Forecast CSV | `phase4/persistence/` |
| FR33.6 | Export Integrated Fire Risk Summary CSV | `phase4/reports/` |
| FR33.7 | Export Early Warning Bulletin CSV | `phase4/reports/` |
| FR33.8 | Export Phase 4 Analysis Metadata JSON | `phase4/metadata/` |

---

## 14. Phase 4B — Peatland Ecohydrology & PFVI Forecasting (PeatFR Adaptation)

### 14.1 Objective
Adapt and implement the Peat Fire Vulnerability Index (PFVI) from the `peatfr` R package (Mahdiyasa et al., *Ecological Informatics*, 2025) into a native Python workflow. Evaluate the ecohydrological response of tropical peatlands to drought and compare PFVI against the mineral-soil KBDI baseline from Phase 3.

### 14.2 Mathematical Specifications
1. **Evaporative Drying Factor ($DF$):**
   $$DF_t = \frac{(300 - \text{PFVI}_t) \cdot \left[0.4982 \cdot \exp(0.0905 \cdot T_t + 1.6096) - 4.268\right] \cdot \Delta t \cdot 10^{-3}}{1 + 10.88 \cdot \exp(-0.001736 \cdot R_0)}$$
2. **Effective Rainfall Reduction Factor ($RF$):**
   $$RF_t = \max(0, Rf_t - 5.1)$$
3. **van Genuchten (1980) Peat Water Retention Factor ($WTF$):**
   $$\theta(h) = \left[1 + \left(\frac{h}{\alpha}\right)^n\right]^{-(1 - 1/n)}, \quad WTF_t = a_H - b_H \cdot \left[(1 - \theta(h)) \cdot 300\right]$$
4. **State Equation:**
   $$\text{PFVI}_{t+1} = \text{PFVI}_t + DF_t - RF_t - WTF_t$$
5. **Optimization:** Nelder-Mead Simplex optimization minimizing MSE against observed peat moisture drought index $DI_{obs}$.

### 14.3 Functional Requirements (Phase 4B)
| ID | Requirement | Output |
|---|---|---|
| FR34.1 | Ingest Phase 1, 2, and 3 summary data for the 10 priority clusters | DataFrame `df_clusters` |
| FR34.2 | Extract/reconstruct 60-day ecohydrological series ($Rf, Temp, WTD, SM$) | Series per cluster |
| FR34.3 | Calibrate van Genuchten parameters ($a_H, b_H, n, \alpha$) via Nelder-Mead | Calibrated parameters |
| FR34.4 | Compute daily PFVI and classify into 4 hazard levels (Low, Moderate, High, Extreme) | Column `pfvi_score`, `pfvi_class` |
| FR34.5 | Forecast 7-day future trajectories using autoregressive ARIMA models | DataFrame `df_forecast_7d` |
| FR34.6 | Conduct A/B comparative benchmarking between normalized KBDI (0–100) and PFVI (0–100) | DataFrame `df_comparison` |
| FR34.7 | Export Phase 4B reports, spatial GeoJSON, and metadata to `export/phase4b_peat/` | CSV, GeoJSON, JSON |

---

## 15. Future Phases (Roadmap Context)

| Phase | Focus | Key Capabilities |
|---|---|---|
| Phase 5 | Production Dashboard | Next.js interactive web app, automated daily pipeline refresh |

---

## 16. Guiding Principles

> 1. **Accuracy before complexity.** (Phase 1)
> 2. **Confirmation before escalation.** (Phase 2)
> 3. **Context without causal speculation.** (Phase 3)
> 4. **Actionable intelligence with calibrated certainty.** (Phase 4)
>
> An integrated environmental assessment that contextualizes active fires with verified precipitation, drought indices, and land cover without claiming unproven causality is scientifically sound and operationally actionable.


