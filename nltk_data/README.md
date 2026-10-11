# Resource NLTK lokal

Folder ini menyediakan resource lokal untuk kedua notebook. Saat memakai ZIP lengkap, sertakan folder ini agar tidak perlu mengunduh ulang. Masing-masing notebook juga dapat dijalankan sendiri: resource yang belum tersedia diunduh otomatis dari repositori resmi dengan checksum SHA-256, tanpa `nltk.download()`.

Resource berasal dari [repositori resmi NLTK Data](https://github.com/nltk/nltk_data). SHA-256 setiap arsip telah dicocokkan dengan indeks resmi sebelum disertakan. `provenance.json` mencatat URL, checksum arsip asli, metadata, dan anggota arsip yang disertakan.

Stopwords menyertakan bahasa Inggris dan Indonesia. Punkt dan Punkt Tab menyertakan model bahasa Inggris yang digunakan oleh `word_tokenize()` dengan pengaturan default. Tagger bahasa Inggris disertakan lengkap; [lisensi MIT upstream](https://github.com/sloria/textblob-aptagger/blob/master/LICENSE) tersedia dalam folder tagger. WordNet 3.0 disertakan sebagai arsip lengkap dengan lisensi dan copyright aslinya. README asli masing-masing resource dipertahankan.

Folder ini berisi data pendukung NLTK, bukan hasil feature engineering. Resource bahasa lain tidak disertakan.
