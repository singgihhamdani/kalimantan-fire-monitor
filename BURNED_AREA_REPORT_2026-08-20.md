# Laporan Situasi Phase 2: Konfirmasi Area Terbakar & Penilaian Tingkat Keparahan
**Kalimantan Fire Situation Monitor — Phase 2**  
*Tanggal Analisis: 20 Agustus 2026 | Sensor: Copernicus Sentinel-2 L2A & USGS Landsat 8/9 L2*

---

## 1. Ringkasan Eksekutif

Berdasarkan hasil analisis konfirmasi optis multispektral terhadap **10 klaster titik panas prioritas teratas** dari Phase 1:
- **Total Luas Terbakar Terkonfirmasi:** **2.684,27 hektare**
- **Klaster Terkonfirmasi Terbakar:** **6 klaster (60%)**
- **Klaster Berstatus Siaga (Api Masih Aktif):** **4 klaster (40%)**
- **Tingkat Keparahan Tinggi (*High Severity*):** **142,54 hektare (5,3%)** kanopi hutan habis terbakar total.
- **Episentrum Utama:** **Kabupaten Berau, Kalimantan Timur (Klaster #327)** dengan luas terbakar **1.727,77 ha** dan menyumbang **97,5% dari seluruh kerusakan tingkat tinggi** di Kalimantan.

---

## 2. Rekapitulasi Berdasarkan Provinsi

| Provinsi | Klaster Dianalisis | Total Terbakar (ha) | Keparahan Tinggi (ha) | Sedang-Tinggi (ha) | Sedang-Rendah (ha) | Rendah (ha) | Kontribusi (%) |
|---|---|---|---|---|---|---|---|
| **Kalimantan Timur** | 2 | **1.875,32** | 139,04 | 306,08 | 372,89 | 1.057,31 | **69,9%** |
| **Kalimantan Barat** | 5 | **424,83** | 2,23 | 12,90 | 38,16 | 371,54 | **15,8%** |
| **Kalimantan Tengah** | 1 | **294,05** | 1,27 | 15,17 | 35,49 | 242,12 | **11,0%** |
| **Kalimantan Utara** | 2 | **90,07** | 0,00 | 0,00 | 0,27 | 89,80 | **3,4%** |
| **Kalimantan Selatan** | 0 | 0,00 | 0,00 | 0,00 | 0,00 | 0,00 | 0,0% |
| **TOTAL** | **10** | **2.684,27** | **142,54** | **334,15** | **446,81** | **1.760,77** | **100,0%** |

---

## 3. Rincian per Klaster Prioritas

| Peringkat | ID Klaster | Wilayah (Kabupaten, Provinsi) | Sensor Digunakan | Total Terbakar (ha) | Keparahan Tinggi (ha) | Status Analisis |
|---|---|---|---|---|---|---|
| **#1** | **#327** | **Berau, Kalimantan Timur** | Sentinel-2 | **1.727,77** | **139,04** | ✅ Terkonfirmasi (Episentrum) |
| **#2** | **#318** | Landak, Kalimantan Barat | Sentinel-2 (Pending) | 0,00 | 0,00 | ⏳ Menunggu Citra Pasca-Api |
| **#3** | **#299** | Sekadau, Kalimantan Barat | Sentinel-2 (Pending) | 0,00 | 0,00 | ⏳ Menunggu Citra Pasca-Api |
| **#4** | **#256** | Bulungan, Kalimantan Utara | Sentinel-2 (Pending) | 0,00 | 0,00 | ⏳ Menunggu Citra Pasca-Api |
| **#5** | **#243** | Sanggau, Kalimantan Barat | Sentinel-2 (Pending) | 0,00 | 0,00 | ⏳ Menunggu Citra Pasca-Api |
| **#6** | **#298** | **Kapuas Hulu, Kalimantan Barat** | Sentinel-2 | **111,86** | **0,28** | ✅ Terkonfirmasi |
| **#7** | **#317** | **Malinau, Kalimantan Utara** | Landsat-8/9 | **90,07** | **0,00** | ✅ Terkonfirmasi (*Fallback*) |
| **#8** | **#58** | **Ketapang, Kalimantan Barat** | Sentinel-2 | **312,97** | **1,95** | ✅ Terkonfirmasi |
| **#9** | **#314** | **Kutai Timur, Kalimantan Timur** | Sentinel-2 | **147,55** | **0,00** | ✅ Terkonfirmasi |
| **#10** | **#16** | **Kotawaringin Timur, Kalimantan Tengah** | Sentinel-2 | **294,05** | **1,27** | ✅ Terkonfirmasi |

---

## 4. Temuan Kunci & Pembahasan Ilmiah

1. **Episentrum Kerusakan Masif di Berau (Klaster #327):**  
   Klaster #327 di Berau terkonfirmasi mengalami kebakaran seluas 1.727,8 ha. Sebanyak **139 ha (8,0%)** mengalami keparahan tingkat tinggi (*High Severity*) di mana lapisan kanopi pohon musnah total. Nilai maksimum dNBR di area ini mencapai **1,477**, menandakan hilangnya biomassa secara drastis.

2. **Keberhasilan Mekanisme *Fallback* Sensor:**  
   Pada Klaster #317 di Kabupaten Malinau (Kaltara), saat citra Sentinel-2 terkontaminasi awan tebal, sistem secara otomatis beralih menggunakan **Landsat 8/9** dan berhasil mengonfirmasi 90,07 ha area terbakar.

3. **Status Klaster Aktif (Pending Post-Fire):**  
   Empat klaster di Landak, Sekadau, Bulungan, dan Sanggau berstatus *Pending Post-Fire* karena titik api baru aktif terdeteksi pada 20 Agustus 2026. Area ini memerlukan pemantauan lanjutan pada siklus orbit Sentinel-2 berikutnya (5 hari ke depan).

---

## 5. Struktur Berkas Hasil Ekspor (`export/phase2/`)

```
export/phase2/
├── clusters/
│   └── top20_priority_clusters.geojson     # 10 sentroid klaster prioritas beserta atributnya
├── burned_area/
│   ├── cluster_016_burned_perimeter.geojson # 965 poligon area terbakar Kotim
│   ├── cluster_058_burned_perimeter.geojson # 2.259 poligon area terbakar Ketapang
│   ├── cluster_298_burned_perimeter.geojson # 331 poligon area terbakar Kapuas Hulu
│   ├── cluster_314_burned_perimeter.geojson # 426 poligon area terbakar Kutim
│   ├── cluster_317_burned_perimeter.geojson # 151 poligon area terbakar Malinau
│   └── cluster_327_burned_perimeter.geojson # 1.797 poligon area terbakar Berau
├── dnbr_rasters/
│   └── cluster_XXX_dnbr_url.txt            # Tautan unduh GeoTIFF dNBR resolusi 20-30m
├── reports/
│   ├── top20_burned_area_summary.csv       # Tabel ringkasan lengkap 10 klaster
│   └── burn_severity_by_province.csv       # Rekapitulasi luas terbakar per provinsi
└── metadata/
    └── phase2_analysis_metadata.json       # Metadata eksekusi, sensor, dan validasi (10/10 PASS)
```
