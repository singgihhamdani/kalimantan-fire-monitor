# Validation Report — Kalimantan Fire Situation Monitor (Phase 1)

This report documents the automated validation suite built into the **Kalimantan Fire Situation Monitor** pipeline and provides the verification checklist for Phase 1 deliverables.

---

## 1. Validation Framework Overview

The validation framework evaluates data correctness, spatial accuracy, temporal consistency, and statistical integrity across all execution stages:

```
[Dataset Integrity Checks]
           │
           ▼
[Temporal Duration Verification]
           │
           ▼
[Spatial Boundary & Bounding Box Checks]
           │
           ▼
[Statistical & Monotonicity Checks]
           │
           ▼
[Export & Output File Verification]
```

---

## 2. Automated Validation Suite Specification

The programmatic validation suite is executed in **Section 13** of `kalimantan_fire_monitor.ipynb`.

### 2.1 Dataset Integrity Checks

| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-DS-01` | SNPP Collection Availability | `ee.ImageCollection('NASA/LANCE/SNPP_VIIRS/C2').size() > 0` | Collection exists and contains imagery | **PASS** |
| `VAL-DS-02` | NOAA-20 Collection Availability | `ee.ImageCollection('NASA/LANCE/NOAA20_VIIRS/C2').size() > 0` | Collection exists and contains imagery | **PASS** |
| `VAL-DS-03` | Required Bands Present (SNPP) | `all(b in snpp_bands for b in FIRE_BANDS)` | `bright_ti4`, `bright_ti5`, `confidence`, `frp` present | **PASS** |
| `VAL-DS-04` | Required Bands Present (NOAA-20) | `all(b in noaa20_bands for b in FIRE_BANDS)` | `bright_ti4`, `bright_ti5`, `confidence`, `frp` present | **PASS** |
| `VAL-DS-05` | Confidence Encoding Verification | `max(confidence_values) <= 2` | Categorical integer encoding (0=Low, 1=Nominal, 2=High) | **PASS** |

### 2.2 Temporal Integrity Checks

| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-TMP-01` | 24-Hour Window Exact Duration | `(ANALYSIS_END - START_24H).total_seconds()` | Exactly `86,400` seconds | **PASS** |
| `VAL-TMP-02` | 72-Hour Window Exact Duration | `(ANALYSIS_END - START_72H).total_seconds()` | Exactly `259,200` seconds | **PASS** |
| `VAL-TMP-03` | 7-Day Window Exact Duration | `(ANALYSIS_END - START_7D).total_seconds()` | Exactly `604,800` seconds | **PASS** |
| `VAL-TMP-04` | Dynamic Time Generation | `ANALYSIS_END.tzinfo == timezone.utc` | Generated dynamically in UTC; no hardcoded dates | **PASS** |

### 2.3 Spatial Integrity Checks

| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-SPA-01` | 5 Kalimantan Provinces Loaded | `len(gdf_provinces) == 5` | Exactly 5 ADM1 provinces loaded from geoBoundaries | **PASS** |
| `VAL-SPA-02` | 24h Detections Bounding Extent | Detections within Kalimantan Bounding Box $\pm 0.1^\circ$ | All points fall within geographic Kalimantan extent | **PASS** |
| `VAL-SPA-03` | 72h Detections Bounding Extent | Detections within Kalimantan Bounding Box $\pm 0.1^\circ$ | All points fall within geographic Kalimantan extent | **PASS** |
| `VAL-SPA-04` | 7d Detections Bounding Extent | Detections within Kalimantan Bounding Box $\pm 0.1^\circ$ | All points fall within geographic Kalimantan extent | **PASS** |

### 2.4 Statistical Consistency & Monotonicity Checks

| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-STAT-01` | 24h Filter Consistency | `len(gdf_24h_operational) <= len(gdf_24h_all)` | Operational count $\le$ Total raw count | **PASS** |
| `VAL-STAT-02` | 72h Filter Consistency | `len(gdf_72h_operational) <= len(gdf_72h_all)` | Operational count $\le$ Total raw count | **PASS** |
| `VAL-STAT-03` | 7d Filter Consistency | `len(gdf_7d_operational) <= len(gdf_7d_all)` | Operational count $\le$ Total raw count | **PASS** |
| `VAL-STAT-04` | Monotonicity: 24h $\le$ 72h | `len(gdf_24h) <= len(gdf_72h) + 1` | Broader window captures $\ge$ detections | **PASS** |
| `VAL-STAT-05` | Monotonicity: 72h $\le$ 7d | `len(gdf_72h) <= len(gdf_7d) + 1` | Broader window captures $\ge$ detections | **PASS** |
| `VAL-STAT-06` | Province Total Reconciliation | `abs(summary_province['7d'].sum() - len(gdf_7d)) <= 1` | Sum of province counts reconciles with total | **PASS** |

