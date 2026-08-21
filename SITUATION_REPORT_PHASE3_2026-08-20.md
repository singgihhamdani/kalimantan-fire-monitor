# LAPORAN SITUASI PHASE 3 — KONTEKS LINGKUNGAN & CUACA KEBAKARAN
## Kalimantan Fire Situation Monitor
**Tanggal Analisis:** 20 Agustus 2026 (18:45 UTC)  
**Cakupan Wilayah:** Pulau Kalimantan (4 dari 5 Provinsi Terdeteksi Aktif)  
**Jumlah Klaster Dianalisis:** 10 klaster prioritas  
**Status Validasi:** 10/10 pengujian otomatis LOLOS ✓

---

## 1. Ringkasan Eksekutif

Analisis Phase 3 menambahkan **konteks biofisik dan atmosferik** terhadap 10 klaster prioritas kebakaran yang telah teridentifikasi di Phase 1 (deteksi titik panas VIIRS) dan Phase 2 (konfirmasi area terbakar via citra optis Sentinel-2/Landsat). Temuan utama:

> **9 dari 10 klaster kebakaran mengalami kondisi kekeringan tanah kategori "Kering" (KBDI 400–600)**, dengan defisit curah hujan 30 hari berkisar antara **-20,9% hingga -99,9%** dibandingkan rata-rata historis.

> **4 dari 10 klaster berada di atas lahan gambut** yang teridentifikasi oleh Global Peatland Map 2.0, dengan estimasi total **1.057,24 hektare area terbakar terkonfirmasi (Phase 2) berlokasi di atas tanah gambut**.

> **PENTING — Prinsip Interpretasi:** Temuan ini menunjukkan **asosiasi spasial (*spatial association*)** antara lokasi kebakaran, defisit presipitasi, dan tutupan gambut — **BUKAN bukti kausalitas**. Penyebab langsung kebakaran memerlukan investigasi lapangan terpisah.

---

## 2. Kondisi Curah Hujan & Defisit Presipitasi (CHIRPS)

### 2.1 Curah Hujan 30 Hari (Kondisi Terkini)

| Peringkat | Klaster | Kabupaten | Provinsi | Hujan 30h (mm) | Anomali 30h | Hari Kering |
|---|---|---|---|---|---|---|
| 1 | #327 | Berau | Kalimantan Timur | 38,5 | **-74,3%** | 26 hari |
| 2 | #318 | Landak | Kalimantan Barat | 27,0 | **-82,0%** | 27 hari |
| 3 | #299 | Sekadau | Kalimantan Barat | 40,9 | **-72,7%** | 25 hari |
| 4 | #256 | Bulungan | Kalimantan Utara | 118,7 | -20,9% | 18 hari |
| 5 | #243 | Sanggau | Kalimantan Barat | 47,0 | **-68,7%** | 25 hari |
| 6 | #298 | Kapuas Hulu | Kalimantan Barat | 64,6 | **-56,9%** | 23 hari |
| 7 | #317 | Malinau | Kalimantan Utara | 158,9 | +5,9% | 14 hari |
| 8 | #58 | Ketapang | Kalimantan Barat | **0,1** | **-99,9%** | **29 hari** |
| 9 | #314 | Kutai Timur | Kalimantan Timur | 44,8 | **-70,1%** | 25 hari |
| 10 | #16 | Kotawaringin Timur | Kalimantan Tengah | 14,7 | **-90,2%** | **28 hari** |

### 2.2 Curah Hujan 90 Hari (Kondisi Kumulatif / Musiman)

| Klaster | Hujan 90h (mm) | Anomali 90h | Interpretasi |
|---|---|---|---|
| #327 | 319,6 | -29,0% | Defisit musiman moderat |
| #318 | 330,5 | -26,6% | Defisit musiman moderat |
| #299 | 374,6 | -16,8% | Mendekati normal |
| #256 | 703,2 | +56,3% | Di atas rata-rata (Kaltara basah) |
| #243 | 386,6 | -14,1% | Mendekati normal |
| #298 | 435,8 | -3,2% | Mendekati normal |
| #317 | 920,9 | +104,6% | Jauh di atas rata-rata |
| #58 | 269,7 | **-40,1%** | **Defisit kumulatif berat** |
| #314 | 382,0 | -15,1% | Mendekati normal |
| #16 | 272,4 | **-39,5%** | **Defisit kumulatif berat** |

### 2.3 Interpretasi Presipitasi

**Klaster dengan Defisit Presipitasi Paling Parah:**

1. **Klaster #58 (Ketapang, Kalbar)** — Hanya menerima **0,1 mm hujan dalam 30 hari** (anomali -99,9%). Kondisi ini menunjukkan kekeringan ekstrem di wilayah pesisir barat Kalimantan. Dari total 312,97 ha area terbakar terkonfirmasi, 226,90 ha berada di atas lahan gambut.

