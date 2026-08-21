# SITUATION REPORT: Burned-Area Confirmation & Severity Assessment (Phase 2)
**Kalimantan Fire Situation Monitor**  
**Periode Analisis:** 14 – 20 Agustus 2026 | **Tanggal Rilis Laporan:** 21 Agustus 2026  
**Sensor Primer:** Copernicus Sentinel-2 L2A (10–20m) | **Sensor Cadangan:** USGS Landsat 8/9 L2 (30m)  
**Referensi Termal:** NASA FIRMS VIIRS (SNPP & NOAA-20 375m) | **Project GEE:** `riset-banjarnegara`

---

## 1. Ringkasan Eksekutif (Executive Summary)

Sebagai kelanjutan dari **Phase 1 (Active-Fire Detection Snapshot)** yang mendeteksi >153.000 anomali termal, **Phase 2** melakukan analisis konfirmasi fisik menggunakan citra satelit optis multispektral resolusi tinggi (*Normalized Burn Ratio* / NBR dan *Differenced NBR* / dNBR) terhadap **10 klaster prioritas teratas** di seluruh Kalimantan.

### 📌 Temuan Kunci:
1. **Total Luas Terbakar Terkonfirmasi:** **2.684,27 Hektare** pada 6 klaster yang telah memasuki fase pasca-kebakaran.
2. **Episentrum Kerusakan Terparah:** **Kabupaten Berau, Kalimantan Timur (Klaster #327)** mengalami kebakaran masif seluas **1.727,77 ha** dengan **139,04 ha mengalami kerusakan tajuk tingkat tinggi (*High Severity*)**, menyumbang **97,5%** dari seluruh kerusakan berat di Kalimantan.
3. **Status Klaster Aktif (*Pending Post-Fire*):** **4 klaster (40%)** di Landak, Sekadau, Bulungan, dan Sanggau berstatus **Siaga Kebakaran Aktif**. Api masih berkobar pada 20 Agustus sehingga citra pasca-api belum terekam pada siklus satelit saat ini.
4. **Validasi Mekanisme *Fallback* Sensor:** Mekanisme pengalihan otomatis dari Sentinel-2 ke **Landsat 8/9** terbukti berhasil pada Klaster #317 (Malinau, Kaltara), mengonfirmasi 90,07 ha area terbakar di tengah tutupan awan tebal.
5. **Integritas Sistem:** Seluruh 10 pengujian validasi otomatis berstatus **PASS (100% lulus)** dan menghasilkan **5.931 poligon batas area terbakar (*burned perimeter*)**.

---

## 2. Rekapitulasi Berdasarkan Provinsi

```
Total Luas Terbakar per Provinsi (Hektare):
┌──────────────────────────────┬──────────────────┬───────────┐
│ Provinsi                     │ Luas Terbakar    │ Porsi (%) │
├──────────────────────────────┼──────────────────┼───────────┤
│ 1. Kalimantan Timur          │ 1.875,32 ha      │  69,9%    │
│ 2. Kalimantan Barat          │   424,83 ha      │  15,8%    │
│ 3. Kalimantan Tengah         │   294,05 ha      │  11,0%    │
│ 4. Kalimantan Utara          │    90,07 ha      │   3,4%    │
│ 5. Kalimantan Selatan        │      0,00 ha      │   0,0%    │
├──────────────────────────────┼──────────────────┼───────────┤
│ TOTAL                        │ 2.684,27 ha      │ 100,0%    │
└──────────────────────────────┴──────────────────┴───────────┘
```

### Tabel Rincian Kelas Keparahan Kebakaran per Provinsi:
*Mengacu pada Standar USGS / Key & Benson (2006)*

| Provinsi | Klaster Dianalisis | Total Terbakar (ha) | Keparahan Tinggi (ha) | Sedang-Tinggi (ha) | Sedang-Rendah (ha) | Rendah (ha) | Unburned / Buffer (ha) |
|---|---|---|---|---|---|---|---|
| **Kalimantan Timur** | 2 | **1.875,32** | 139,04 | 306,08 | 372,89 | 1.057,31 | 12.975,16 |
| **Kalimantan Barat** | 5 | **424,83** | 2,23 | 12,90 | 38,16 | 371,54 | 34.825,57 |
| **Kalimantan Tengah** | 1 | **294,05** | 1,27 | 15,17 | 35,49 | 242,12 | 4.440,65 |
| **Kalimantan Utara** | 2 | **90,07** | 0,00 | 0,00 | 0,27 | 89,80 | 14.390,11 |
| **TOTAL** | **10** | **2.684,27** | **142,54** | **334,15** | **446,81** | **1.760,77** | **66.631,49** |

---

## 3. Rincian Analisis 10 Klaster Prioritas

| Peringkat | ID Klaster | Wilayah Administrasi | Titik VIIRS | FRP Maks (MW) | Sensor Optis | Total Terbakar (ha) | Keparahan Tinggi (ha) | Status Analisis |
|:---:|:---:|---|:---:|:---:|:---:|:---:|:---:|---|
| **#1** | **#327** | **Berau, Kalimantan Timur** | 216 | 60,74 | Sentinel-2 | **1.727,77** | **139,04** | ✅ **Terkonfirmasi (Episentrum Terparah)** |
| **#2** | **#318** | Landak, Kalimantan Barat | 113 | 88,94 | Sentinel-2 | 0,00 | 0,00 | ⏳ **Menunggu Citra Pasca-Api (Api Aktif)** |
| **#3** | **#299** | Sekadau, Kalimantan Barat | 95 | 77,58 | Sentinel-2 | 0,00 | 0,00 | ⏳ **Menunggu Citra Pasca-Api (Api Aktif)** |
| **#4** | **#256** | Bulungan, Kalimantan Utara | 90 | 91,00 | Sentinel-2 | 0,00 | 0,00 | ⏳ **Menunggu Citra Pasca-Api (Api Aktif)** |
| **#5** | **#243** | Sanggau, Kalimantan Barat | 103 | 41,93 | Sentinel-2 | 0,00 | 0,00 | ⏳ **Menunggu Citra Pasca-Api (Api Aktif)** |
| **#6** | **#298** | **Kapuas Hulu, Kalimantan Barat** | 48 | 50,91 | Sentinel-2 | **111,86** | **0,28** | ✅ **Terkonfirmasi** |
| **#7** | **#317** | **Malinau, Kalimantan Utara** | 67 | 31,51 | Landsat-8/9 | **90,07** | **0,00** | ✅ **Terkonfirmasi via Landsat** |
| **#8** | **#58** | **Ketapang, Kalimantan Barat** | 117 | 37,29 | Sentinel-2 | **312,97** | **1,95** | ✅ **Terkonfirmasi** |
| **#9** | **#314** | **Kutai Timur, Kalimantan Timur** | 91 | 29,26 | Sentinel-2 | **147,55** | **0,00** | ✅ **Terkonfirmasi** |
| **#10** | **#16** | **Kotawaringin Timur, Kalteng** | 54 | 26,53 | Sentinel-2 | **294,05** | **1,27** | ✅ **Terkonfirmasi** |

---

## 4. Sorotan Kasus Khusus (Case Studies)

### 🔴 Kasus 1: Episentrum Kebakaran Hutan Berau (Klaster #327)
- **Koordinat Sentroid:** $2,274^\circ\text{ N}, 117,902^\circ\text{ E}$ (Kabupaten Berau, Kaltim).
- **Karakteristik Spektral:** Nilai dNBR maksimum mencapai **+1,477**, mengindikasikan kehancuran kanopi vegetasi secara total dan terbukanya lapisan tanah gambut/mineral kering.
- **Dampak Fisik:**
  - Luas Terbakar Total: **1.727,77 hektare**.
  - Keparahan Tinggi (*High Severity*): **139,04 hektare (8,0%)**.
  - Keparahan Sedang-Tinggi (*Moderate-High*): **305,76 hektare (17,7%)**.
- **Jumlah Poligon Terbentuk:** **1.797 fitur poligon** terfragmentasi di dalam radius buffer 5 km.

### 🟡 Kasus 2: Keberhasilan *Fallback* Landsat 8/9 di Malinau (Klaster #317)
- **Koordinat Sentroid:** $3,120^\circ\text{ N}, 116,120^\circ\text{ E}$ (Kabupaten Malinau, Kaltara).
- **Dinamika Penginderaan Jauh:** Sentinel-2 L2A terkontaminasi tutupan awan $>50\%$ pada jendela pasca-kebakaran. Sistem secara otomatis beralih ke koleksi **Landsat 8/9 C2 L2 (30m)**.
- **Hasil:** Berhasil mengonfirmasi **90,07 hektare** area terbakar (didominasi keparahan rendah seluas 89,80 ha) tanpa intervensi manual.

### ⏳ Kasus 3: Klaster Siaga Api Aktif (Landak #318, Sekadau #299, Bulungan #256, Sanggau #243)
- **Kondisi:** Titik panas VIIRS masih terdeteksi aktif dengan intensitas FRP sangat tinggi (Bulungan 91 MW, Landak 88,9 MW) pada 20 Agustus 2026.
- **Status Metodologi:** Citra pasca-kebakaran bebas asap belum tersedia pada tanggal analisis. Area ini ditetapkan sebagai **Zona Prioritas Pemadaman & Penyekatan Lapangan**.

---

## 5. Distribusi Tingkat Keparahan Kebakaran (*Burn Severity Distribution*)

```
Distribusi Keparahan Seluruh Area Terbakar di Kalimantan (2.684,27 ha):
┌──────────────────────────────────────┬─────────────┬───────────┐
│ Kelas Keparahan                      │ Luas (ha)   │ Porsi (%) │
├──────────────────────────────────────┼─────────────┼───────────┤
│ 🟩 Keparahan Rendah (Low)            │ 1.760,77 ha │  65,6%    │
│ 🟨 Keparahan Sedang-Rendah (Mod-Low) │   446,81 ha │  16,6%    │
│ 🟧 Keparahan Sedang-Tinggi (Mod-High)│   334,15 ha │  12,4%    │
│ 🟥 Keparahan Tinggi (High)           │   142,54 ha │   5,3%    │
├──────────────────────────────────────┼─────────────┼───────────┤
│ TOTAL                                │ 2.684,27 ha │ 100,0%    │
└──────────────────────────────────────┴─────────────┴───────────┘
```

---

## 6. Inventaris Berkas Hasil Ekspor (`export/phase2/`)

Seluruh berkas hasil olahan telah terstruktur dan siap digunakan untuk integrasi GIS, pelaporan dinas, dan dashboard web:

| Sub-Direktori | Nama Berkas | Format | Deskripsi & Isi |
|---|---|---|---|
| `clusters/` | `top20_priority_clusters.geojson` | GeoJSON | 10 titik sentroid klaster prioritas beserta seluruh atributnya. |
| `burned_area/` | `cluster_327_burned_perimeter.geojson` | GeoJSON | 1.797 poligon batas luka bakar Berau (Kaltim). |
| `burned_area/` | `cluster_058_burned_perimeter.geojson` | GeoJSON | 2.259 poligon batas luka bakar Ketapang (Kalbar). |
| `burned_area/` | `cluster_016_burned_perimeter.geojson` | GeoJSON | 965 poligon batas luka bakar Kotim (Kalteng). |
| `burned_area/` | `cluster_314_burned_perimeter.geojson` | GeoJSON | 426 poligon batas luka bakar Kutim (Kaltim). |
| `burned_area/` | `cluster_298_burned_perimeter.geojson` | GeoJSON | 331 poligon batas luka bakar Kapuas Hulu (Kalbar). |
| `burned_area/` | `cluster_317_burned_perimeter.geojson` | GeoJSON | 151 poligon batas luka bakar Malinau (Kaltara). |
| `dnbr_rasters/` | `cluster_XXX_dnbr_url.txt` | Text / URL | Tautan unduh GeoTIFF dNBR resolusi tinggi (20m & 30m). |
| `reports/` | `top20_burned_area_summary.csv` | CSV | Rekapitulasi kuantitatif 10 klaster (22 kolom data). |
| `reports/` | `burn_severity_by_province.csv` | CSV | Rekapitulasi luas terbakar per provinsi. |
| `metadata/` | `phase2_analysis_metadata.json` | JSON | Catatan sistem, parameter, dan status validasi (10/10 PASS). |

---

## 7. Rekomendasi Tindak Lanjut Operasional

1. **Prioritas Pemadaman & Penyekatan (Operasi Darat & Water Bombing):**  
   Fokuskan armada pemadaman darat (Manggala Agni, BPBD, TNI/Polri) pada klaster aktif:
   * **Kabupaten Landak & Sekadau (Kalbar)** — Klaster #318 & #299
   * **Kabupaten Bulungan (Kaltara)** — Klaster #256
   * **Kabupaten Sanggau (Kalbar)** — Klaster #243
2. **Kaji Cepat Kerusakan & Rehabilitasi Lahan (Pasca-Bencana):**  
   Lakukan ground-check dan penilaian kerusakan vegetasi/ekosistem gambut pada klaster yang terkonfirmasi luas:
   * **Kabupaten Berau (Kaltim)** — Klaster #327 (1.727,8 ha)
   * **Kabupaten Ketapang (Kalbar)** — Klaster #58 (312,9 ha)
   * **Kabupaten Kotawaringin Timur (Kalteng)** — Klaster #16 (294,1 ha)
3. **Pemantauan Lanjutan Siklus Satelit (3–5 Hari ke Depan):**  
   Jalankan kembali pipeline Phase 2 pada overpass Sentinel-2 berikutnya untuk mengonfirmasi luasan akhir pada 4 klaster yang saat ini masih aktif terbakar.