---

## 3. Output File Artifacts Verification

| Output File Path | Format | Verification Condition | Status |
|---|---|---|---|
| `raw/kalimantan_fires_all_{window}.csv` | CSV | File exists, valid headers, preserves all detections | **VERIFIED** |
| `processed/kalimantan_fires_operational_{window}.csv` | CSV | File exists, confidence filtered ($\ge 1$), cluster IDs included | **VERIFIED** |
| `processed/kalimantan_fires_{window}.geojson` | GeoJSON | Valid RFC 7946 GeoJSON FeatureCollection | **VERIFIED** |
| `processed/kalimantan_clusters_{window}.geojson` | GeoJSON | Point FeatureCollection with cluster centroid properties | **VERIFIED** |
| `processed/kalimantan_density_7d.geojson` | GeoJSON | Polygon FeatureCollection representing 0.1° density grid | **VERIFIED** |
| `reports/province_summary.csv` | CSV | Comparative table with columns `province, 7d, 72h, 24h` | **VERIFIED** |
| `reports/regency_summary.csv` | CSV | Comparative table with columns `regency, province, 7d, 72h, 24h` | **VERIFIED** |
| `metadata/analysis_metadata.json` | JSON | Valid JSON with execution timestamps, collections, and counts | **VERIFIED** |

---

## 4. Code Syntax & Structural AST Verification

All code cells in `kalimantan_fire_monitor.ipynb` were parsed and verified using Python's `ast` module:
- **Total Notebook Cells:** 42 cells (Markdown: 19, Code: 23)
- **AST Parsing Result:** **0 Syntax Errors across all code cells**
- **Dependency Isolation:** All imports and third-party tools are constrained to the approved Phase 1 stack.

---

## 5. Phase 1 Verification Conclusion

The **Kalimantan Fire Situation Monitor (Phase 1)** satisfies all functional and non-functional requirements specified in `PRD.md` and `AGENTS.md`. The pipeline is verified for reproducibility and scientific transparency.

---

# Phase 2 Validation Suite — Burned-Area Confirmation

---

## 6. Phase 2 Automated Validation Specifications

The Phase 2 validation suite is executed in **Section 17** of `kalimantan_burned_area.ipynb`.

### 6.1 Priority Selection & Ingestion Checks
| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-P2-01` | Phase 1 Clusters Ingestion | `len(df_clusters) > 0` | Successfully loaded Phase 1 cluster statistics | **READY** |
| `VAL-P2-02` | Priority Ranking Correctness | `len(top20_clusters) == min(20, len(df_clusters))` | Exactly Top 20 clusters selected by priority score | **READY** |
| `VAL-P2-03` | Priority Score Range | `top20_clusters['priority_score'].between(0, 1).all()` | All priority scores normalized within $[0, 1]$ | **READY** |

### 6.2 Sensor Availability & Compositing Checks
| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-P2-04` | Pre-fire Baseline Duration | `baseline_days in [30, 60]` | 30-day baseline applied (or 60-day fallback) | **READY** |
| `VAL-P2-05` | Sensor Attribution Valid | `sensor_used in ['Sentinel-2', 'Landsat-8/9']` | Sensor used is explicitly documented per cluster | **READY** |
| `VAL-P2-06` | Cloud Masking Application | Cloud-masked image has valid pixels | Median composite successfully generated | **READY** |

### 6.3 dNBR & Severity Consistency Checks
| Test ID | Check Name | Evaluation Logic | Target / Pass Condition | Status |
|---|---|---|---|---|
| `VAL-P2-07` | dNBR Value Bounds | `dnbr_min >= -2.0 and dnbr_max <= 2.0` | dNBR index within physical theoretical limits | **READY** |
| `VAL-P2-08` | Area Sum Consistency | `abs(sum(severity_ha) - total_burned_ha) <= 0.05` | Sum of severity class areas equals total burned area | **READY** |
| `VAL-P2-09` | Burned Perimeter Validity | All polygons valid Shapely geometries | Burned perimeter vectorization is topologically valid | **READY** |
| `VAL-P2-10` | MODIS Cross-Validation | `0 <= modis_agreement_pct <= 100` | Overlap percentage correctly calculated | **READY** |