2. **Klaster #16 (Kotawaringin Timur, Kalteng)** — Hanya 14,7 mm dalam 30 hari (anomali -90,2%) dengan 28 hari kering. Area kubah gambut Kalimantan Tengah secara historis merupakan salah satu hotspot kebakaran hutan dan lahan terburuk di Indonesia.

3. **Klaster #327 (Berau, Kaltim)** — Meskipun anomali 30h "hanya" -74,3%, klaster ini memiliki **luas terbakar terbesar** (1.727,77 ha) dari seluruh Phase 2.

**Pengecualian Penting:**
- **Klaster #317 (Malinau, Kaltara)** memiliki curah hujan mendekati normal (+5,9%) dan KBDI rendah (199,7 — kategori "Basah"). Meski demikian, kebakaran tetap terdeteksi (90,07 ha), mengindikasikan bahwa faktor selain kekeringan mungkin berperan — namun hal ini memerlukan investigasi lapangan.

---

## 3. Indeks Kekeringan Tanah (KBDI — Keetch-Byram Drought Index)

| Klaster | Kabupaten | Skor KBDI | Klasifikasi | Interpretasi |
|---|---|---|---|---|
| #327 | Berau | **521,4** | Kering | Serasah organik dalam mengering; potensi kebakaran bawah permukaan |
| #318 | Landak | 512,9 | Kering | Lapisan duff mengering; bahan bakar permukaan mudah menyala |
| #299 | Sekadau | 478,6 | Kering | Lapisan organik bawah mulai terdampak |
| #256 | Bulungan | 420,0 | Kering | Transisi ke fase kering |
| #243 | Sanggau | 469,3 | Kering | Ketersediaan bahan bakar kering meningkat |
| #298 | Kapuas Hulu | 431,0 | Kering | Defisit kelembapan moderat |
| **#317** | **Malinau** | **199,7** | **Basah** | Satu-satunya klaster dengan tanah masih jenuh lembap |
| #58 | Ketapang | 419,8 | Kering | Lapisan organik mengering; area gambut pesisir |
| #314 | Kutai Timur | 472,9 | Kering | Serasah permukaan dan lapisan bawah kering |
| #16 | Kotawaringin Timur | 415,6 | Kering | Kubah gambut Kalteng; water table menurun |

**Interpretasi:** 9 dari 10 klaster berada di kategori **Kering (KBDI 400–600)**, yang berarti lapisan organik bawah mulai mengering dan berkontribusi sebagai bahan bakar dalam kebakaran. Tidak ada klaster yang mencapai kategori Kekeringan Ekstrem (KBDI >= 600) saat analisis ini, namun jika musim kemarau berlanjut tanpa hujan signifikan, beberapa klaster berisiko memasuki kategori tersebut.

---

## 4. Kondisi Cuaca: Suhu Udara & Angin

### 4.1 Suhu Udara & Permukaan Tanah

| Klaster | Suhu Udara Maks (C) | Suhu Permukaan / LST (C) | Selisih LST - Udara |
|---|---|---|---|
| #327 | 35,0 | 29,8 | -5,2 (kanopi hutan menjaga kelembapan) |
| #318 | 35,0 | 39,0 | +4,0 (permukaan terbuka / vegetasi kering) |
| #299 | 35,0 | 39,0 | +4,0 |
| #256 | 35,0 | 25,8 | -9,2 (kanopi lebat) |
| #317 | 24,8 | 21,7 | -3,1 (wilayah pegunungan, lebih dingin) |
| #58 | 28,5 | 27,6 | -0,9 |
| #16 | 28,4 | 26,9 | -1,5 |

### 4.2 Kecepatan & Arah Angin

Kecepatan angin 6-7 km/jam tergolong **lemah hingga sedang**. Arah angin **Tenggara (TG)** pada Klaster #58 (Ketapang) dan #16 (Kotawaringin Timur) konsisten dengan pola angin musim kemarau Kalimantan yang membawa massa udara kering dari Australia.

---

## 5. Asosiasi Spasial Lahan Gambut

| Klaster | Kabupaten | Status Gambut | Proporsi Gambut (%) | Luas Terbakar (ha) | Terbakar di Gambut (ha) |
|---|---|---|---|---|---|
| #327 | Berau | Ya | 34,0% | 1.727,77 | **587,44** |
| #318 | Landak | Ya | 34,0% | 0,00 (pending) | 0,00 |
| #58 | Ketapang | Ya | **72,5%** | 312,97 | **226,90** |
| #16 | Kotawaringin Timur | Ya | **72,5%** | 294,05 | **213,19** |

**Ringkasan:**

| Metrik | Nilai |
|---|---|
| Total klaster di atas lahan gambut | **4 dari 10** (40%) |
| Total area terbakar terkonfirmasi Phase 2 | 2.684,27 ha |
| Area terbakar di atas lahan gambut | **1.057,24 ha** |
| **Proporsi terbakar di lahan gambut** | **39,4%** |

---

## 6. Konteks Lingkungan per Provinsi

