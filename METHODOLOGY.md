# Methodology — Kalimantan Fire Situation Monitor (Phase 1)

This document provides a detailed remote sensing, spatial analysis, and data engineering methodology for the **Kalimantan Fire Situation Monitor (Phase 1)**.

---

## 1. Remote Sensing Foundations & Sensor Characteristics

### 1.1 Physical Basis of VIIRS Active-Fire Detection
The Visible Infrared Imaging Radiometer Suite (VIIRS) active-fire algorithm detects thermal anomalies on the Earth's surface by exploiting the spectral contrast in the mid-infrared (MIR) and thermal-infrared (TIR) portions of the electromagnetic spectrum:

1. **Mid-Infrared Channel I-4 (~3.75 µm):**  
   According to Planck's Radiation Law and Wien's Displacement Law, objects at typical terrestrial ambient temperatures (~300 K) emit peak radiation in the longwave thermal infrared (~10 µm). In contrast, objects undergoing active flaming or smoldering combustion (600 K – 1200 K) experience peak spectral radiance shifted dramatically toward the mid-infrared (3–4 µm). Consequently, even a small sub-pixel fire (e.g., covering only 0.01% of a pixel area) causes a marked increase in the apparent brightness temperature of Channel I-4 (`bright_ti4`).

2. **Thermal-Infrared Channel I-5 (~11.0 µm):**  
   Channel I-5 (`bright_ti5`) is primarily sensitive to background ambient surface temperature and cloud cover. Comparing `bright_ti4` against `bright_ti5` allows the detection algorithm to differentiate true high-temperature thermal emissions from solar reflection, warm non-fire surfaces (e.g., bare soil, urban areas), and clouds.

3. **Fire Radiative Power (FRP):**  
   FRP measures the rate of radiant heat output from the detected fire pixel in Megawatts (MW). Based on the empirical relationship developed by Wooster et al., FRP is proportional to the rate of biomass consumption.

---

## 2. Satellite Platforms & Dual-Source Integration

### 2.1 Satellite Specifications

| Sensor Platform | Orbit | Equator Overpass Times (Local Solar Time) | GEE ImageCollection ID |
|---|---|---|---|
| **Suomi NPP (SNPP)** | Sun-synchronous, ~824 km | ~13:30 (Ascending) / ~01:30 (Descending) | `NASA/LANCE/SNPP_VIIRS/C2` |
| **NOAA-20 (JPSS-1)** | Sun-synchronous, ~824 km | ~13:30 (Ascending) / ~01:30 (Descending) — ~50 min offset from SNPP | `NASA/LANCE/NOAA20_VIIRS/C2` |

### 2.2 Dual-Satellite Rationale & Handling
- **Observation Coverage:** NOAA-20 orbits in the same orbital plane as Suomi NPP but flies approximately 50 minutes ahead/behind. This provides enhanced spatial sampling, fills inter-orbit swath gaps at tropical latitudes, and increases the likelihood of observing cloud-free views.
- **Independence Principle:** Observations from SNPP and NOAA-20 are **not independent fire events**. A single physical combustion event may be detected by both SNPP and NOAA-20 within a ~50-minute interval.
- **Implementation:** Both collections are extracted independently and tagged with their platform name (`satellite: 'SNPP'` or `'NOAA-20'`). Raw detections are preserved without lossy or premature deduplication.

---

## 3. Data Extraction Pipeline (Raster to Point)

VIIRS active-fire data in Google Earth Engine are delivered as daily raster `ImageCollections` containing active-fire pixels.

```
[VIIRS ImageCollection (SNPP & NOAA-20)]
               │
               ▼
[Spatial Filter (Kalimantan Boundary)]
               │
               ▼
[Temporal Filter (24h / 72h / 7d Windows)]
               │
               ▼
[Pixel Sampling (ee.Image.sample @ 375m scale)]
               │
               ▼
[Point FeatureCollection (Centroid Coordinates & Band Attributes)]
               │
               ▼
[Administrative Spatial Join (geoBoundaries ADM1 & ADM2)]
               │
               ▼
[Confidence Split: ALL vs OPERATIONAL (confidence >= 1)]
```

