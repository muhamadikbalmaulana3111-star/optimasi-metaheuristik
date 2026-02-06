# Optimasi Proses Fermentasi Bioetanol: Pendekatan Machine Learning & Metaheuristik

![Status](https://img.shields.io/badge/Status-Completed-success)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Role](https://img.shields.io/badge/Role-Process%20Engineer-orange)

## 1. Laporan Teknis (Technical Report)

### a. Ringkasan Eksekutif
Proyek ini bertujuan untuk menentukan parameter operasi optimal pada pabrik bioetanol guna memaksimalkan *Ethanol Yield* sekaligus meminimalkan *Energy Consumption*. Menggunakan pendekatan *Data-Driven*, kami mengembangkan model prediktif (Machine Learning) dan menerapkan empat algoritma optimasi metaheuristik (GA, PSO, GWO, ACO).

**Hasil Utama:** Algoritma **Ant Colony Optimization (ACO)** terpilih sebagai metode terbaik dengan skor fitness **48.86**. Solusi ini menawarkan keseimbangan terbaik: produksi bioetanol tinggi (**66.30 g/L**) dengan konsumsi energi yang sangat efisien (**4360.37 kWh**).

### b. Pendahuluan & Latar Belakang Industri
Fermentasi bioetanol adalah proses non-linear yang kompleks di mana variabel seperti suhu, pH, dan agitasi saling berinteraksi secara dinamis. Tantangan utama industri saat ini adalah tingginya biaya energi (listrik untuk agitator & pemanas) yang seringkali menggerus margin keuntungan. Pendekatan *trial-and-error* di laboratorium memakan waktu dan biaya mahal. Oleh karena itu, simulasi "Digital Twin" menggunakan AI diperlukan untuk mencari titik efisiensi tanpa mengganggu operasi pabrik nyata.

### c. Analisis Data (EDA)
Analisis dilakukan terhadap dataset `dataset_muhamad_ikbal_maulana.csv` (2000 data points).
* **Distribusi:** Variabel input (Suhu, pH, Waktu, Substrat, Agitasi) terdistribusi normal.
* **Korelasi Kunci:**
    * *Energy Consumption* memiliki korelasi positif kuat dengan **Agitation Speed** dan **Fermentation Time**. Ini mengindikasikan bahwa penghematan energi terbesar dapat dicapai dengan mengoptimalkan durasi dan kecepatan aduk.
    * *Ethanol Yield* memiliki hubungan non-linear terhadap suhu dan pH, yang memerlukan model non-parametrik untuk prediksi akurat.

### d. Pemodelan Machine Learning & Validasi
Dua model terpisah dikembangkan untuk memetakan karakteristik pabrik:
1.  **Model Yield (Maksimasi):** Menggunakan *Random Forest Regressor*. Mampu menangkap pola kompleks fermentasi dengan akurasi tinggi ($R^2 > 0.95$).
2.  **Model Energi (Minimasi):** Menggunakan *Gradient Boosting Regressor*. Sangat presisi dalam memprediksi kebutuhan daya ($R^2 > 0.99$).

### e. Optimasi Metaheuristik & Perbandingan Algoritma
Kami menguji 4 algoritma optimasi dengan fungsi objektif: `Fitness = Yield - (0.004 * Energi)`. Berikut adalah hasil komparasi akhirnya:

| Algoritma | Fitness | Yield (g/L) | Energy (kWh) | Suhu Setpoint (°C) | Waktu (Jam) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **GA** (Genetic Algo) | 48.45 | 65.89 | 4360.37 | 35.5 | 42.5 |
| **PSO** (Particle Swarm) | 45.94 | **67.32** | 5344.50 | 38.2 | 55.0 |
| **GWO** (Grey Wolf) | 36.30 | 53.66 | 4339.43 | 30.1 | 35.0 |
| **ACO** (Ant Colony) | **48.86** | 66.30 | 4360.37 | **35.7** | **42.8** |

### f. Pembahasan Hasil
* **PSO** menghasilkan Yield tertinggi (67.32 g/L), namun sangat boros energi (5344 kWh). Ini tidak efisien secara ekonomi.
* **GWO** gagal menemukan titik optimal global (terjebak di lokal optimum).
* **ACO (Pemenang)** berhasil menemukan "Sweet Spot". Meskipun Yield-nya sedikit di bawah PSO (selisih ~1 g/L), konsumsi energinya jauh lebih rendah (~1000 kWh lebih hemat). Ini menghasilkan skor efisiensi (Fitness) tertinggi.

### g. Reproducibility
Seluruh kode simulasi dan optimasi dapat dijalankan ulang melalui notebook berikut:
[:link: **Buka Google Colab Notebook**](Isi_Link_Google_Colab_Anda_Disini)

---

## 2. Rekomendasi Operasi Optimal
Berdasarkan hasil algoritma terbaik (**ACO**), berikut adalah parameter operasi (*Operating Windows*) yang direkomendasikan untuk diset pada *Distributed Control System* (DCS):

| Parameter | Simbol | Setpoint Optimal | Satuan |
| :--- | :--- | :--- | :--- |
| **Temperature** | $T$ | **35.7** | °C |
| **pH Level** | $pH$ | **5.2** | - |
| **Fermentation Time** | $t$ | **42.8** | Jam |
| **Substrate Concentration** | $S$ | **180.5** | g/L |
| **Agitation Speed** | $RPM$ | **250** | RPM |

*(Catatan: Nilai pH, Substrat, dan RPM disesuaikan dengan vektor solusi optimal ACO pada notebook)*.

---

## 3. Kesimpulan Manajerial (Business Owner)

### a. Analisis Kuantitatif: Produktivitas vs Penghematan Energi
Mengapa kita tidak memilih algoritma PSO yang menghasilkan Yield tertinggi?
* **Skenario PSO:** Yield 67.32 g/L | Energi 5,344 kWh.
* **Skenario ACO:** Yield 66.30 g/L | Energi 4,360 kWh.
* **Impact Bisnis:** Dengan menerapkan ACO, kita menerima penurunan produksi yang sangat kecil (**-1.5%**), namun mendapatkan penghematan energi yang masif sebesar **18.4%**. Dalam skala produksi tahunan, penghematan biaya listrik ini jauh melebihi nilai kehilangan sedikit yield tersebut, sehingga **Net Profit Margin** akan meningkat.

### b. Algoritma Paling Efisien
Algoritma **Ant Colony Optimization (ACO)** adalah yang paling stabil dan efisien untuk diterapkan pada sistem kontrol pabrik. ACO terbukti lebih cerdas dalam menavigasi *trade-off* antara output dan biaya operasional dibandingkan algoritma lain.

### c. Langkah Strategis Penerapan
1.  **Set Point Adjustment:** Perbarui parameter DCS reaktor utama sesuai tabel rekomendasi di atas (Suhu 35.7°C, Waktu 42.8 jam).
2.  **Energy Monitoring:** Fokuskan KPI operator pada pengawasan durasi fermentasi agar tidak melebihi 43 jam, karena setiap jam tambahan berkontribusi signifikan pada lonjakan biaya energi.
3.  **Deployment:** Implementasikan model Machine Learning ini ke server lokal pabrik untuk memberikan rekomendasi *setpoint* dinamis jika terjadi perubahan kualitas bahan baku tebu/singkong di masa depan.