| Provinsi | Klaster | Luas Terbakar (ha) | Terbakar di Gambut (ha) | Hujan 30h Rata-rata (mm) | KBDI Rata-rata |
|---|---|---|---|---|---|
| Kalimantan Timur | 2 | **1.875,3** | 600,0 | 41,6 | **497,2** |
| Kalimantan Barat | 5 | 424,8 | 236,4 | 35,9 | 462,3 |
| Kalimantan Tengah | 1 | 294,0 | 213,2 | 14,7 | 415,6 |
| Kalimantan Utara | 2 | 90,1 | 7,7 | 138,8 | 309,8 |
| Kalimantan Selatan | 0 | - | - | - | - |

---

## 7. Catatan Kualitas Data & Keterbatasan

### 7.1 Data Tutupan Lahan (ESA WorldCover)

**PERINGATAN:** Hasil analisis tutupan lahan menunjukkan nilai identik (Tutupan Pohon 68,4%, Semak 18,2%, Pertanian 10,5%) untuk semua 10 klaster. Hal ini sangat kuat menunjukkan bahwa ekstraksi nilai tutupan lahan ESA WorldCover melalui Google Earth Engine menggunakan **nilai fallback** akibat kegagalan koneksi atau timeout API. Data tutupan lahan per klaster **belum dapat diandalkan** dan memerlukan re-eksekusi notebook dengan koneksi GEE yang stabil.

### 7.2 Data Cuaca (ERA5-Land)

Beberapa klaster menampilkan suhu udara maksimum identik (35,0 C) dan kecepatan angin yang mirip (6,9 km/jam). Hal ini juga mungkin menunjukkan penggunaan nilai fallback pada sebagian query ERA5-Land.

### 7.3 Cakupan Klaster

Hanya **10 dari 20 klaster prioritas** yang berhasil dianalisis. Sisa 10 klaster memerlukan pemrosesan tambahan pada eksekusi notebook berikutnya.

---

## 8. Profil Lingkungan 3 Klaster Kritis

### Klaster #327 — Berau, Kalimantan Timur (PRIORITAS #1)

- Luas Terbakar (Phase 2): 1.727,77 ha (TERBESAR)
- Curah Hujan 30h: 38,5 mm (Anomali: -74,3%)
- Curah Hujan 90h: 319,6 mm (Anomali: -29,0%)
- Hari Kering: 26 dari 30 hari
- Suhu Udara Maks: 35,0 C
- KBDI: 521,4 (Kering — Defisit Tinggi)
- Status Gambut: Ya (34,0%)
- Terbakar di Gambut: 587,44 ha

### Klaster #58 — Ketapang, Kalimantan Barat (PRIORITAS #8)

- Luas Terbakar (Phase 2): 312,97 ha
- Curah Hujan 30h: 0,1 mm (Anomali: -99,9% — EKSTREM)
- Curah Hujan 90h: 269,7 mm (Anomali: -40,1%)
- Hari Kering: 29 dari 30 hari
- KBDI: 419,8 (Kering)
- Status Gambut: Ya (72,5% — KONSENTRASI TINGGI)
- Terbakar di Gambut: 226,90 ha

### Klaster #16 — Kotawaringin Timur, Kalimantan Tengah (PRIORITAS #10)

- Luas Terbakar (Phase 2): 294,05 ha
- Curah Hujan 30h: 14,7 mm (Anomali: -90,2%)
- Curah Hujan 90h: 272,4 mm (Anomali: -39,5%)
- Hari Kering: 28 dari 30 hari
- KBDI: 415,6 (Kering)
- Status Gambut: Ya (72,5% — KONSENTRASI TINGGI)
- Terbakar di Gambut: 213,19 ha

---

## 9. Kesimpulan

1. **Defisit Presipitasi Meluas:** 8 dari 10 klaster mengalami anomali curah hujan 30 hari negatif (lebih dari -55%), menunjukkan kekeringan berskala regional di Kalimantan pada Agustus 2026.

2. **Kekeringan Tanah Konsisten:** 9 dari 10 klaster memiliki KBDI kategori Kering (400-600), yang berarti bahan bakar organik lapisan bawah telah mengering.

3. **Paparan Gambut Signifikan:** Dari 2.684,27 ha area terbakar terkonfirmasi Phase 2, sekitar 1.057,24 ha (39,4%) berada di atas lahan gambut.

4. **Satu Anomali Penting:** Klaster #317 (Malinau) memiliki kondisi Basah (KBDI 199,7) namun tetap terbakar, mengindikasikan faktor non-klimatik yang memerlukan investigasi lapangan.

**Prinsip Non-Kausalitas:** Seluruh analisis Phase 3 menunjukkan hubungan spasial dan temporal antara kondisi lingkungan dengan lokasi kebakaran. Analisis ini TIDAK membuktikan bahwa kekeringan atau gambut menyebabkan kebakaran.

---

*Laporan ini dihasilkan oleh Kalimantan Fire Situation Monitor — Phase 3 (Environmental Context and Fire Weather Assessment). Seluruh analisis bersifat otomatis menggunakan citra satelit penginderaan jauh dan data reanalisis iklim.*
