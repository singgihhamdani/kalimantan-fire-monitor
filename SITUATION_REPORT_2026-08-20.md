# Laporan Situasi Titik Panas & Deteksi Kebakaran Aktif Kalimantan
## Kalimantan Fire Situation Report — 20 Agustus 2026

| Parameter Analisis | Informasi |
|---|---|
| **Waktu Analisis (UTC)** | 20 Agustus 2026, 16:47:04 UTC (23:47:04 WIB) |
| **Sensor Satelit** | NASA FIRMS VIIRS 375 m (Suomi NPP & NOAA-20) |
| **Google Earth Engine Project** | `riset-banjarnegara` |
| **Koleksi Data** | `NASA/LANCE/SNPP_VIIRS/C2`, `NASA/LANCE/NOAA20_VIIRS/C2` |
| **Batas Administrasi** | geoBoundaries ADM1 & ADM2 (Indonesia) |
| **Tingkat Keyakinan (Confidence)** | Operational (Nominal & High Confidence, $\ge 1$) |
| **Folder Data Ekspor** | `U:\Project\kalimantanfire\export\2026-08-20\` |

---

## 1. Ringkasan Eksekutif (Executive Summary)

Berdasarkan pengolahan data satelit penginderaan jauh VIIRS 375m menggunakan pipeline *Kalimantan Fire Situation Monitor*, wilayah Kalimantan pada periode pertengahan Agustus 2026 berada dalam **fase aktivitas termal tinggi (puncak musim kemarau/kebakaran)**:

1. **Skala Deteksi Total (All Raw Detections):**
   - **24 Jam Terakhir:** Terdeteksi **19.333 deteksi anomali termal** di seluruh daratan Kalimantan.
   - **72 Jam Terakhir:** Terakumulasi **86.673 deteksi anomali termal**.
   - **7 Hari Terakhir:** Terakumulasi **153.251 deteksi anomali termal**.
2. **Konsentrasi Spasial Utama (Hotspot Epicenter):**
   - **Dalam 24 Jam Terakhir:** Terjadi lonjakan intensitas di **Kalimantan Barat** (2.553 deteksi operasional) dan **Kalimantan Timur** (2.009 deteksi operasional).
   - **Kabupaten Paling Terdampak:** **Kabupaten Berau** (Kalimantan Timur) menjadi wilayah paling kritis dengan **1.228 deteksi dalam 24 jam** dan **927 deteksi operasional dalam 7 hari**. Diikuti oleh **Kutai Timur** (753 deteksi 24h) dan **Sanggau** (701 deteksi 24h).
3. **Intensitas Energi Termal (Fire Radiative Power - FRP):**
   - Nilai FRP tertinggi yang tercatat mencapai **139,53 MW** di Kabupaten Berau.
   - Total FRP dalam 24 jam terakhir didominasi oleh **Kalimantan Barat (27.849,5 MW)** dan **Kalimantan Timur (15.960,0 MW)**, mengindikasikan pelepasan energi pembakaran biomassa yang sangat masif.
4. **Klaster Spasial (DBSCAN Clustering):**
   - Teridentifikasi **325 klaster spasial aktif** pada jendela 7 hari dan **334 klaster aktif** pada jendela 72 jam ($eps = 2,0\text{ km}$, $min\_samples = 3$).
   - Klaster terbesar mencakup radius sebaran hingga $10,1\text{ km} \times 13,3\text{ km}$ di wilayah Berau.

---

## 2. Struktur File Hasil Ekspor (`export/2026-08-20/`)

Arsip hasil analisis tersimpan secara terstruktur dalam 4 direktori utama:

```
export/2026-08-20/
├── metadata/
│   └── analysis_metadata.json          # Metadata lengkap waktu, parameter, versi, dan konfigurasi
├── raw/
│   ├── kalimantan_fires_all_24h.csv     # Seluruh deteksi mentah 24 jam (termasuk low confidence)
│   ├── kalimantan_fires_all_72h.csv     # Seluruh deteksi mentah 72 jam
│   └── kalimantan_fires_all_7d.csv      # Seluruh deteksi mentah 7 hari
├── processed/
│   ├── kalimantan_fires_operational_24h.csv  # Deteksi operasional 24 jam (confidence >= 1)
│   ├── kalimantan_fires_operational_72h.csv  # Deteksi operasional 72 jam + cluster_id
│   ├── kalimantan_fires_operational_7d.csv   # Deteksi operasional 7 hari + cluster_id
│   ├── kalimantan_fires_24h.geojson          # Spasial GeoJSON titik deteksi 24 jam
│   ├── kalimantan_fires_7d.geojson           # Spasial GeoJSON titik deteksi 7 hari
│   ├── kalimantan_clusters_72h.geojson       # Sentroid klaster DBSCAN 72 jam
│   ├── kalimantan_clusters_7d.geojson        # Sentroid klaster DBSCAN 7 hari
│   └── kalimantan_density_7d.geojson         # Poligon grid densitas 0.1° (330 sel aktif)
└── reports/
    ├── province_summary.csv             # Tabel rekapitulasi per provinsi
    ├── regency_summary.csv              # Tabel rekapitulasi per kabupaten/kota (48 wilayah aktif)
    ├── cluster_statistics_72h.csv       # Statistik detail 334 klaster 72 jam
    └── cluster_statistics_7d.csv        # Statistik detail 325 klaster 7 hari
