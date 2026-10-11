# Google Books: API, HTTP, dan Text Processing

Kelompok 2: Muhammad Refi Setyawan (159) dan Doni Wahana Putra (248).

## Isi paket

| File atau folder | Isi |
| --- | --- |
| `01_google_books_api.ipynb` | Scraping API, preprocessing, dan Exercise 5 dari data API. |
| `02_google_books_http.ipynb` | Scraping HTML, preprocessing, dan Exercise 5 dari data HTTP. |
| `API_pre-processing_result/` | CSV preprocessing API, frekuensi kata, dan word cloud yang disertakan. |
| `Http_pre-processing_result/` | CSV preprocessing HTTP, frekuensi kata, dan word cloud yang disertakan. |
| `feature_engineering_result/API/` | Matriks fitur, vocabulary, model embedding, tabel, dan grafik hasil API. |
| `feature_engineering_result/HTTP/` | Matriks fitur, vocabulary, model embedding, tabel, dan grafik hasil HTTP. |
| `nltk_data/` | Resource lokal stopwords, WordNet, tagger Inggris, dan tokenisasi. |
| `requirements.txt` | Dependensi scraping, preprocessing, feature engineering, dan Jupyter. |
| `.gitignore` | Aturan pengabaian lingkungan lokal, cache, dan file konfigurasi. |
| `verification.json` | Pemeriksaan data, matriks, model, output UMAP, dan pemisahan sumber API/HTTP. |
| `artifact_manifest.csv` | Daftar file paket beserta ukuran dan checksum. |

## Instalasi

Untuk Colab, masing-masing file `.ipynb` dapat diunggah sendiri dan dijalankan dari awal. Untuk memakai hasil snapshot yang disertakan, ekstrak ZIP dan buka folder `web_scraping_google_books-main`. Lingkungan pengujian menggunakan Python 3.12. Versi paket numerik pada `requirements.txt` mengikuti konfigurasi notebook.

```bash
python -m pip install -r requirements.txt
python -m jupyterlab
```

Di Jupyter atau Colab, dari folder yang memuat `requirements.txt`, instalasi dapat dijalankan pada cell Code:

```python
%pip install -r requirements.txt
```

Setelah instalasi, restart kernel/runtime sebelum menjalankan notebook. Untuk Colab dengan satu notebook, gunakan cell `%pip install` yang sudah tersedia, lalu jalankan scraping, preprocessing, dan Exercise 5 secara berurutan. Untuk mode snapshot, ekstrak ZIP ke runtime atau Drive dan arahkan direktori kerja ke folder paket.

## Menjalankan notebook

1. Buka notebook API atau HTTP dan jalankan cell secara berurutan.
2. Pada notebook API, ganti placeholder `GOOGLE BOOK API KEY` dengan API key milikmu sebelum request. Notebook HTTP memakai halaman Google Books dan tidak memerlukan API key.
3. Preprocessing menggunakan deskripsi hasil scraping pada notebook yang sedang dijalankan. Resource NLTK memakai folder lokal jika tersedia. Saat notebook dijalankan sendiri, resource yang belum ada diunduh otomatis dari versi tertentu pada repositori resmi NLTK Data dengan checksum SHA-256 dan pemeriksaan path arsip. Koneksi internet diperlukan untuk instalasi, scraping, dan unduhan pertama. Pengaturan keamanan NLTK tetap aktif.
4. Exercise 5 memakai `df_processed` dari preprocessing sumber yang sesuai. Jika variabel tersebut belum ada, bagian Exercise 5 membaca CSV snapshot sumbernya sendiri dari folder preprocessing yang disertakan.
5. Hasil feature engineering disimpan pada `feature_engineering_result/API/` atau `feature_engineering_result/HTTP/`.

Preprocessing yang dijalankan ulang menulis hasil ke `hasil_text_processing_api/` atau `hasil_text_processing_http/`. CSV snapshot yang disertakan tetap berada pada folder preprocessing dalam tabel di atas. Direktori output mengikuti direktori kerja notebook atau lokasi paket yang ditemukan oleh loader.