### 3.1 Pixel Extraction Details
- For each daily image in the filtered collection, `ee.Image.sample()` extracts the spatial coordinates and band values (`bright_ti4`, `bright_ti5`, `confidence`, `frp`) for all valid active-fire pixels within the Kalimantan study boundary.
- **Pixel Centroid Representation:** Extracted point coordinates represent the geographic center of the 375 m pixel footprint on the ground, not a precision pinpoint of the physical flame front.

---

## 4. Temporal Windows

Three non-overlapping analysis windows are dynamically computed relative to the execution timestamp (`datetime.now(timezone.utc)`):

```
                        NOW (UTC)
                            │
◀──────── 24 Hours ─────────┤ (Immediate Situation Snapshot)
◀──────────────── 72 Hours ─┤ (Persistence & Early Cluster Formation)
◀──────────────────────── 7 Days ────────────────────────┤ (Regional Density & Ranking)
```

1. **Last 24 Hours (Immediate Snapshot):**
   - Focus: Real-time situational awareness.
   - Outputs: Immediate hotspot counts, satellite platforms, confidence distributions, and current province/regency tables.

2. **Last 72 Hours (Persistence & Dynamic Clusters):**
   - Focus: Tracking ongoing burning activity across multiple satellite overpasses.
   - Outputs: Day-by-day satellite breakdown, multi-day pixel persistence proxy, and DBSCAN clustering.

3. **Last 7 Days (Regional Patterns & Hotspot Density):**
   - Focus: Synoptic spatial patterns and regional hotspots.
   - Outputs: 0.1° density grid choropleth, comprehensive province and regency rankings, and persistent cluster delineations.

---

## 5. Confidence Filtering Strategy

VIIRS Collection 2 active-fire algorithms assign a categorical confidence flag:
- `0`: **Low Confidence** (Marginal thermal anomalies, often associated with sun glint edges, sensor artifacts, or low-temperature backgrounds).
- `1`: **Nominal Confidence** (Standard, high-probability active-fire detection free from severe contamination).
- `2`: **High Confidence** (Intense thermal anomaly, saturated detector channels, high-temperature combustion).

### Two-Tier Dataset Architecture
1. **`ALL DETECTIONS` (Raw Tier):**
   - Retains all detections (`confidence >= 0`).
   - Purpose: Sensitivity analysis, forensic auditing, and false-negative investigations.
2. **`OPERATIONAL DETECTIONS` (Operational Tier):**
   - Retains nominal and high confidence detections (`confidence >= 1`).
   - Purpose: Primary operational reporting, map rendering, and administrative rankings to minimize false alarms.

---

## 6. Spatial Clustering Methodology (DBSCAN)

To detect meaningful spatial concentrations of thermal anomalies without imposing arbitrary geometric shapes, Density-Based Spatial Clustering of Applications with Noise (**DBSCAN**) is employed.

### 6.1 Algorithmic Configuration
- **Distance Metric:** **Haversine Distance** applied to spherical latitude/longitude coordinates converted to radians ($R_{earth} \approx 6,371.01\text{ km}$).
- **Neighborhood Radius ($\epsilon$):** `2.0 km` — Selected based on the 375 m pixel footprint, allowing grouping of contiguous and neighboring fire front pixels while separating distinct localized events.
- **Minimum Points ($MinPts$):** `3` — Requires at least 3 detections to establish a core cluster, filtering out isolated point detections as spatial noise (`cluster_id = -1`).

### 6.2 Per-Cluster Metrics
For each identified cluster, the pipeline computes:
- Detection count ($N$)
- Spatial centroid $(\bar{\phi}, \bar{\lambda})$
- North-south and east-west bounding extents in kilometers:
  $$\Delta Lat\text{ (km)} = (\phi_{max} - \phi_{min}) \times 111.13$$
  $$\Delta Lon\text{ (km)} = (\lambda_{max} - \lambda_{min}) \times 111.13 \times \cos(\bar{\phi})$$
- Maximum and average Fire Radiative Power (MW)
- Temporal lifespan ($Date_{first}$ to $Date_{last}$)
- Majority administrative attribution (Province and Regency)

---

## 7. Spatial Density Estimation (Regular Grid)