```

---

## 3. Analisis Situasi per Jendela Waktu

### 3.1 Jendela 24 Jam Terakhir (Immediate Snapshot)
*Rentang Waktu: 19 Agustus 2026 16:47 UTC s/d 20 Agustus 2026 16:47 UTC*

Pada jendela 24 jam, sebanyak **4.888 deteksi operasional** dianalisis dengan tingkat keyakinan **High (23,4% / 1.144 titik)** dan **Nominal (76,6% / 3.744 titik)**.

#### Tabel Ringkasan Provinsi (24 Jam)
| Provinsi | Deteksi Operasional | Total FRP (MW) | Rata-rata FRP (MW) | FRP Maksimum (MW) |
|---|---|---|---|---|
| **Kalimantan Barat** | 2.553 | 27.849,55 | 10,91 | 88,94 |
| **Kalimantan Timur** | 2.009 | 15.960,00 | 7,94 | 60,74 |
| **Kalimantan Utara** | 288 | 2.488,00 | 8,64 | 28,11 |
| **Kalimantan Tengah** | 38 | 777,12 | 20,45 | 31,53 |
| **Kalimantan Selatan** | 0 | 0,00 | 0,00 | 0,00 |

#### Top 10 Kabupaten/Kota Paling Kritis (24 Jam)
| Peringkat | Kabupaten / Kota | Provinsi | Deteksi 24h | Titik High Confidence | Rata-rata FRP (MW) | FRP Maks (MW) |
|---|---|---|---|---|---|---|
| 1 | **Berau** | Kalimantan Timur | **1.228** | 392 | 9,31 | 60,74 |
| 2 | **Kutai Timur** | Kalimantan Timur | **753** | 74 | 5,74 | 29,26 |
| 3 | **Sanggau** | Kalimantan Barat | **701** | 200 | 7,86 | 41,93 |
| 4 | **Sekadau** | Kalimantan Barat | **458** | 101 | 10,99 | 77,58 |
| 5 | **Landak** | Kalimantan Barat | **394** | 68 | 15,51 | 88,94 |
| 6 | **Ketapang** | Kalimantan Barat | **268** | 56 | 7,40 | 37,29 |
| 7 | **Bulungan** | Kalimantan Utara | **253** | 73 | 8,57 | 19,63 |
| 8 | **Kapuas Hulu** | Kalimantan Barat | **217** | 51 | 11,34 | 50,91 |
| 9 | **Melawi** | Kalimantan Barat | **152** | 42 | 12,76 | 62,31 |
| 10 | **Sintang** | Kalimantan Barat | **141** | 50 | 11,76 | 62,31 |

---

### 3.2 Jendela 72 Jam Terakhir (Persistence & Cluster Formation)
*Rentang Waktu: 17 Agustus 2026 16:47 UTC s/d 20 Agustus 2026 16:47 UTC*

Pada jendela 72 jam, algoritma DBSCAN mengelompokkan deteksi menjadi **334 klaster aktif**.

#### Top 5 Klaster Terbesar (72 Jam)
| Cluster ID | Jumlah Titik | Provinsi Dominan | Kabupaten Dominan | Koordinat Sentroid (Lat, Lon) | Rentang Wilayah ($\Delta Lat \times \Delta Lon$) | FRP Maks (MW) |
|---|---|---|---|---|---|---|
| **#327** | **216** | Kalimantan Timur | Berau | $2,274^\circ\text{ N}, 117,902^\circ\text{ E}$ | $8,6\text{ km} \times 9,5\text{ km}$ | 28,52 |
| **#58** | **117** | Kalimantan Barat | Ketapang | $-1,938^\circ\text{ S}, 110,248^\circ\text{ E}$ | $6,0\text{ km} \times 10,4\text{ km}$ | 10,65 |
| **#318** | **113** | Kalimantan Barat | Landak | $0,211^\circ\text{ N}, 109,794^\circ\text{ E}$ | $4,5\text{ km} \times 8,4\text{ km}$ | 88,94 |
| **#243** | **103** | Kalimantan Barat | Sanggau | $0,157^\circ\text{ N}, 110,486^\circ\text{ E}$ | $5,6\text{ km} \times 5,3\text{ km}$ | 33,26 |
| **#299** | **95** | Kalimantan Barat | Sekadau | $0,086^\circ\text{ N}, 110,951^\circ\text{ E}$ | $4,5\text{ km} \times 8,3\text{ km}$ | 77,58 |

---

### 3.3 Jendela 7 Hari Terakhir (Synoptic Pattern & Density)
*Rentang Waktu: 13 Agustus 2026 16:47 UTC s/d 20 Agustus 2026 16:47 UTC*

Analisis 7 hari memberikan gambaran sebaran titik panas regional dan pola akumulasi mingguan.

#### Tabel Perbandingan Akumulasi per Provinsi (Rekonsiliasi)
| Provinsi | Deteksi 7 Hari | Deteksi 72 Jam | Deteksi 24 Jam | Total FRP 7 Hari (MW) | FRP Maks 7 Hari (MW) |
|---|---|---|---|---|---|
| **Kalimantan Timur** | **1.577** | 1.127 | 2.009 | 10.536,95 | **139,53** |
| **Kalimantan Tengah** | **1.261** | 877 | 38 | 4.210,87 | 26,53 |
| **Kalimantan Barat** | **876** | 2.697 | 2.553 | 2.495,49 | 16,59 |
| **Kalimantan Utara** | **848** | 248 | 288 | **10.868,84** | 91,00 |
| **Kalimantan Selatan** | **397** | 6 | 0 | 1.456,97 | 32,36 |

#### Karakteristik Grid Densitas ($0,1^\circ \approx 11\text{ km}$):
- **Jumlah Sel Grid Aktif:** 330 sel.
- **Rata-rata Titik Panas per Sel Aktif:** 15,03 titik.
- **Densitas Tertinggi dalam 1 Sel:** **144 titik panas** (terletak di koridor pesisir Berau, Kalimantan Timur).

---

## 4. Temuan Spasial & Rekomendasi Lapangan

1. **Episentrum Utama Berau - Kutai Timur (Kalimantan Timur):**
   - Wilayah Berau menunjukkan konsentrasi anomali termal persisten selama 7 hari berturut-turut dengan klaster terluas (>13 km) dan daya radiasi energi tertinggi (139,5 MW). Disarankan peninjauan satelit resolusi tinggi (Sentinel-2 / PlanetScope) atau patroli darat/udara pada koordinat $2,26^\circ\text{ N}, 117,89^\circ\text{ E}$.
2. **Lonjakan Cepat Koridor Barat (Sanggau - Sekadau - Landak):**
   - Terjadi peningkatan tajam titik panas dalam 24 jam terakhir di Kalimantan Barat (lebih dari 2.500 deteksi), didominasi oleh titik berdaya tinggi (FRP > 70 MW) di Landak dan Sekadau.
3. **Penyebaran Luas di Kalimantan Tengah:**
   - Wilayah Kotawaringin Timur, Kapuas, dan Palangka Raya menunjukkan sebaran titik yang merata pada jendela 7 hari (total 1.261 titik), dengan nilai FRP rata-rata relatif rendah (~2–3 MW) yang tipikal terjadi pada pembakaran vegetasi semak atau lahan gambut awal.

---

## 5. Batasan Metodologi

1. **Deteksi Anomali Termal $\neq$ Kebakaran Hutan Terkonfirmasi:**  
   Sensor VIIRS mendeteksi kontras radiasi inframerah gelombang menengah (MIR 3,75 µm). Sumber radiasi mencakup pembakaran lahan pertanian, pembersihan lahan, titik industri/flare, dan kebakaran vegetasi alami.
2. **Pengaruh Tutupan Awan & Asap Tebal:**  
   Awan konvektif tebal atau asap pekat dapat menutupi radiasi termal dari permukaan (*cloud obscuration*), sehingga jumlah titik deteksi dapat lebih rendah daripada kejadian fisik sebenarnya.
3. **Multiplicity of Detections:**  
   Satu kejadian kebakaran fisik berukuran besar dapat terdeteksi sebagai beberapa piksel 375 m yang bersebelahan oleh satelit Suomi NPP dan NOAA-20.
