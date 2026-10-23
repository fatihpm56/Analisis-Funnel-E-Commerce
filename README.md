# 🛒 Analisis Funnel E-commerce Elektronik

**Dari 407 Ribu Pengunjung Menjadi Pembeli: Analisis Funnel Konversi E-commerce Elektronik**

Periode data: 24 September 2020 – 28 Februari 2021 | Tools: Python (Google Colab), Power BI

<img width="1225" height="733" alt="Dashboard Analisis Funnel E-commerce" src="https://github.com/user-attachments/assets/58d2cc58-0130-42d1-9cd8-bd63603a9b82" />

---

## 📑 Daftar Isi

1. [Project Background](#1-project-background)
2. [Problem Statement, Tujuan Analisis, dan Business Problem](#2-problem-statement-tujuan-analisis-dan-business-problem)
3. [Analisis](#3-analisis)
   - [Data Understanding](#31-data-understanding)
   - [Data Preprocessing](#32-data-preprocessing)
   - [Analysis and Insight](#33-analysis-and-insight)
4. [Recommendations and Actionable Insights](#4-recommendations-and-actionable-insights)
5. [Tech Stack](#5-tech-stack)
6. [Struktur Repository](#6-struktur-repository)
7. [Author](#7-author)

---

## 1. Project Background

Toko online elektronik dan komputer ini mencatat **±884 ribu event perilaku pengguna** (view, cart, purchase) dari **407 ribu pengguna unik** selama 5 bulan (24 September 2020 – 28 Februari 2021). Trafik yang besar belum otomatis menjadi pendapatan: sebagian besar pengunjung berhenti di tengah perjalanan belanja mereka.

Project ini mengubah data perilaku pengguna menjadi keputusan bisnis yang dapat menaikkan konversi dan pendapatan, **tanpa perlu menambah biaya akuisisi trafik**.

**Hasil yang diharapkan**
- Titik kebocoran (*drop-off*) pada funnel teridentifikasi.
- Insight yang dapat langsung ditindaklanjuti.
- Dashboard interaktif untuk pemantauan berkelanjutan.

**Penerima manfaat:** tim marketing, tim katalog & merchandising, serta manajemen e-commerce.

| 407 rb | 884 rb | 5 bulan | 5,13 juta |
|:--:|:--:|:--:|:--:|
| pengguna unik | event setelah dibersihkan | 24 Sep 2020 – 28 Feb 2021 | total revenue |

---

## 2. Problem Statement, Tujuan Analisis, dan Business Problem

### Problem Statement
Toko online menarik traffic yang besar, tetapi hanya **5,2% pengguna unik** yang akhirnya membeli dan hanya **4,7% aktivitas view** yang berujung transaksi. Funnel menunjukkan kebocoran terbesar terjadi di tahap awal: hanya **6,8% view** yang berlanjut ke cart, padahal **±69% cart** berhasil menjadi pembelian. Artinya, pengunjung yang sudah memasukkan produk ke keranjang cenderung serius membeli, sedangkan sebagian besar pengunjung belum tertarik atau belum yakin untuk melangkah ke keranjang.

### Tujuan Analisis
Menganalisis perilaku pengguna di sepanjang funnel (*view → cart → purchase*) untuk:
- mengidentifikasi **di mana dan mengapa** pengguna berhenti,
- mengetahui **kategori, brand, harga, dan waktu** mana yang konversinya tinggi atau rendah,
- menentukan **langkah paling efektif** untuk meningkatkan konversi dan pendapatan.

### Business Problem
| | |
|---|---|
| **Masalah** | Hanya 5,2% pengguna yang pernah membeli; 4,7% view berakhir transaksi; ±50% produk di keranjang tidak dibeli. Pendapatan juga bergantung pada satu kategori (*computers* ±73%). |
| **Mengapa penting** | Perbaikan kecil di funnel langsung berdampak ke pendapatan tanpa menambah biaya akuisisi trafik. |
| **Dampak jika terpecahkan** | Prioritas perbaikan jelas, promosi tepat sasaran, dan ketergantungan pada satu kategori berkurang. |

### Business Questions
1. Di tahap mana funnel conversion drop, dan kategori mana yang konversinya paling lemah?
2. Bagaimana harga memengaruhi keputusan pengguna, baik untuk memasukkan produk ke keranjang maupun untuk membeli?
3. Kapan (jam dan bulan) konversi paling tinggi dan paling rendah, dan bagaimana tren konversinya?
4. Brand mana yang paling efektif mengubah view menjadi pembelian, dan brand mana yang trafiknya tinggi tetapi konversinya rendah?

---

## 3. Analisis

### 3.1 Data Understanding

**Sumber:** `events.csv`, log aktivitas situs yang dicatat otomatis per event.

| Aspek | Keterangan |
|---|---|
| Ukuran data | 885.129 baris × 9 kolom |
| Kolom | `event_time`, `event_type`, `product_id`, `category_id`, `category_code`, `brand`, `price`, `user_id`, `user_session` |
| Jenis event | `view`, `cart`, `purchase` |
| Cakupan | ±407 ribu pengguna, ±53 ribu produk, ±490 ribu sesi |
| Periode | 24 Sep 2020 – 28 Feb 2021 |
| Zona waktu | UTC |

**Kualitas data yang ditemukan**
- **655 baris duplikat**
- **162 baris** tanpa `user_session`
- `category_code` kosong ±27% dan `brand` kosong ±24%
- Harga ekstrem (maksimum 64.771)

### 3.2 Data Preprocessing

| No | Langkah | Keterangan |
|---|---|---|
| 1 | Hapus duplikat | 655 baris duplikat dibuang |
| 2 | Konversi waktu | `event_time` diubah ke datetime (UTC) |
| 3 | Tangani missing value | Baris tanpa `user_session` dihapus; `category_code` dan `brand` kosong diisi `unknown` |
| 4 | Pecah kategori bertingkat | `category_code` dipecah menjadi `cat1`, `cat2`, `cat3` |
| 5 | Feature engineering waktu | Dibuat kolom `date`, `month`, `hour`, `dow` (hari) |
| 6 | Ubah tipe ID | `product_id`, `category_id`, `user_id` dijadikan string |
| 7 | Tandai outlier harga | Harga di atas persentil 99,9 ditandai (`is_outlier_price`), **tidak dibuang** agar revenue tidak bias |
| 8 | Simpan data bersih | Hasil akhir **884.312 baris** |

> **Catatan:** September 2020 hanya berisi data sejak 24 September, sehingga bulan tersebut parsial dan tidak layak dibandingkan langsung dengan bulan lain.

### 3.3 Analysis and Insight

**Teknik:** analisis deskriptif dan eksploratif (funnel, kategori, harga, brand, pola waktu). Rumus konversi yang dipakai:

- **View → Cart (v2c)** = jumlah cart / jumlah view
- **Cart → Purchase (c2p)** = jumlah purchase / jumlah cart
- **View → Purchase (v2p)** = jumlah purchase / jumlah view

#### 🔹 Funnel dan Kategori
- Dari **406,82 ribu** pengguna yang melihat produk, hanya **36,95 ribu (9,1%)** yang menambah ke keranjang dan **21,30 ribu (5,2%)** yang akhirnya membeli.
- Secara event: 792,94 ribu view → 54,03 ribu cart → 37,34 ribu purchase. **View → purchase hanya 4,7%.**
- **Kebocoran terbesar terjadi di tahap view → cart.**
- Kategori dengan v2c terlemah: **apparel (0,85%)**, **country_yard (1,37%)**, dan **medicine (2,79%)**.
- Kategori di atas rata-rata: **computers (9,89%)** dan **stationery (8,79%)**; di bawah rata-rata: electronics (5,84%), furniture (5,04%), construction (4,75%).

#### 🔹 Pengaruh Harga
Harga dibagi menjadi 5 *bucket* (kuintil): <21, 21–47, 47–96, 96–227, >227.

| Rentang harga | View → Cart | Cart → Purchase |
|---|:--:|:--:|
| 1. Sangat murah (<21) | 5,75% | 79,00% |
| 2. Murah (21–47) | 6,40% | 81,03% |
| 3. Menengah (47–96) | 5,88% | 69,75% |
| 4. Mahal (96–227) | 7,46% | 65,98% |
| 5. Sangat mahal (>227) | 8,63% | 55,63% |

**Insight:** semakin mahal produk, semakin sering dimasukkan ke keranjang, tetapi semakin jarang dibeli. Hambatan produk mahal ada pada **keputusan akhir (checkout)**, bukan pada minat.

#### 🔹 Pola Waktu
- Konversi view → purchase memuncak sekitar **pukul 10.00 UTC (5,38%)** dan terendah pada **pukul 01.00 UTC (3,61%)**.
- Sore hari trafik tinggi tetapi konversi menurun.
- **Tren bulanan naik stabil** dari **4,29% (Okt 2020)** menjadi **5,17% (Feb 2021)**.

#### 🔹 Brand
- **Paling efektif:** sapphire (v2p 11,44%), msi (8,97%), gigabyte (7,65%). msi juga menghasilkan revenue tertinggi di antara brand yang terdata (±643 ribu).
- **Trafik tinggi, konversi rendah:** asus (24 ribu view, v2p 5,20%) dan palit (21 ribu view, v2p 5,02%, c2p hanya 48,06%).
- Brand **unknown** menyumbang ±190 ribu view (terbesar) dengan v2p 5,00%, sehingga banyak produk sulit dianalisis dan dioptimalkan.

#### 🔹 Dashboard Power BI
Dashboard satu halaman berisi KPI (Total User dan Revenue), funnel, tren bulanan, pola per jam, konversi per harga, kategori, dan brand. Dilengkapi filter **Brand, Category, DayName, dan Month** untuk menelusuri segmen.

---

## 4. Recommendations and Actionable Insights

| No | Rekomendasi | Aksi | Target / Dampak |
|:--:|---|---|---|
| 1 | **Perbaiki halaman produk** | Lengkapi foto, spesifikasi, dan ulasan pada kategori di bawah rata-rata (electronics, furniture, construction, juga apparel dan country_yard); sertakan datanya di tampilan | View → cart naik dari 6,8% menjadi 7,5% |
| 2 | **Tangani produk mahal** | Tawarkan cicilan, garansi, gratis ongkir, dan pengingat keranjang (*abandoned cart reminder*) | Cart → purchase rentang termahal naik dari 56% menjadi 65% |
| 3 | **Jadwalkan promosi sesuai pola waktu** | Pusatkan kampanye, flash sale, dan notifikasi pada **pukul 09.00–11.00 UTC**; gunakan promo berbatas waktu di sore hari; kurangi iklan dini hari | Promosi lebih efisien dan tepat sasaran |
| 4 | **Kelola portofolio brand** | Jaga stok dan harga msi, gigabyte, sapphire; selidiki penyebab c2p rendah pada palit; tinjau brand dengan konversi lemah seperti asus | Perkuat brand efektif, benahi yang lemah |
| 5 | **Rapikan data katalog** | Wajibkan pengisian brand dan kategori (±seperempat data kosong) | Analisis dan rekomendasi produk lebih akurat |

**Dampak bisnis:** prioritas perbaikan menjadi jelas, promosi lebih tepat sasaran, dan ketergantungan pada satu kategori (*computers* ±73% revenue) dapat dikurangi.

---

## 5. Tech Stack

- **Python** (Pandas, NumPy, Matplotlib, Seaborn, Plotly) di Google Colab, untuk cleaning dan EDA
- **Power BI**, untuk dashboard interaktif
- **PowerPoint**, untuk presentasi hasil

---

## 6. Struktur Repository

```
├── README.md
├── Analisis_Funnel_E_Commerce.ipynb        # Notebook cleaning & EDA
├── Analisis_Funnel_Ecommerce_Fatih.pptx    # Slide presentasi
├── Dashboard_Analisis_Funnel_E-commerce.png # Preview dashboard
└── data/                                   # (opsional) events.csv
```

---

## 7. Author

**Fatih Putra M.**
Aspiring Data Analyst

- LinkedIn: https://www.linkedin.com/in/fatihputramaulana/
- Email: fatihpm56@gmail.com