To visualize broader regional concentrations over the 7-day period without overfitting:
- **Grid Resolution:** `0.1° × 0.1°` (approximately $11.1\text{ km} \times 11.1\text{ km}$ at the equator).
- **Computation:** Detections are assigned to 2D spatial bins via coordinate digitization. Each cell aggregates the total count of operational detections.
- **Advantages:** Computationally efficient, mathematically transparent, and avoids arbitrary kernel bandwidth selection.

---

## 8. Administrative Aggregation & Spatial Joins

Spatial joins are performed in Python using GeoPandas (`gpd.sjoin`) with a point-in-polygon predicate:
1. **Level 1 (ADM1):** geoBoundaries Indonesia ADM1 dataset filtered to the 5 Kalimantan provinces.
2. **Level 2 (ADM2):** geoBoundaries Indonesia ADM2 dataset spatially intersecting Kalimantan.
3. Points lying outside boundary polygon tolerances are assigned `'Unassigned'` to preserve data completeness.

---

## 9. Methodological Limitations & Interpretation Safeguards

1. **Thermal Anomaly ≠ Confirmed Forest Fire:** Satellite sensors detect radiant heat. Detections may represent agricultural burning, land clearing, industrial flares, or non-forest ground fires.
2. **Atmospheric Obscuration:** Cloud cover and dense smoke attenuate infrared radiation, causing potential omission errors (false negatives).
3. **No Direct Burned Area Equivalence:** Active-fire hotspot counts cannot be directly converted to burned area. A single 375 m pixel detection indicates the presence of fire within that footprint, not that the entire 14-hectare area burned.
4. **Orbital Sampling Constraints:** VIIRS provides twice-daily overpasses per satellite. Fast-moving or short-duration fires ignited and extinguished between satellite passes may not be detected.

---

# Phase 2 Methodology — Burned-Area Confirmation & Severity Assessment

---

## 10. Physical Basis of Optical Burned-Area Mapping

### 10.1 Spectral Response of Burned Vegetation
Fire transforms live, chlorophyll-rich, moisture-laden vegetation into charcoal, ash, and exposed mineral soil:
1. **Near-Infrared (NIR, ~860 nm):** Healthy vegetation exhibits high reflectance due to multiple scattering within the spongy mesophyll of green leaves. Fire destroys cell structure and consumes foliage, causing a severe drop in NIR reflectance.
2. **Shortwave-Infrared (SWIR2, ~2200 nm):** Live vegetation absorbs SWIR radiation due to water content. Fire vaporizes moisture, exposes dry soil, and leaves char/ash, resulting in a dramatic increase in SWIR reflectance.

```
Reflectance (%)
    ▲
    │         Healthy Vegetation (Pre-Fire)
    │             ┌─────────┐ (High NIR)
    │            ╱           ╲
    │           ╱             ╲
    │  ─────────               ───────── (Low SWIR)
    │
    │         Burned Surface (Post-Fire)
    │                          ┌─────────┐ (High SWIR)
    │                         ╱
    │  ───────               ╱
    │         ╲             ╱
    │          └───────────┘ (Low NIR)
    └────────────────────────────────────────► Wavelength (nm)
             NIR (~865nm)     SWIR2 (~2200nm)
```

---

## 11. Normalized Burn Ratio (NBR) & Differenced NBR (dNBR)

### 11.1 Formulas
The **Normalized Burn Ratio (NBR)** normalizes the spectral contrast between NIR and SWIR:
$$\text{NBR} = \frac{\rho_{\text{NIR}} - \rho_{\text{SWIR2}}}{\rho_{\text{NIR}} + \rho_{\text{SWIR2}}}$$

- **Sentinel-2 L2A:** $\text{NBR} = \frac{\text{B8A} - \text{B12}}{\text{B8A} + \text{B12}}$
- **Landsat 8/9 L2:** $\text{NBR} = \frac{\text{SR\_B5} - \text{SR\_B7}}{\text{SR\_B5} + \text{SR\_B7}}$

The **Differenced Normalized Burn Ratio (dNBR)** measures post-fire spectral departure from the pre-fire baseline:
$$\text{dNBR} = \text{NBR}_{\text{pre}} - \text{NBR}_{\text{post}}$$

Higher dNBR values indicate more severe vegetation loss and soil alteration.

### 11.2 Burn Severity Categorization
Standard thresholds from **Key & Benson (2006)** and the **USGS FireMon / Burned Area Emergency Response (BAER)** program:

