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

Dataset berisi informasi 10.000 buku dari Goodreads dengan 8 kolom berikut:

| Column        | Tipe     | Deskripsi                          |
|---------------|----------|-------------------------------------|
| `Book`        | object   | Judul buku                          |
| `Author`      | object   | Nama penulis                        |
| `Description` | object   | Ringkasan isi buku (deskripsi)     |
| `Genres`      | object   | Genre utama buku                   |
| `Avg_Rating`  | float64  | Rating rata-rata buku              |
| `Num_Ratings` | object   | Jumlah rating pengguna             |
| `URL`         | object   | Tautan ke halaman Goodreads        |

> Catatan: `Description` memiliki 9923 entri non-null dan `Num_Ratings` perlu dikonversi ke numerik dari format teks.

---

##  Exploratory Data Analysis

Analisis dilakukan untuk memahami tren dan distribusi data:

- **Top Genre dan Penulis Populer**: Visualisasi 10 genre dan penulis terbanyak.
- **Distribusi Rating**: Sebagian besar buku memiliki rating antara 3.5 hingga 4.5.
- **WordCloud**: Visualisasi kata populer dari deskripsi buku.
- **Korelasi**: Korelasi antara jumlah rating dan rating rata-rata.

---

##  Data Preparation

Langkah-langkah utama:
- Menghapus duplikat dan data kosong.
- Pembersihan deskripsi menggunakan regex dan lowercasing.
- Lemmatization dan token filtering untuk deskripsi.
- Ekstraksi genre utama dari string genre.
- Transformasi `Num_Ratings` dari teks ke integer.
- Penambahan fitur baru: panjang deskripsi (jumlah kata).
- Vektorisasi:
  - TF-IDF pada deskripsi dan genre.
  - Normalisasi `Avg_Rating`, `Num_Ratings`, dan `Desc_Length`.
- Gabungan semua fitur menjadi vektor `book_vectors`.

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
          0         1         2         3         4
0  1.000000  0.463594  0.555042  0.393592  0.464165
1  0.463594  1.000000  0.378306  0.340730  0.373731
2  0.555042  0.378306  1.000000  0.376996  0.380583
3  0.393592  0.340730  0.376996  1.000000  0.284297
4  0.464165  0.373731  0.380583  0.284297  1.000000

Nilai diagonal (misal similarity[0][0] = 1.0) menandakan buku dibandingkan dengan dirinya sendiri.
Nilai non-diagonal menunjukkan kemiripan antar buku.
Misalnya: Buku ke-0 paling mirip dengan buku ke-2 (nilai 0.555042).


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

