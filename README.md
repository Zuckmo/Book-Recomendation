#  Book Recommendation System

##  Domain Proyek
Proyek ini berada di domain **Rekomendasi Buku** menggunakan teknik **machine learning** dan **pemrosesan bahasa alami (NLP)**. Sistem ini bertujuan membantu pengguna menemukan buku yang relevan dengan preferensi mereka berdasarkan deskripsi, genre, dan rating buku.

---

##  Latar Belakang
Di era digital, jumlah buku yang tersedia secara daring semakin banyak, membuat pengguna kesulitan memilih bacaan yang sesuai. Oleh karena itu, sistem rekomendasi menjadi solusi penting dalam menyaring informasi dan menawarkan buku yang sesuai dengan selera pembaca.

---

##  Business Understanding
Platform penyedia buku atau e-commerce seperti Goodreads dan Amazon membutuhkan sistem rekomendasi yang efisien untuk meningkatkan pengalaman pengguna dan mendorong keterlibatan serta penjualan.

---

##  Problem Statement
Bagaimana membangun sistem rekomendasi buku yang dapat memberikan saran bacaan relevan berdasarkan deskripsi, genre, dan rating buku dari dataset Goodreads?

---

##  Goals
- Mengembangkan sistem rekomendasi buku berbasis konten (content-based filtering).
- Menganalisis data buku untuk memahami preferensi pembaca.
- Menyediakan rekomendasi buku yang akurat dan relevan.

---

##  Solution Statements
- Melakukan eksplorasi dan pra-pemrosesan data deskriptif dan numerik.
- Menggunakan metode TF-IDF dan normalisasi fitur numerik.
- Menghitung kemiripan antar buku menggunakan cosine similarity.
- Menyediakan fungsi rekomendasi berdasarkan judul dan ID buku.

---

##  Data Understanding