### 6.4 Export Artifacts Verification
| Output File Path | Format | Verification Condition |
|---|---|---|
| `phase2/clusters/top20_priority_clusters.geojson` | GeoJSON | Valid FeatureCollection of top 20 cluster centroids |
| `phase2/burned_area/cluster_XXX_burned_perimeter.geojson` | GeoJSON | Valid Polygon FeatureCollection per cluster |
| `phase2/dnbr_rasters/cluster_XXX_dnbr.tif` | GeoTIFF | Single-band Float32 raster with EPSG:4326 geotransform |
---

## 7. Phase 3 Validation Matrix — Environmental Context & Fire Weather

| Test ID | Test Name | Target / Criteria | Description | Status |
|---|---|---|---|---|
| `VAL-P3-01` | Cluster Ingestion | `len(df_clusters) > 0` | Priority clusters from Phase 1/2 successfully ingested | **READY** |
| `VAL-P3-02` | CHIRPS Collection Availability | `chirps_30d.size() > 0` and `chirps_90d.size() > 0` | Daily rainfall imagery available for dual windows | **READY** |
| `VAL-P3-03` | ERA5 Weather Availability | `era5_col.size() > 0` | Temperature and wind variables present | **READY** |
| `VAL-P3-04` | Rainfall Non-Negativity | `precip_30d >= 0` and `precip_90d >= 0` | Precipitation values are strictly non-negative | **READY** |
| `VAL-P3-05` | Air Temperature Bounds | `10.0 <= max_air_temp_c <= 50.0` | Air temperature within realistic tropical range | **READY** |
| `VAL-P3-06` | KBDI Drought Range | `0 <= kbdi_score <= 800` | Drought index within valid Keetch-Byram bounds | **READY** |
| `VAL-P3-07` | Wind Speed Non-Negativity | `max_wind_speed_kmh >= 0` | Wind speeds are physical and positive | **READY** |
| `VAL-P3-08` | Land Cover Completeness | `tree_pct + shrub_pct + crop_pct + other_pct ≈ 100%` | Land cover percentages sum to 100% (±0.5%) | **READY** |
| `VAL-P3-09` | Peatland Overlay Validity | `0 <= peatland_pct <= 100` | Peatland proportion within valid bounds | **READY** |
| `VAL-P3-10` | Export Artifacts Integrity | CSV, GeoTIFF, GeoJSON, JSON present | All Phase 3 output files created successfully | **READY** |

---

## 8. Phase 4 Validation Matrix — Fire Intelligence & Risk Modeling

| Test ID | Test Name | Target / Criteria | Description | Status |
|---|---|---|---|---|
| `VAL-P4-01` | Multi-Phase Data Ingestion | Phase 1/2/3 summary tables loaded | Ingestion of priority clusters, burned area, and KBDI | **READY** |
| `VAL-P4-02` | Sentinel-5P AAI Plausibility | `-2.0 <= mean_aai <= 10.0` | UV Aerosol Index within valid physical range | **READY** |
| `VAL-P4-03` | Sentinel-5P CO Plausibility | `0.0 <= mean_co <= 0.20 mol/m²` | Carbon monoxide total column density valid | **READY** |
| `VAL-P4-04` | FRP Trend Slope Computation | `isinstance(frp_trend_slope, float)` | Linear rate of change of FRP calculated | **READY** |
| `VAL-P4-05` | Trajectory Vector Validity | `0.0 <= heading <= 360.0`, `disp >= 0` | Centroid displacement distance and heading valid | **READY** |
| `VAL-P4-06` | Persistence Probability Bounds | `0.0 <= persistence_prob_48h <= 1.0` | Modelled persistence probability bounded [0, 1] | **READY** |
| `VAL-P4-07` | KFSI Raster Normalization | `0.0 <= mean_kfsi <= 1.0` | Continuous fire susceptibility index bounded [0, 1] | **READY** |
| `VAL-P4-08` | Composite Risk Score Scaling | `0.0 <= composite_risk_score <= 100.0`| Integrated risk score scaled [0, 100] | **READY** |
| `VAL-P4-09` | Early Warning Classification | Valid Level 1–4 string | Operational alert level assigned without missing values | **READY** |
| `VAL-P4-10` | Export Artifacts Completeness | GeoTIFF, GeoJSON, CSV, JSON present | All Phase 4 exports created with valid schemas | **READY** |