| Class Code | Burn Severity Class | dNBR Range | Physical & Ecological Interpretation |
|---|---|---|---|
| 0 | **Unburned / Regrowth** | $< 0.10$ | Undamaged vegetation, water body, or post-fire vegetation green-up |
| 1 | **Low Severity** | $0.10 \le \text{dNBR} < 0.27$ | Surface litter charred; foliage scorched but largely retained; tree survival high |
| 2 | **Moderate-Low Severity** | $0.27 \le \text{dNBR} < 0.44$ | Understory consumed; 20–50% canopy scorch; mixed tree mortality |
| 3 | **Moderate-High Severity** | $0.44 \le \text{dNBR} < 0.66$ | 50–80% canopy consumed; deep duff charring; high overstory mortality |
| 4 | **High Severity** | $\ge 0.66$ | >80% canopy consumed; ash and bare soil exposed; near-complete vegetation mortality |

---

## 12. Pre-Fire Baseline & Compositing Strategy

### 12.1 Dynamic Temporal Windows
Each cluster has its own tailored temporal windows relative to its observed active-fire period:
- **`first_date`**: Earliest VIIRS detection date in cluster.
- **`last_date`**: Latest VIIRS detection date in cluster.

```
       Pre-fire Baseline Window                      Post-fire Window
[first_date - 30d  ───►  first_date - 2d]        [last_date + 2d  ───►  last_date + 30d]
                │                                                │
                └─────────── VIIRS ACTIVE FIRE ──────────────────┘
                           [first_date ── last_date]
```

### 12.2 Baseline Extension & Fallback Mechanism
1. **Primary Baseline (30 Days):** Default `BASELINE_DAYS = 30` provides an immediate pre-disturbance vegetation condition without seasonal phenological shifts.
2. **Extended Baseline (60 Days):** If cloudy pixels exceed 50% in the 30-day window, the baseline expands to `FALLBACK_BASELINE_DAYS = 60` (`first_date - 60d` to `first_date - 2d`).
3. **Median Pixel Compositing:** Cloud-masked scene collections are aggregated using pixel-wise median (`.median()`), effectively removing residual cloud shadows and sensor artifacts.

---

## 13. Cloud Masking & Quality Control

### 13.1 Sentinel-2 Masking
- **`QA60` Bitmask:** Bit 10 (Opaque Clouds) and Bit 11 (Cirrus Clouds) must equal 0.
- **Scene Classification Layer (`SCL`):** Excludes values 3 (Cloud Shadow), 8 (Cloud Medium Probability), 9 (Cloud High Probability), 10 (Thin Cirrus), and 11 (Snow/Ice).

### 13.2 Landsat 8/9 Masking
- **`QA_PIXEL` Bitmask:** Clears Dilated Cloud (Bit 1), Cirrus (Bit 2), Cloud (Bit 3), and Cloud Shadow (Bit 4).

---

## 14. Cluster Prioritization Algorithm (Multi-Criteria Scoring)

To focus high-resolution optical processing on the most impactful events:
$$\text{Priority Score} = w_{\text{count}} \cdot \tilde{N} + w_{\text{frp}} \cdot \widetilde{\text{FRP}}_{\text{max}} + w_{\text{conf}} \cdot \tilde{C}_{\text{avg}} + w_{\text{persist}} \cdot \tilde{D}$$

Where:
- $\tilde{N} = \frac{N - N_{\min}}{N_{\max} - N_{\min}}$ (Detection volume, weight = 0.35)
- $\widetilde{\text{FRP}}_{\max} = \frac{\text{FRP}_{\max} - \text{FRP}_{\min}}{\text{FRP}_{\max} - \text{FRP}_{\min}}$ (Thermal energy intensity, weight = 0.30)
- $\tilde{C}_{\text{avg}} = \frac{C_{\text{avg}} - C_{\min}}{C_{\max} - C_{\min}}$ (Detection certainty, weight = 0.15)
- $\tilde{D} = \frac{D - D_{\min}}{D_{\max} - D_{\min}}$ (Active duration in days, weight = 0.20)

Top 20 clusters with the highest scores are selected for Phase 2 optical analysis.

---

