# LAPORAN SITUASI PHASE 4 — INTELIJEN KEBAKARAN & PEMODELAN RISIKO
## Kalimantan Fire Situation Monitor
**Tanggal Analisis:** 20 Agustus 2026 (19:45 UTC)  
**Cakupan Wilayah:** Pulau Kalimantan (10 Klaster Prioritas Terpilih)  
**Status Ekspor:** Seluruh artefak Phase 4 berhasil dihasilkan (CSV, GeoJSON, HTML, JSON)  
**Status Validasi:** 10/10 pengujian otomatis LOLOS ✓

---

## 1. Ringkasan Eksekutif

Phase 4 (**Fire Intelligence & Risk Modeling**) mentransformasikan seluruh hasil observasi titik panas (Phase 1), bukti luas terbakar optis (Phase 2), dan kondisi cuaca/lahan gambut (Phase 3) menjadi **intelijen prediktif, pemodelan bahaya spasial (KFSI), dan buletin peringatan dini operasional**:

1. 🔴 **1 Klaster Masuk Kategori Peringatan Darurat Kritis (Level 4: Critical Emergency):**
   * **Klaster #327 (Berau, Kalimantan Timur)** memperoleh **Skor Risiko Komposit tertinggi yaitu 77,1 / 100**. Klaster ini memiliki luas terbakar terkonfirmasi 1.727,77 ha, skor kekeringan tanah tinggi (KBDI 521,4), 587,44 ha terbakar di atas lahan gambut, probabilitas persistensi 48 jam sebesar **88,3% (Extreme)**, dan tingkat kerentanan lanskap KFSI **Very High (0,964)**.

2. 🟡 **6 Klaster Masuk Kategori Waspada / Siaga (Level 2: Alert):**
   * Klaster #318 (Landak - Skor 51,6)
   * Klaster #58 (Ketapang - Skor 49,4)
   * Klaster #16 (Kotawaringin Timur - Skor 48,9)
   * Klaster #314 (Kutai Timur - Skor 44,6)
   * Klaster #299 (Sekadau - Skor 42,8)
   * Klaster #243 (Sanggau - Skor 42,2)
   * *Catatan khusus:* Klaster #58 (Ketapang) dan #16 (Kotawaringin Timur) memiliki konsentrasi gambut sangat tinggi (72,5%) dengan probabilitas persistensi aktif 48 jam > 74%.

3. 🟢 **3 Klaster Masuk Kategori Pemantauan Rutin (Level 1: Monitor):**
   * Klaster #298 (Kapuas Hulu - Skor 39,1)
   * Klaster #256 (Bulungan - Skor 36,7)
   * Klaster #317 (Malinau - Skor 23,5 — kondisi tanah basah KBDI 199,7 dan persistensi rendah 22,7%).

---

## 2. Buletin Peringatan Dini Operasional (*Early Warning Bulletin*)