Dataset yang digunakan berasal dari Goodreads dan dapat diakses melalui tautan berikut:

 **[goodreads_data.csv](https://raw.githubusercontent.com/Zuckmo/Book-Recomendation/refs/heads/main/goodreads_data.csv)**

Dataset ini berisi informasi mengenai **10.000 buku** dari Goodreads, dengan **8 kolom** sebagai berikut:

| Kolom        | Tipe Data | Deskripsi                                                                 |
|--------------|-----------|---------------------------------------------------------------------------|
| Unnamed: 0   | int64     | Indeks baris dari dataset asli (digunakan sebagai ID sementara)           |
| Book         | object    | Judul buku                                                                |
| Author       | object    | Nama penulis                                                              |
| Description  | object    | Ringkasan isi buku (*deskripsi*)                                         |
| Genres       | object    | Genre utama buku                                                          |
| Avg_Rating   | float64   | Rating rata-rata buku dari pengguna Goodreads                             |
| Num_Ratings  | object    | Jumlah pengguna yang memberikan rating, dalam format teks (perlu dikonversi) |
| URL          | object    | Tautan ke halaman buku di Goodreads                                       |

>  **Catatan:**
> - Kolom `Num_Ratings` perlu dikonversi ke format numerik.
> - Kolom `Description` memiliki **9.923 entri non-null** dari total 10.000.


---

##  Exploratory Data Analysis

Analisis dilakukan untuk memahami tren dan distribusi data:

- **Top Genre dan Penulis Populer**: Visualisasi 10 genre dan penulis terbanyak.
- **Distribusi Rating**: Sebagian besar buku memiliki rating antara 3.5 hingga 4.5.
- **WordCloud**: Visualisasi kata populer dari deskripsi buku.
- **Korelasi**: Korelasi antara jumlah rating dan rating rata-rata.

---

##  Data Preparation


Tahapan ini bertujuan untuk membersihkan, merapikan, dan menyiapkan data agar bisa digunakan secara efektif dalam sistem rekomendasi berbasis konten. Dataset awal terdiri dari 9.923 entri dan 8 kolom. Berikut adalah langkah-langkah utamanya:

---

### 1. Pilih Fitur yang Relevan

Langkah pertama adalah memilih kolom yang benar-benar diperlukan untuk membangun sistem rekomendasi. Kolom yang tidak relevan dihilangkan agar proses analisis lebih efisien dan fokus. Kolom yang dipilih antara lain:

- Judul buku (`Book`)
- Nama penulis (`Author`)
- Deskripsi buku (`Description`)
- Genre buku (`Genres`)
- Rata-rata rating (`Avg_Rating`)
- Jumlah rating (`Num_Ratings`)

---

### 2. Tangani Nilai Hilang dan Duplikat

Data yang tidak lengkap atau duplikat dapat mengganggu proses analisis. Oleh karena itu:

- Baris yang memiliki nilai kosong (missing values) dihapus.
- Baris yang duplikat (sama persis) juga dihapus agar tidak terjadi bias atau pengulangan informasi.

---

### 3. Bersihkan Teks dan Data

Pembersihan teks sangat penting terutama untuk analisis berbasis konten. Tahapan ini meliputi:

#### a. Pembersihan Kolom Teks

- Menghapus spasi kosong berlebih di awal/akhir pada kolom judul dan penulis.
- Mengubah semua huruf di deskripsi menjadi huruf kecil (lowercase).
- Menghapus karakter khusus seperti tanda baca, simbol, dan newline (`\n`).
- Menghapus spasi yang berlebihan di dalam teks.

#### b. Penambahan Fitur Baru

- Membuat kolom baru untuk menyimpan versi teks deskripsi yang telah dibersihkan.
- Menghitung panjang deskripsi (jumlah kata) sebagai fitur tambahan.
- Memecah genre ke dalam bentuk daftar dan menyimpan genre utama sebagai fitur tambahan.

#### c. Tangani Nilai Anomali

Tiga kolom penting dianalisis nilainya:
- `Avg_Rating` (rata-rata rating)
- `Num_Ratings` (jumlah rating)
- `Desc_Length` (panjang deskripsi)

Baris dengan nilai nol pada salah satu kolom di atas dihapus karena dianggap tidak informatif atau berpotensi menyesatkan analisis.

---

### 4. Representasi Teks dengan TF-IDF

TF-IDF (Term Frequency – Inverse Document Frequency) digunakan untuk mengubah data teks menjadi format numerik yang bisa diproses oleh komputer.

- TF-IDF memberikan bobot tinggi pada kata-kata yang unik dan informatif.
- Kata-kata umum seperti “the”, “and”, “book” diberi bobot rendah atau diabaikan.
- TF-IDF digunakan pada kolom deskripsi buku dan genre buku.

---

### 5. Normalisasi

Normalisasi adalah proses mengubah nilai-nilai numerik agar berada dalam skala yang sama, misalnya 0–1. Ini penting untuk:

- Menghindari fitur tertentu mendominasi proses perhitungan jarak atau kesamaan.
- Memastikan bahwa semua fitur memiliki pengaruh yang seimbang dalam model rekomendasi.

Fitur yang dinormalisasi antara lain:
- Rata-rata rating (`Avg_Rating`)
- Jumlah rating (`Num_Ratings`)
- Panjang deskripsi (`Desc_Length`)

Setelah semua proses ini, fitur-fitur dari teks, genre, dan angka digabung menjadi satu representasi akhir yang siap digunakan dalam model rekomendasi.

---


---

##  Model Development

### Algoritma:
- Menggunakan **cosine similarity** antara vektor buku.
- Content-based filtering: pengguna mendapatkan rekomendasi berdasarkan kemiripan deskripsi dan genre dari buku yang mereka pilih.

### Fungsi:
- `recommend(book_title, top_n)`: Rekomendasi berdasarkan judul.
- `get_recommendations(book_id, k)`: Rekomendasi berdasarkan ID (untuk evaluasi).
- `recommend_partial(book_title)`: Fuzzy matching untuk pencarian.

### Penjelasan Output: Similarity Matrix
- Shape dari similarity matrix: (9850, 9850)
- Terdapat 9.850 buku unik dalam sistem rekomendasi.
- Matriks ini adalah matriks simetri ukuran 9850 x 9850.

Contoh isi similarity matrix:
|     |    0     |    1     |    2     |    3     |    4     |
|-----|----------|----------|----------|----------|----------|
| 0   | 1.000000 | 0.463594 | 0.555042 | 0.393592 | 0.464165 |
| 1   | 0.463594 | 1.000000 | 0.378306 | 0.340730 | 0.373731 |
| 2   | 0.555042 | 0.378306 | 1.000000 | 0.376996 | 0.380583 |
| 3   | 0.393592 | 0.340730 | 0.376996 | 1.000000 | 0.284297 |
| 4   | 0.464165 | 0.373731 | 0.380583 | 0.284297 | 1.000000 |


Nilai diagonal (misal similarity[0][0] = 1.0) menandakan buku dibandingkan dengan dirinya sendiri.
Nilai non-diagonal menunjukkan kemiripan antar buku.
Misalnya: Buku ke-0 paling mirip dengan buku ke-2 (nilai 0.555042).

---

Contoh Rekomendasi buku untuk 'The Giving Tree':

| Book                               | Author          | Genres                                                                                             | Avg_Rating |
|------------------------------------|------------------|----------------------------------------------------------------------------------------------------|-------------|
| Where the Sidewalk Ends            | Shel Silverstein | ['Poetry', 'Childrens', 'Classics', 'Fiction', 'Humor', 'Young Adult', 'Picture Books']            | 4.32        |
| Falling Up                         | Shel Silverstein | ['Poetry', 'Childrens', 'Fiction', 'Humor', 'Classics', 'Picture Books', 'Young Adult']            | 4.35        |
| The Missing Piece (The Missing Piece, #1) | Shel Silverstein | ['Poetry', 'Childrens', 'Picture Books', 'Fiction', 'Classics', 'Young Adult', 'Humor']           | 4.27        |
| Oh, the Places You'll Go!          | Dr. Seuss        | ['Childrens', 'Picture Books', 'Fiction', 'Classics', 'Poetry', 'Fantasy', 'Young Adult']          | 4.36        |
| Lafcadio, the Lion Who Shot Back   | Shel Silverstein | ['Childrens', 'Fiction', 'Poetry', 'Picture Books', 'Humor', 'Fantasy', 'Young Adult']             | 4.16        |


**Insight Rekomendasi untuk "The Giving Tree"**

- Dominasi Karya Shel Silverstein
Empat dari lima buku yang direkomendasikan ditulis oleh Shel Silverstein, yang juga merupakan penulis The Giving Tree. Hal ini menunjukkan bahwa model rekomendasi mengenali gaya penulisan, tema, dan target pembaca yang konsisten dari penulis tersebut. Ini menjadi sinyal kuat bahwa pembaca yang menyukai The Giving Tree kemungkinan besar akan menyukai karya lain dari penulis yang sama.

- Genre yang Konsisten
Semua buku yang direkomendasikan memiliki genre dominan:
1. Childrens
2. Poetry
3. Picture Books
4. Classics
5. Humor atau Young Adult

Ini menunjukkan bahwa model mengenali elemen emosional, visual, dan sastra sederhana namun mendalam yang menjadi karakteristik The Giving Tree, dan mencari buku dengan kombinasi genre yang serupa.

- Rating Tinggi dan Konsisten
Semua buku yang direkomendasikan memiliki rating di atas 4.1, dengan dua buku di atas 4.3. Artinya, model tidak hanya mempertimbangkan kemiripan konten, tetapi juga reputasi dan kepuasan pembaca sebagai faktor penting.


---

##  Evaluasi

### 1. Precision
Average Precision (AP): 0.73

Nilai Average Precision sebesar 0.73 menunjukkan bahwa rekomendasi yang diberikan cukup akurat pada level individual—artinya, urutan item yang direkomendasikan cukup tepat dalam mencakup item yang relevan. Skor ini dekat dengan 1, yang berarti precision di antara daftar rekomendasi cukup baik.

### 2. Mean Average Precision (MAP)
Mean Average Precision (MAP): 0.47

Nilai MAP sebesar 0.47 menunjukkan rata-rata dari precision across multiple queries (dalam hal ini, mungkin rekomendasi untuk beberapa pengguna atau buku). Meskipun nilainya tidak setinggi AP individual, MAP = 0.47 masih dapat dikategorikan sebagai hasil yang cukup baik untuk sistem rekomendasi berbasis konten (content-based filtering). Artinya, dari semua pengguna/buku yang diuji, secara rata-rata sistem mampu memberikan rekomendasi yang relevan hampir separuh dari daftar yang diberikan.


---

##  Dependencies

- `pandas`, `numpy`
- `matplotlib`, `seaborn`
- `nltk`, `wordcloud`, `re`
- `sklearn`: TF-IDF, Normalizer, Cosine Similarity, evaluasi
- `scipy`: sparse matrix

---

##  Author

- Zuckmo - [GitHub](https://github.com/Zuckmo)
- Sistem dikembangkan dan diuji di Google Colab.

---

##  Referensi Dataset

- [goodreads_data.csv](https://raw.githubusercontent.com/Zuckmo/Book-Recomendation/refs/heads/main/goodreads_data.csv)