## 15. Cross-Validation with MODIS MCD64A1

The **MODIS Monthly Burned Area product (MCD64A1 Version 6.1)** provides an independent 500 m global reference.
- **Spatial Overlap:** Optical burned-area polygons are intersected with MCD64A1 `BurnDate > 0` pixels for the corresponding month.
- **Metrics Computed:**
  - $\text{Agreement Area (ha)} = \text{Area}(\text{Optical}_{\text{burned}} \cap \text{MODIS}_{\text{burned}})$
  - $\text{Agreement Rate (\%)} = \frac{\text{Agreement Area}}{\text{Total Optical Burned Area}} \times 100$
- **Interpretation:** High resolution Sentinel-2 (20m) frequently detects small, fragmented burns that 500m MODIS omits. Discrepancies are documented as resolution-dependent omission/commission rather than errors.

---

# Phase 3 Methodology — Environmental Context & Fire Weather

---

## 16. Environmental Context & Fire Weather Principles

Phase 3 enriches active-fire detections (Phase 1) and confirmed burned areas (Phase 2) with biophysical and atmospheric layers.

> **Methodological Rule on Causality:**  
> Remote sensing observations demonstrate **spatial co-occurrence and environmental predisposition**, not direct human or natural ignition causality.  
> Formulations such as *"Fires were caused by peatland"* are methodologically invalid; correct framing is *"Fires occurred in areas with high peatland exposure under negative precipitation anomalies"*.

---

## 17. Dual-Window Precipitation & Climatological Anomaly

### 17.1 CHIRPS 30-Day vs 90-Day Accumulation
- **30-Day Window ($P_{30}$):** Captures short-term, immediate moisture conditions and surface fuel desiccation prior to fire detection.
- **90-Day Window ($P_{90}$):** Captures seasonal, cumulative rainfall deficits affecting deep duff layers and water table levels in peat ecosystems.

### 17.2 Precipitation Anomaly Calculation
$$\text{Anomaly (\%)} = \frac{P_{\text{observed}} - \mu_{\text{historical}}}{\mu_{\text{historical}}} \times 100$$
Where $\mu_{\text{historical}}$ is the 40-year historical mean (1981–2025) for the corresponding 30-day or 90-day calendar period.
- $\text{Anomaly} < -40\%$: Severe rainfall deficit
- $-40\% \le \text{Anomaly} < -20\%$: Moderate rainfall deficit
- $-20\% \le \text{Anomaly} \le +20\%$: Normal rainfall
- $\text{Anomaly} > +20\%$: Above-average rainfall

---

## 18. Ambient Weather & Fire Behavior

### 18.1 Maximum Daily Air Temperature & Land Surface Temperature
- **ERA5-Land 2m Temperature ($T_{2\text{m}}$):** Hourly reanalysis converted from Kelvin to Celsius: $T(^\circ\text{C}) = T(\text{K}) - 273.15$. Daily maximum is extracted over cluster buffers.
- **MODIS Land Surface Temperature (LST):** Captures radiative skin temperature of surface fuels and canopy top ($1\text{ km}$ resolution).

### 18.2 Wind Velocity & Vector Direction
- **Wind Speed:** $V_{\text{wind}} = \sqrt{u_{10}^2 + v_{10}^2} \times 3.6\text{ km/h}$.
- **Wind Direction:** $\theta = \left(\text{atan2}(-u_{10}, -v_{10}) \times \frac{180}{\pi}\right) \pmod{360}^\circ$.
- Used to evaluate whether elongated burned-area perimeters align with prevailing wind vectors.

---

## 19. Keetch-Byram Drought Index (KBDI)

The **Keetch-Byram Drought Index (Keetch & Byram, 1968)** models cumulative moisture depletion in the upper soil layers and forest floor:

$$KBDI_t = (KBDI_{t-1} - R_{\text{net}}) + dQ$$

Where the daily drought factor increment $dQ$ is:
$$dQ = \frac{(800 - Q) \cdot (0.968 \cdot \exp(0.0486 \cdot T_{\max}) - 8.30)}{1 + 10.88 \cdot \exp(-0.0441 \cdot P_{\text{ann}})} \times 0.001$$