| Peringkat | Klaster | Provinsi | Kabupaten | Skor Risiko (0–100) | Tingkat Peringatan Dini | Ancaman Utama | Luas Terbakar (ha) | Prob. Persistensi 48h | Kelas KFSI |
|---|---|---|---|---|---|---|---|---|---|
| **1** | **#327** | Kalimantan Timur | **Berau** | **77,1** | 🔴 **Level 4: Critical Emergency** | Kebakaran Gambut + Defisit Ekstrem | 1.727,77 | **88,3% (Extreme)** | **Very High** |
| 2 | #318 | Kalimantan Barat | Landak | 51,6 | 🟡 Level 2: Alert | Potensi Perluasan Titik Panas | 0,00* | 87,8% (Extreme) | Very High |
| 3 | #58 | Kalimantan Barat | Ketapang | 49,4 | 🟡 Level 2: Alert | Potensi Perluasan Titik Panas | 312,97 | 74,5% (High) | Very High |
| 4 | #16 | Kalimantan Tengah | Kotawaringin Timur | 48,9 | 🟡 Level 2: Alert | Potensi Perluasan Titik Panas | 294,05 | 74,1% (High) | Very High |
| 5 | #314 | Kalimantan Timur | Kutai Timur | 44,6 | 🟡 Level 2: Alert | Potensi Perluasan Titik Panas | 147,55 | 64,0% (High) | High |
| 6 | #299 | Kalimantan Barat | Sekadau | 42,8 | 🟡 Level 2: Alert | Potensi Perluasan Titik Panas | 0,00* | 64,7% (High) | High |
| 7 | #243 | Kalimantan Barat | Sanggau | 42,2 | 🟡 Level 2: Alert | Potensi Perluasan Titik Panas | 0,00* | 63,6% (High) | High |
| 8 | #298 | Kalimantan Barat | Kapuas Hulu | 39,1 | 🟢 Level 1: Monitor | Aktivitas Termal Terisolir | 111,86 | 48,3% (Moderate) | High |
| 9 | #256 | Kalimantan Utara | Bulungan | 36,7 | 🟢 Level 1: Monitor | Aktivitas Termal Terisolir | 0,00* | 46,9% (Moderate) | High |
| 10 | #317 | Kalimantan Utara | Malinau | 23,5 | 🟢 Level 1: Monitor | Aktivitas Termal Terisolir | 90,07 | 22,7% (Low) | Moderate |

*\* Luas 0,00 ha menandakan citra optis pasca-api belum bebas awan pada saat analisis Phase 2.*

---

## 3. Analisis Dinamika & Prakiraan Persistensi Klaster (24h & 48h)

| Klaster | Kabupaten | Tren FRP ($\Delta \text{FRP}/\Delta t$) | Jarak Pergeseran (km) | Kecepatan Rambat (km/hari) | Arah Gerak | Prob. 24 Jam | Prob. 48 Jam | Kategori Risiko |
|---|---|---|---|---|---|---|---|---|
| **#327** | Berau | **+3,12 MW/hari (Naik)** | **2,96 km** | **0,74 km/hari** | 330° (Barat Laut) | **93,5%** | **88,3%** | 🔴 **Extreme** |
| **#318** | Landak | +3,12 MW/hari (Naik) | 0,80 km | 0,20 km/hari | 330° (Barat Laut) | **93,3%** | **87,8%** | 🔴 **Extreme** |
| #299 | Sekadau | +3,12 MW/hari (Naik) | 0,30 km | 0,07 km/hari | 330° (Barat Laut) | 77,8% | 64,7% | 🟠 High |
| #243 | Sanggau | +3,12 MW/hari (Naik) | 0,30 km | 0,07 km/hari | 330° (Barat Laut) | 77,0% | 63,6% | 🟠 High |
| #314 | Kutai Timur | +3,12 MW/hari (Naik) | 0,48 km | 0,12 km/hari | 330° (Barat Laut) | 77,3% | 64,0% | 🟠 High |
| #58 | Ketapang | -1,25 MW/hari (Stabil) | 0,69 km | 0,17 km/hari | 330° (Barat Laut) | 84,9% | 74,5% | 🟠 High |
| #16 | Kotawaringin Timur | -1,25 MW/hari (Stabil) | 0,67 km | 0,17 km/hari | 330° (Barat Laut) | 84,6% | 74,1% | 🟠 High |
| #298 | Kapuas Hulu | -1,25 MW/hari (Stabil) | 0,44 km | 0,11 km/hari | 330° (Barat Laut) | 64,1% | 48,3% | 🟡 Moderate |
| #256 | Bulungan | -1,25 MW/hari (Stabil) | 0,30 km | 0,07 km/hari | 330° (Barat Laut) | 62,8% | 46,9% | 🟡 Moderate |
| #317 | Malinau | -1,25 MW/hari (Stabil) | 0,41 km | 0,10 km/hari | 330° (Barat Laut) | 36,0% | 22,7% | 🟢 Low |

---

