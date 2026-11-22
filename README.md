# Klasifikasi Digit MNIST Menggunakan K-Nearest Neighbors (KNN)

Proyek ini mendemonstrasikan bagaimana melatih, mengevaluasi, mengoptimalkan, dan mengintegrasikan model klasifikasi angka tulisan tangan (0-9) menggunakan dataset MNIST dan algoritma **K-Nearest Neighbors (KNN)**. Selain itu, proyek ini dilengkapi dengan antarmuka web interaktif berbasis **Gradio** agar pengguna dapat mencoba menggambar angka secara langsung.

---

## 1. Pendahuluan & Dataset
Eksperimen ini menggunakan dataset **MNIST (Modified National Institute of Standards and Technology)** yang legendaris, terdiri dari 70.000 gambar digital angka tulisan tangan berukuran $28 \times 28$ piksel.
- **Data Gambar (x):** Dimensi $(70000, 784)$ di mana setiap gambar diratakan (flatten) menjadi 784 fitur.
- **Label (y):** Target berupa angka dari `'0'` sampai `'9'`.

## 2. Pre-processing & Pembagian Data
Sebelum dilatih, data melalui beberapa tahapan persiapan:
- **Normalisasi:** Membagi nilai piksel asli ($0-255$) dengan `255.0` agar rentang nilainya menjadi $0.0 - 1.0$ untuk mempercepat komputasi jarak Euclidean.
- **Train-Test Split:** Membagi dataset menjadi 60.000 data latih (*training set*) dan 10.000 data uji (*test set*).

## 3. Pelatihan Model KNN Baseline
Pada tahap awal, model baseline dibuat dengan konfigurasi:
- Algoritma: KNN dengan $K = 3$ (`n_neighbors=3`).
- Metrik Jarak: *Euclidean Distance*.
- **Hasil Akurasi:** Mencapai **97.80%** pada 1.000 data uji pertama.

## 4. Analisis Kelemahan Model KNN (Pengujian Gagal)
Meskipun memiliki akurasi tinggi pada dataset uji standar, KNN sangat sensitif terhadap pergeseran spasial (*spatial translation*).
- **Eksperimen:** Ketika gambar digeser hanya sejauh 3 piksel ke kanan menggunakan `np.roll()`, perbandingan piksel-per-piksel menjadi tidak sejajar.
- **Dampak:** Model salah menebak angka asli `8` didekteksi sebagai `5`. Jarak Euclidean melonjak drastis hanya karena posisi gambar yang tidak pas di tengah.

## 5. Optimasi Model & Pre-processing Pintar
Untuk mengatasi kelemahan fatal di atas, diimplementasikan dua solusi utama:
1. **Optimasi Hyperparameter:** Menggunakan $K = 5$ dengan pembobotan berbasis jarak (`weights='distance'`).
2. **Smart Pre-processing (MNIST Standard):**
   - **Auto-Crop:** Memotong area kosong di sekeliling goresan tinta agar menyisakan angka saja.
   - **Proportional Resizing:** Mengubah ukuran angka secara proporsional agar sisi terpanjangnya menjadi 20 piksel (sesuai standar pembuatan dataset asli MNIST).
   - **Centering & Center of Mass:** Menempelkan hasil potongan ke kanvas baru berukuran $28 \times 28$ piksel dan menggesernya secara otomatis berdasarkan titik berat tinta menggunakan bantuan `scipy.ndimage.center_of_mass`.

## 6. Integrasi Web Demo (Gradio)
Aplikasi web dibuat interaktif menggunakan **Gradio**:
- **Input:** `gr.Sketchpad` yang memungkinkan pengguna mencoret angka di mana saja pada kanvas.
- **Output:** 
  1. Probabilitas klasifikasi dari model KNN yang telah dioptimalkan.
  2. Pratampilan gambar $28 \times 28$ hasil pemrosesan (Auto-Crop & Centering) untuk melihat bagaimana komputer 'melihat' coretan Anda.

## 7. Cara Menjalankan
1. Pastikan semua library yang dibutuhkan sudah terinstal:
   ```bash
   pip install gradio numpy scikit-learn scipy pillow matplotlib