- $T_{\max}$: Maximum daily air temperature in $^\circ\text{F}$
- $P_{\text{ann}}$: Mean annual precipitation in inches
- $R_{\text{net}}$: Net rainfall exceeding $0.2\text{ in}$ ($5\text{ mm}$) threshold
- **Index Scale:** $0$ (Saturation) to $800$ (Extreme drought).

| KBDI Range | Classification | Soil / Duff Condition | Fire Potential |
|---|---|---|---|
| 0 – 200 | **Wet / Saturated** | High soil moisture, active vegetation growth | Low |
| 200 – 400 | **Moderate / Transition** | Surface litter dry, lower organic layers moist | Moderate |
| 400 – 600 | **Dry / High Deficit** | Deep duff drying, increased fuel availability | High |
| 600 – 800 | **Severe Drought** | Complete duff desiccation, high smoldering potential | Extreme |

---

## 20. Land Cover Composition (ESA WorldCover 10m)

The **ESA WorldCover 2021 v200** dataset provides 10 m global land cover with 11 discrete classes:
- **Spatial Extraction:** Zonal histogram within 5 km buffer of each priority cluster.
- **Ecosystem Impact:** Direct geometric intersection with Phase 2 vector burned perimeters to calculate exact hectares burned by land cover class (e.g. Tree cover, Shrubland, Cropland, Wetlands).

---

## 21. Peatland Spatial Association (Global Peatland Map 2.0)

Tropical peatlands in Kalimantan (peat domes in Central, West, and East Kalimantan) store gigatons of organic carbon.
- **Peatland Identification:** Global Peatland Map 2.0 at 1 km resolution.
- **Zonal Association:** Percentage of cluster buffer and percentage of delineated burned area falling on peatland soils.
- **Operational Relevance:** Fires in peatland areas exhibit persistent subsurface smoldering combustion that is resistant to surface suppression and produces massive particulate emissions.

---

# Phase 4 Methodology — Fire Intelligence & Risk Modeling

---

## 22. Atmospheric Smoke & Aerosol Dispersion (Sentinel-5P TROPOMI)

The **Tropospheric Monitoring Instrument (TROPOMI)** on board the Copernicus Sentinel-5P satellite provides daily global atmospheric composition measurements at ~1.1 km resolution.

### 22.1 UV Absorbing Aerosol Index (AAI)
The UV Aerosol Index ($AAI$) detects elevated absorbing aerosols (black carbon, organic carbon smoke particles, and dust) in the atmosphere based on spectral contrast in Rayleigh scattering between 354 nm and 388 nm:

$$AAI = -100 \cdot \left[ \log_{10} \left( \frac{R_{354}^{\text{meas}}}{R_{388}^{\text{meas}}} \right) - \log_{10} \left( \frac{R_{354}^{\text{sim}}}{R_{388}^{\text{sim}}} \right) \right]$$

- **$AAI < 0.0$:** Cloud-free, clean marine/continental atmosphere.
- **$0.0 \le AAI < 1.0$:** Background atmospheric aerosols.
- **$1.0 \le AAI < 2.0$:** Moderate smoke plume / aerosol layer.
- **$2.0 \le AAI < 3.0$:** Dense biomass burning smoke.
- **$AAI \ge 3.0$:** Hazardous / intense smoke plume.

### 22.2 Carbon Monoxide (CO Column)
Total column CO number density ($\text{mol/m}^2$) measures primary trace gas emissions from incomplete biomass combustion, providing a chemically conservative tracer of fire plume transport.

---

## 23. Active-Fire Persistence & Trajectory Modeling

### 23.1 FRP Temporal Dynamics & Trend
For each active cluster with multi-temporal detections over the 7-day window, the FRP rate of change is modeled:

$$\frac{\Delta \text{FRP}}{\Delta t} = \frac{\text{FRP}_{\text{latest}} - \text{FRP}_{\text{initial}}}{t_{\text{latest}} - t_{\text{initial}}}$$

- $\Delta \text{FRP}/\Delta t > 0$: Intensifying fire activity.
- $\Delta \text{FRP}/\Delta t \approx 0$: Steady / smoldering fire.
- $\Delta \text{FRP}/\Delta t < 0$: Decaying / suppressed thermal activity.