Masing-masing notebook menyediakan cell `%pip install` lengkap di bawah judul library. Setelah dependensi tersedia, bagian Exercise 5 dapat dijalankan sendiri menggunakan snapshot CSV tanpa mengulangi scraping. Mode snapshot memerlukan CSV dari ZIP lengkap. Mode menjalankan notebook dari awal memakai hasil scraping dan preprocessing sendiri sehingga tidak memerlukan file pendamping.

## Metode Exercise 5

Metode mengikuti contoh Chapter 5–6 pada notebook materi: encoding biner dengan `CountVectorizer(binary=True)`, Bag of Words dengan `CountVectorizer()`, TF-IDF, Word2Vec CBOW, serta FastText Skip-Gram. Tokenisasi memakai `nltk.word_tokenize`. Embedding dokumen menggunakan rata-rata vektor kata.

Word2Vec menggunakan vektor 100 dimensi, window 5, min_count 1, dan 5 epoch. FastText menggunakan vektor 100 dimensi, window 5, min_count 1, min_n 3, max_n 6, sg 1, dan 10 epoch seperti contoh kode dosen. N-gram karakter merupakan bagian internal FastText.

Untuk ukuran model yang ringkas, bucket FastText diatur menjadi 20.000. Pelatihan memakai satu worker dan seed 1; UMAP diberi random_state 42. Pengaturan ini dicatat pada `parameters.json`. Fungsi rata-rata menyediakan vektor nol jika tidak ada kata yang memiliki embedding.

Visualisasi mengikuti scatter plot UMAP vektor kata dengan Plotly, seperti pada contoh dosen. File `word2vec_umap.html` dan `fasttext_umap.html` pada masing-masing folder hasil dapat dibuka di browser. Teks semua kata ditampilkan pada scatter plot. Setiap cell UMAP juga menyimpan output PNG agar grafik terlihat pada viewer notebook dan GitHub. Gambar PNG menggunakan koordinat yang sama dengan grafik interaktif.

Fitur dan pelatihan model API menggunakan preprocessing API; fitur dan pelatihan model HTTP menggunakan preprocessing HTTP.

## Hasil tersimpan

Sebanyak 11 cell kode Exercise 5 pada masing-masing notebook diuji dari CSV snapshot: API mempunyai 9 deskripsi valid dan HTTP mempunyai 1 deskripsi valid. Scraping dan Chapter 4 tidak dijalankan ulang pada pengujian ini. Output lama Chapter 4 API memuat 6 deskripsi valid, sementara snapshot API memuat 9 deskripsi valid; mode input dicatat pada hasil Exercise 5.

Setiap folder hasil berisi matriks sparse `.npz`, vectorizer `.joblib`, vocabulary CSV, model Word2Vec/FastText `.model`, matriks embedding `.npy`, koordinat UMAP CSV, visualisasi HTML, metadata, parameter, status eksekusi, serta manifest file. Gunakan `requirements.txt` di root untuk instalasi lengkap dari awal.

Cell Markdown notebook berisi judul bagian. Hasil angka, tabel, dan grafik dipertahankan. Warning tampilan progress yang sebelumnya muncul telah dibersihkan, dengan penanganan khusus sebelum impor library.

Hasil scraping dapat berubah mengikuti data dan HTML Google Books. Jika API mengembalikan 503, tunggu dan coba ulang cell request. Resource lokal diuji tanpa akses jaringan; mode satu notebook diuji dari direktori kosong dengan unduhan resource resmi, kemudian preprocessing dan loader feature engineering diuji untuk masing-masing sumber. Instalasi bersih Colab belum diuji secara langsung.

## Mengunggah ke GitHub

Unggah isi folder paket setelah diekstrak, termasuk folder hasil, preprocessing, dan `nltk_data/`. `.gitignore` mengabaikan lingkungan Python lokal, cache, checkpoint notebook, folder upload sementara, dan file `.env`. API key di notebook masih berupa placeholder; isi key saat menjalankan notebook dan kembalikan ke placeholder sebelum menyimpan versi publik.
