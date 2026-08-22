# Kalimantan Fire Situation Monitor — Phase 1, 2, 3 & 4

[![Platform: Google Colab](https://img.shields.io/badge/Platform-Google%20Colab-orange.svg)](https://colab.research.google.com/)
[![Engine: Google Earth Engine](https://img.shields.io/badge/Engine-Google%20Earth%20Engine-blue.svg)](https://earthengine.google.com/)
[![Data: NASA FIRMS VIIRS](https://img.shields.io/badge/Data-NASA%20FIRMS%20VIIRS%20375m-red.svg)](https://firms.modaps.eosdis.nasa.gov/)
[![Data: Copernicus Sentinel-2](https://img.shields.io/badge/Data-Copernicus%20Sentinel--2%20L2A-green.svg)](https://sentinels.copernicus.eu/)
[![Data: Copernicus Sentinel-5P](https://img.shields.io/badge/Data-Copernicus%20Sentinel--5P%20TROPOMI-purple.svg)](https://sentinels.copernicus.eu/)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Docs: CC BY 4.0](https://img.shields.io/badge/Docs-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Author: Singgih Hamdani](https://img.shields.io/badge/Author-Singgih%20Hamdani-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/singgihhamdani/)

> **A reproducible, validated geospatial analysis pipeline for monitoring active-fire detections, assessing burned-area severity, contextualizing fire weather, and modeling fire intelligence and risk across Indonesian Kalimantan.**

---

## 📚 Quick Documentation Links

| Document | Language / Audience | Description |
|---|:---:|---|
| **[📖 Ringkasan Temuan untuk Awam](RINGKASAN_TEMUAN_AWAM.md)** | 🇮🇩 Bahasa Indonesia (Umum) | Penjelasan populer temuan Phase 1 s/d 4 dengan analogi mudah dipahami |
| **[📰 Satellite vs Ground Exposure Findings](FINDINGS_SATELLITE_VS_MEDIA_PERSPECTIVE.md)** | 🇮🇩 / 🇬🇧 Analytical Note | Perbandingan metrik fisik satelit vs laporan dampak asap media (e.g. Palangka Raya) |
| **[📊 Situation Report: Phase 1 (Hotspots)](SITUATION_REPORT_2026-08-20.md)** | Technical Report | Rekapitulasi deteksi termal VIIRS 24h / 72h / 7d & klaster DBSCAN |
| **[🔥 Situation Report: Phase 2 (Burned Area)](SITUATION_REPORT_PHASE2_2026-08-20.md)** | Technical Report | Konfirmasi luas terbakar optis (Sentinel-2/Landsat dNBR) & tingkat keparahan |
| **[🌧️ Situation Report: Phase 3 (Fire Weather)](SITUATION_REPORT_PHASE3_2026-08-20.md)** | Technical Report | Konteks biofisik, defisit CHIRPS 30d/90d, KBDI, dan tutupan gambut |
| **[🧠 Situation Report: Phase 4 (Fire Intelligence)](SITUATION_REPORT_PHASE4_2026-08-20.md)** | Technical Report | Prakiraan persistensi, trajektori, model KFSI, dan buletin peringatan dini |
| **[🌱 Situation Report: Phase 4B (Peat Ecohydrology & PFVI)](SITUATION_REPORT_PHASE4B_2026-08-20.md)** | Technical Report | Adaptasi *PeatFR* (Mahdiyasa et al. 2025), retensi van Genuchten & perbandingan KBDI vs PFVI |
| **[🔬 Technical Methodology](METHODOLOGY.md)** | Scientific Documentation | Landasan matematis, spektral NBR/dNBR, KBDI, KFSI, dan PFVI |
| **[📑 Data Dictionary](DATA_DICTIONARY.md)** | Developer Reference | Skema data lengkap, nama atribut layer, dan struktur folder ekspor |
| **[🧪 Validation Report](VALIDATION_REPORT.md)** | Quality Assurance | Matriks pengujian otomatis dan hasil uji integritas sistem |
| **[📋 Product Requirements Document](PRD.md)** | Specification | Spesifikasi kebutuhan teknis dan batasan sistem (Phase 1–4B) |

---

## 1. Project Overview

The **Kalimantan Fire Situation Monitor** is a Google Earth Engine & Google Colab geospatial system designed to provide transparent, scientifically grounded situational awareness of fire activity across all five provinces of Indonesian Kalimantan:
- **Kalimantan Barat** (West Kalimantan)
- **Kalimantan Tengah** (Central Kalimantan)
- **Kalimantan Selatan** (South Kalimantan)
- **Kalimantan Timur** (East Kalimantan)
- **Kalimantan Utara** (North Kalimantan)

---

## 2. Multi-Phase Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 1: Active-Fire Detection Snapshot (kalimantan_fire_monitor.ipynb) │
│ - Ingest VIIRS SNPP & NOAA-20 375m NRT thermal anomalies                │
│ - Filter by confidence (Operational >= 1) & spatial boundaries          │
│ - Run DBSCAN clustering (eps=2.0 km, min_samples=3)                     │
│ - Output: cluster_statistics_7d.csv & kalimantan_clusters_7d.geojson    │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 2: Burned-Area Confirmation (kalimantan_burned_area.ipynb)        │
│ - Compute Cluster Priority Scores -> Select Top 20 priority clusters    │
│ - Sentinel-2 L2A (Primary) / Landsat 8/9 L2 (Fallback) optical queries  │
│ - Cloud & shadow masking -> 30d/60d pre-fire and post-fire composites   │
│ - Compute NBR & dNBR -> Classify into 5 USGS burn severity classes      │
│ - Delineate burned perimeters (GeoJSON) & export dNBR rasters (GeoTIFF) │
│ - Cross-validate with MODIS MCD64A1 Monthly Burned Area product         │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 3: Environmental Context (kalimantan_fire_weather.ipynb)          │
│ - Ingest Phase 1 & 2 priority clusters                                  │
│ - CHIRPS 30d & 90d precipitation accumulation + historical anomalies    │
│ - ERA5-Land ambient temperature, wind vectors & MODIS LST               │
│ - Keetch-Byram Drought Index (KBDI) soil moisture deficit calculation   │
│ - ESA WorldCover 10m & Global Peatland Map 2.0 spatial overlay          │
│ - Output: Integrated environmental summary & provincial reports         │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
         ┌───────────────────────────┴───────────────────────────┐
         ▼                                                       ▼
┌───────────────────────────────────────────┐ ┌───────────────────────────────────────────┐
│ PHASE 4: Fire Intelligence & Risk         │ │ PHASE 4B: Peat Ecohydrology & PFVI        │
│ (kalimantan_fire_intelligence.ipynb)      │ │ (kalimantan_peat_vulnerability.ipynb)     │
│ - Ingest Phase 1, 2, & 3 outputs          │ │ - Ingest Phase 1, 2, & 3 outputs          │
│ - Sentinel-5P UV Aerosol Index & CO       │ │ - Adapt PeatFR (Mahdiyasa et al. 2025)    │
│ - FRP trends & centroid displacement      │ │ - van Genuchten soil water retention      │
│ - 24h & 48h active persistence probability│ │ - Nelder-Mead parameter calibration       │
│ - Kalimantan Fire Susceptibility (KFSI)   │ │ - 7-day ARIMA Box-Cox PFVI forecasting    │
│ - Level 1–4 Early Warning Bulletin        │ │ - Head-to-Head Benchmarking: KBDI vs PFVI │
└───────────────────────────────────────────┘ └───────────────────────────────────────────┘
```

---

## 3. Key Datasets & Sources

| Dataset | Provider / Source | Earth Engine Collection ID | Resolution / Band | Phase |
|---|---|---|---|---|
| **VIIRS Suomi NPP Active Fire** | NASA LANCE / FIRMS | `NASA/LANCE/SNPP_VIIRS/C2` | 375 m, `Bright_ti4`, `frp` | 1 |
| **VIIRS NOAA-20 Active Fire** | NASA LANCE / FIRMS | `NASA/LANCE/NOAA20_VIIRS/C2` | 375 m, `Bright_ti4`, `frp` | 1 |
| **Sentinel-2 L2A Harmonized** | ESA / Copernicus | `COPERNICUS/S2_SR_HARMONIZED` | 20 m, `B8A`, `B12`, `QA60`, `SCL` | 2 |
| **Landsat 8/9 Collection 2 L2** | USGS / NASA | `LANDSAT/LC09/C02/T1_L2`, `LC08` | 30 m, `SR_B5`, `SR_B7`, `QA_PIXEL`| 2 |
| **MODIS Monthly Burned Area** | NASA LP DAAC | `MODIS/061/MCD64A1` | 500 m, `BurnDate` | 2 |
| **CHIRPS Daily Precipitation** | UCSB Climate Hazards Center | `UCSB-CHG/CHIRPS/DAILY` | 0.05° (~5.5 km), `precipitation` | 3 |
| **ERA5-Land Daily Reanalysis** | ECMWF / Copernicus | `ECMWF/ERA5_LAND/DAILY_AGGR` | 0.1° (~11 km), `temperature_2m`, wind, precip | 3 |
| **MODIS Land Surface Temp** | NASA LP DAAC | `MODIS/061/MOD11A1` | 1 km, `LST_Day_1km`, `LST_Night_1km` | 3 |
| **ESA WorldCover 10m v200** | ESA / VITO | `ESA/WorldCover/v200` | 10 m, `Map` (11 classes) | 3, 4 |
| **Global Peatland Map 2.0** | GEE Community Catalog | `projects/sat-io/open-datasets/GLOBAL_PEATLAND_MAP` | 1 km, peatland classification | 3, 4 |
| **Sentinel-5P TROPOMI Aerosol**| ESA / Copernicus | `COPERNICUS/S5P/OFFL/L3_AER_AI` | 0.01° (~1.1 km), `absorbing_aerosol_index` | 4 |
| **Sentinel-5P TROPOMI CO** | ESA / Copernicus | `COPERNICUS/S5P/OFFL/L3_CO` | 0.01° (~1.1 km), `CO_column_number_density` | 4 |
| **NASA SRTM DEM** | USGS / NASA | `USGS/SRTMGL1_003` | 30 m, `elevation`, `slope` | 4 |
| **Administrative Boundaries** | geoBoundaries v6.0.0 | `WM/geoLab/geoBoundaries/600/ADM1`, `ADM2` | ADM1 (5 Prov) / ADM2 (56 Reg) | 1, 2, 3, 4 |

---

## 4. Mandatory Terminology & Scientific Principles

| Mandatory Term | Prohibited Term | Rationale |
|---|---|---|
| **active-fire detection** | confirmed fire | Satellite thermal anomalies are not field-verified |
| **thermal anomaly** | forest fire | Detection may arise from non-forest heat sources |
| **hotspot detection** | wildfire | Cause and fuel type are unverified |
| **active-fire detection cluster** | fire cluster / fire event | A cluster is a spatial concentration of pixels |
| **detection count** | fire count | One physical fire can produce multiple detections |
| **burned-area evidence** | confirmed burned area (without optical change) | Requires multispectral change detection |
| **burn severity** | fire damage level | Follows USGS/Key & Benson (2006) standards |
| **Cluster Priority Score** | fire-risk score / risk index (in Phase 2) | Scoring is strictly for optical analysis prioritization |
| **precipitation deficit / anomaly** | drought disaster | Requires defined climatological threshold |
| **environmental context** | fire cause / arson proof | Remote sensing shows spatial association, not causality |
| **fire susceptibility / danger index** | absolute fire prediction | Models biophysical predisposition, not deterministic fate |

---

## 5. Repository File Structure

```
kalimantanfire/
├── .gitignore                          # Git ignore rules for Python, Colab, & large files
├── LICENSE                             # Apache License 2.0
├── requirements.txt                    # Python environment dependencies
├── PRD.md                              # Product Requirements Document (Phase 1, 2, 3 & 4)
├── README.md                           # Repository overview and guide (this file)
├── DATA_DICTIONARY.md                  # Detailed schema of all fields, bands, and exports
├── METHODOLOGY.md                      # Remote sensing, NBR/dNBR, and spatial methodology
├── VALIDATION_REPORT.md                # Validation test matrix (Phase 1, 2, 3 & 4)
│
├── RINGKASAN_TEMUAN_AWAM.md            # Plain-language explanation for laypeople (Indonesian)
├── FINDINGS_SATELLITE_VS_MEDIA_PERSPECTIVE.md # Comparative analytical technical note
├── SITUATION_REPORT_2026-08-20.md      # Phase 1 situation report (Active Fire Snapshot)
├── SITUATION_REPORT_PHASE2_2026-08-20.md  # Phase 2 situation report (Burned Area & Severity)
├── SITUATION_REPORT_PHASE3_2026-08-20.md  # Phase 3 situation report (Fire Weather & Peatland)
├── SITUATION_REPORT_PHASE4_2026-08-20.md  # Phase 4 situation report (Intelligence & Risk)
├── BURNED_AREA_REPORT_2026-08-20.md    # Phase 2 technical burned-area assessment
│
├── kalimantan_fire_monitor.ipynb       # Phase 1 Colab notebook (Active-Fire Snapshot)
├── kalimantan_burned_area.ipynb        # Phase 2 Colab notebook (Burned-Area Confirmation)
├── kalimantan_fire_weather.ipynb       # Phase 3 Colab notebook (Environmental Context)
├── kalimantan_fire_intelligence.ipynb  # Phase 4 Colab notebook (Fire Intelligence & Risk)
│
├── references/                         # Reference academic papers & publications
│   └── 1-s2.0-S2666592126000727-main.pdf
│
└── export/                             # Exported geospatial outputs & tabular datasets
    ├── 2026-08-20/                     # Phase 1 exports (raw, processed, reports)
    ├── phase2/                         # Phase 2 exports (burned_area, dnbr_rasters, clusters)
    ├── phase3/                         # Phase 3 exports (precipitation, weather, peatland)
    └── phase4/                         # Phase 4 exports (smoke, persistence, risk, reports)
```

---

## 6. How to Run the Analysis

### Option A: Running in Google Colab (Recommended)
1. Open [Google Colab](https://colab.research.google.com/).
2. Upload the notebook for the desired phase:
   - `kalimantan_fire_monitor.ipynb` (Phase 1)
   - `kalimantan_burned_area.ipynb` (Phase 2)
   - `kalimantan_fire_weather.ipynb` (Phase 3)
   - `kalimantan_fire_intelligence.ipynb` (Phase 4)
3. Authenticate with your Google Earth Engine account (`ee.Authenticate()`).
4. Set your GEE Project ID in Section 03 (e.g. `EE_PROJECT_ID = 'your-project-id'`).
5. Run all cells (`Runtime -> Run all`).

### Option B: Local Jupyter Environment
```bash
# Clone the repository
git clone https://github.com/your-username/kalimantanfire.git
cd kalimantanfire

# Create and activate virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter Lab
jupyter lab
```

---

## 7. Future Roadmap & Scientific Inspiration (Phase 5)

As part of the continuous evolution of the **Kalimantan Fire Situation Monitor**, future development (**Phase 5**) aims to advance the intelligence pipeline from multi-criteria spatial danger indices (KFSI) toward **calibrated deep learning spatio-temporal forecasting** and an **operational interactive web dashboard**.

### 🔬 Scientific Benchmark & Inspiration
This roadmap draws direct inspiration from recent cutting-edge research in tropical peatland ecohydrology and AI-driven hazard modeling:
- **Mahdiyasa, A. W., Chaim, J. F., Pasaribu, U. S., Sumarga, E., Muljadi, B. P., & Chrysanti, A. (2026).** *A Calibrated Spatio-Temporal Machine Learning Model for Tropical Peatland Fire Risk*. **Natural Hazards Research** (Elsevier). [DOI: 10.1016/j.nhres.2026.07.007](https://doi.org/10.1016/j.nhres.2026.07.007) *(archived under [`references/`](references/))*.

> *Special appreciation to **Prof. Bagus P. Muljadi** (University of Nottingham) for his insightful scientific discourses and public advocacy on applying mathematical physics, porous media mechanics, and machine learning to tackle Indonesia's complex tropical peatland and wildfire challenges.*

### 🚀 Planned Milestones for Phase 5:
1. **CNN-ConvLSTM Deep Learning Architecture:** Integrating 2D convolutional spatial feature extractors with Convolutional LSTM layers to capture dynamic time-space dependencies in peat drying and fire ignition.
2. **Isotonic Probabilistic Calibration:** Implementing non-parametric monotonic probability calibration to ensure forecasted probabilities strictly align with empirical fire event frequencies (minimizing Brier score uncertainty).
3. **Automated Operational Web Platform:** Transforming the pipeline into an automated, interactive web dashboard providing 3-day and 10-day predictive alerts for field task forces (BPBD, Manggala Agni, TNI/Polri) and local communities.

---

## 👤 Author & Maintainer

**Singgih Hamdani**
- 💼 LinkedIn: [Singgih Hamdani](https://www.linkedin.com/in/singgihhamdani/)
- 🐙 GitHub: [@singgihhamdani](https://github.com/singgihhamdani)

---

## 8. License & Attribution

- **Code:** Licensed under [Apache License 2.0](LICENSE). Copyright © 2026 Singgih Hamdani.
- **Documentation & Data Derivatives:** Licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).
- **Data Attribution:** NASA FIRMS VIIRS & MODIS courtesy of NASA LANCE / EOSDIS; Copernicus Sentinel-2 & Sentinel-5P data courtesy of ESA / European Commission; USGS Landsat & SRTM courtesy of USGS / NASA; CHIRPS courtesy of UCSB Climate Hazards Center; ERA5-Land courtesy of ECMWF / Copernicus.