### 23.2 Centroid Displacement & Trajectory Vector
The dynamic expansion or migration of active fire fronts is tracked by computing the displacement vector between initial centroid $(x_0, y_0)$ and latest centroid $(x_t, y_t)$:

$$\text{Distance} = \text{Haversine}(x_0, y_0, x_t, y_t)$$
$$\text{Heading} = \left( \text{atan2}(\sin \Delta \lambda \cos \phi_t, \cos \phi_0 \sin \phi_t - \sin \phi_0 \cos \phi_t \cos \Delta \lambda) \times \frac{180}{\pi} \right) \pmod{360}^\circ$$
$$\text{Propagation Speed} = \frac{\text{Distance}}{t_{\text{span}}\text{ (days)}}$$

### 23.3 Persistence Probability Modeling
The probability $P_{\text{persist}}$ of a cluster continuing to burn over 24h and 48h horizons is formulated using logistic combination:

$$P_{\text{persist}} = \frac{1}{1 + \exp\left( -(\beta_0 + \beta_1 \tilde{N}_{\text{det}} + \beta_2 \widetilde{\text{FRP}}_{\text{max}} + \beta_3 \widetilde{\text{KBDI}} + \beta_4 F_{\text{peat}} + \beta_5 \Delta \widetilde{\text{FRP}}) \right)}$$

Where weights reflect empirical fire persistence drivers (soil dryness, peat fuel, and active energy).

---

## 24. Kalimantan Fire Susceptibility Index (KFSI)

The **Kalimantan Fire Susceptibility Index (KFSI)** is a spatially explicit, continuous (0–1) landscape hazard index computed via Multi-Criteria Decision Analysis (Weighted Linear Combination):

$$\text{KFSI}(x, y) = \sum_{i=1}^{5} w_i \cdot F_i(x, y)$$

| Factor | Criterion Layer | Source | Weight ($w_i$) | Rationale |
|---|---|---|---|---|
| $F_{\text{dry}}$ | **Moisture Deficit** | KBDI + 30d CHIRPS Anomaly | **0.30** | Desiccated fuels ignite and propagate rapidly |
| $F_{\text{fuel}}$ | **Fuel Combustibility** | ESA WorldCover 10m | **0.25** | Shrubland/grassland/degraded forest combustibility |
| $F_{\text{peat}}$ | **Peat Vulnerability** | Global Peatland Map 2.0 | **0.20** | Deep organic fuel store with extreme persistence |
| $F_{\text{hist}}$ | **Historical Fire Density**| VIIRS Active Fire Kernel Density | **0.15** | Spatial recurrence and human ignition proximity |
| $F_{\text{topo}}$ | **Topographic Slope** | NASA SRTM 30m Slope | **0.10** | Flame preheating on steep slopes vs flat peatlands |

### KFSI Hazard Classification:
- **Very Low:** $\text{KFSI} < 0.20$
- **Low:** $0.20 \le \text{KFSI} < 0.40$
- **Moderate:** $0.40 \le \text{KFSI} < 0.60$
- **High:** $0.60 \le \text{KFSI} < 0.80$
- **Very High:** $\text{KFSI} \ge 0.80$

---

## 25. Composite Risk Score & Early Warning Levels

To support actionable response, an integrated **Composite Risk Score (0–100)** is computed for each priority cluster:

$$\text{Risk Score} = 100 \times \left( 0.25 \cdot \tilde{A}_{\text{burned}} + 0.25 \cdot \widetilde{\text{KBDI}} + 0.20 \cdot P_{\text{persist}} + 0.15 \cdot \widetilde{\text{AAI}} + 0.15 \cdot \widetilde{\text{KFSI}} \right)$$

### Early Warning Decision Matrix:
| Risk Score | Persistence | Peat Association | Early Warning Level | Recommended Action |
|---|---|---|---|---|
| $\ge 75$ | High / Extreme | Yes | 🔴 **Level 4: Critical Emergency** | Immediate aerial water bombing & canal blocking |
| $60 – 74$ | Moderate / High | Any | 🟠 **Level 3: High Action** | Ground suppression deployment & community alert |
| $40 – 59$ | Moderate | Any | 🟡 **Level 2: Alert** | Field patrol verification & drone reconnaissance |
| $< 40$ | Low | No | 🟢 **Level 1: Monitor** | Routine satellite monitoring |