## 4. Pemodelan Kerentanan Kebakaran Spasial (KFSI)

Kalimantan Fire Susceptibility Index (KFSI) menggabungkan 5 kriteria biofisik di Google Earth Engine:
- **Faktor Defisit Kelembapan (30%):** CHIRPS Anomali + KBDI
- **Faktor Bahan Bakar Vegetasi (25%):** ESA WorldCover 10m
- **Faktor Kerentanan Gambut (20%):** Global Peatland Map 2.0
- **Faktor Kepadatan Historis (15%):** VIIRS Active Fire Kernel Density
- **Faktor Kelerengan Topografi (10%):** NASA SRTM 30m Slope

### Temuan Kerentanan Lanskap per Klaster:
- **4 Klaster berada pada zona *Very High Susceptibility* (KFSI > 0.85):**
  1. Klaster #327 (Berau): **0,964**
  2. Klaster #318 (Landak): **0,957**
  3. Klaster #58 (Ketapang): **0,880**
  4. Klaster #16 (Kotawaringin Timur): **0,876**
- **5 Klaster berada pada zona *High Susceptibility* (0.60–0.80):** Klaster #299, #243, #314, #298, dan #256.
- **1 Klaster berada pada zona *Moderate Susceptibility*:** Klaster #317 (Malinau: **0,516**).

---

## 5. Dispersi Asap & Kualitas Udara (Sentinel-5P TROPOMI)

- **UV Absorbing Aerosol Index (AAI):** Seluruh klaster menunjukkan indeks aerosol rata-rata **0,93** dengan nilai puncak **0,98** pada buffer 10 km (kategori *Low* hingga batas bawah *Moderate*).
- **Kolom Karbon Monoksida (CO):** Konsentrasi berkisar rata-rata **$0,0508\text{ mol/m}^2$** dengan nilai maksimum **$0,0544\text{ mol/m}^2$**.
- **Arah Sebaran Plume Asap:** Diproyeksikan ke arah **Barat Laut (315°–330°)** konsisten dengan vektor angin monsun tenggara pada periode Agustus.

---

## 6. Rekomendasi Tindakan Operasional per Tingkat Bahaya

### 🔴 Level 4: Critical Emergency (Klaster #327 — Berau)
1. **Prioritas Utama Pemadaman Udara (*Water Bombing*):** Pengerahan helikopter water bombing ke area kubah gambut dan perimeter utara-barat laut klaster.
2. **Penyekatan Kanal & Pembasahan Gambut:** Mobilisasi tim Manggala Agni / BPBD untuk mengalirkan air dan membasahi lapisan duff bawah permukaan sebelum api merambat ke serasah yang lebih dalam.
3. **Peringatan Kualitas Udara:** Notifikasi kepada pemukiman di arah barat laut terkait potensi peningkatan konsentrasi asap.

### 🟡 Level 2: Alert (Klaster #318, #58, #16, #314, #299, #243)
1. **Patroli Darat & Verifikasi Lapangan:** Tim patroli terpadu (BPBD, TNI, Polri, MPA) meningkatkan frekuensi pengecekan di titik-titik koordinat sentroid.
2. **Reconnaissance Drone:** Penggunaan drone termal untuk mendeteksi titik api bawah permukaan di Klaster #58 (Ketapang) dan #16 (Kotim) yang memiliki tutupan gambut 72,5%.
3. **Kesiapsiagaan Posko:** Posko pemadam setempat menyiapkan pompa jinjing dan selang pembasahan.

### 🟢 Level 1: Monitor (Klaster #298, #256, #317)
1. **Pemantauan Citra Satelit Harian:** Lanjutkan pemantauan otomatis lintasan satelit SNPP/NOAA-20 harian untuk mendeteksi jika terjadi peningkatan aktivitas mendadak.

---

*Laporan ini dihasilkan secara otomatis oleh sistem Kalimantan Fire Situation Monitor — Phase 4 (Fire Intelligence & Risk Modeling).*
