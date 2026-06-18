## Klasifikasi Citra MRI Otak untuk Deteksi Tumor Menggunakan Ekstraksi Fitur Tekstur GLCM dan Metode KNN, SVM, dan Random Forest

### Nama Anggota
### FAUZAN HARI RAMDANI (F1D02410047)
### KAMRUN SYAH SYAHIDU (F1D02410011)
### WARITSA WULAN RAMADANIS (F1D02410136)
### MUHAMAD FARHAN MAULANA (F1D02410015)


Proyek ini merupakan implementasi Pengolahan Citra Digital (PCD) untuk melakukan klasifikasi citra medis ke dalam kategori **Normal** dan **Tumor**. Sistem dibangun menggunakan ekstraksi fitur tekstur *Gray Level Co-occurrence Matrix* (GLCM) dan tiga algoritma klasifikasi populer: *Random Forest*, *Support Vector Machine* (SVM), dan *K-Nearest Neighbors* (KNN).

---

## 1. Latar Belakang
Klasifikasi citra medis secara otomatis merupakan langkah krusial dalam membantu proses diagnosis klinis, khususnya untuk mendeteksi keberadaan tumor secara cepat dan akurat. Mengingat variasi visual dan noise pada citra medis (seperti MRI/CT Scan), analisis berbasis mata telanjang memiliki keterbatasan dalam hal konsistensi dan kecepatan. 

Melalui pendekatan **Pengolahan Citra Digital (PCD)** dan **Machine Learning**, proyek ini mengekstrak karakteristik tekstur citra yang tidak tertangkap dengan mudah oleh mata manusia menggunakan metode **GLCM**. Fitur-fitur statistik tekstur tersebut kemudian digunakan untuk melatih model klasifikasi agar mampu membedakan citra otak/jaringan medis yang normal dari yang memiliki tumor secara otomatis.

---

## 2. Deskripsi Program
Program ini diimplementasikan menggunakan bahasa pemrograman Python di lingkungan Jupyter Notebook. Pipeline program terdiri dari tahap-tahap berikut:
1. **Data Loading**: Membaca citra medis dari folder dataset, mengubah warna menjadi grayscale, dan menstandardisasi resolusi awal.
2. **Data Understanding**: Menganalisis sebaran jumlah sampel citra pada masing-masing kelas dan memvisualisasikan sampel citra.
3. **Preprocessing**: Menerapkan berbagai teknik perbaikan citra (seperti *Median Filter*, *Histogram Equalization*, dan *Operasi Morfologi*) untuk meningkatkan kualitas visual citra sebelum diekstraksi.
4. **Feature Extraction (GLCM)**: Menghitung matriks ko-okurensi GLCM pada empat arah sudut (0°, 45°, 90°, 135°) dengan jarak piksel $d=1$. Dari matriks tersebut, diekstraksi 7 fitur tekstur statistik:
   - *Contrast* (Kontras)
   - *Correlation* (Korelasi)
   - *Energy* (Energi)
   - *Homogeneity* (Homogenitas)
   - *Dissimilarity* (Ketidakmiripan)
   - *ASM* (*Angular Second Moment*)
   - *Entropy* (Entropi)
5. **Feature Selection**: Membuang fitur yang redundan dengan menyaring fitur yang memiliki nilai korelasi antar-fitur $\ge 0.95$.
6. **Data Splitting**: Membagi data menjadi data latih (*Training Set*) dan data uji (*Testing Set*).
7. **Feature Normalization**: Melakukan standardisasi skala fitur menggunakan teknik *Z-score Normalization* (Standardisasi).
8. **Modeling & Evaluation**: Melatih dan menguji performa model *Random Forest Classifier*, *Support Vector Machine (SVM) Classifier*, dan *K-Nearest Neighbors (KNN) Classifier* menggunakan metrik akurasi, presisi, recall, F1-score, serta visualisasi *Confusion Matrix*.

---

## 3. Penjelasan Dataset
Dataset diletakkan pada folder `dataset/` dengan struktur sebagai berikut:
```text
dataset/
├── normal/   # Berisi 98 citra medis jaringan normal
└── tumor/    # Berisi 98 citra medis jaringan tumor
```

- **Jumlah Kelas**: 2 Kelas (`normal` dan `tumor`).
- **Total Citra**: 196 citra.
- **Distribusi Kelas**: Seimbang (*balanced dataset*), masing-masing kelas memiliki 98 citra (50% normal, 50% tumor). Hal ini memastikan model tidak bias ke salah satu kelas selama proses pelatihan.
- **Format Citra**: Skala abu-abu (*grayscale*) dengan ukuran akhir 128×128 piksel setelah melalui tahap preprocessing.

---

## 4. Tahapan Preprocessing
Untuk menguji pengaruh kualitas citra terhadap performa klasifikasi, proyek ini membandingkan empat skenario preprocessing:

1. **Skenario Baseline**:
   - Citra hanya dikonversi ke grayscale dan diubah ukurannya (*resize*) menjadi 128×128 piksel tanpa filter tambahan.
2. **Percobaan 1 (Median Filter + Histogram Equalization)**:
   - Citra diubah menjadi grayscale dan di-resize ke 128×128 piksel.
   - **Median Filter (3×3)** diterapkan untuk mereduksi noise (seperti noise bintik/salt-and-pepper) tanpa mengaburkan bagian tepi citra.
   - **Histogram Equalization** (diimplementasikan secara manual dari nol) diterapkan untuk meratakan distribusi intensitas warna sehingga kontras citra meningkat secara optimal.
3. **Percobaan 2 (Morphological Closing)**:
   - Citra dikonversi ke grayscale dan di-resize ke 128×128 piksel.
   - **Thresholding** dengan batas intensitas (threshold) 127 diterapkan untuk mengubah citra menjadi biner (hitam-putih).
   - Operasi morfologi **Closing** (Dilasi diikuti Erosi) menggunakan kernel berukuran 5×5 diterapkan untuk menutup lubang-lubang kecil atau retakan di dalam area objek putih.
4. **Percobaan 3 (Morphological Opening + Closing)**:
   - Citra dikonversi ke grayscale dan di-resize ke 128×128 piksel.
   - Operasi morfologi **Opening** (Erosi diikuti Dilasi) menggunakan kernel 3×3 diterapkan untuk menghilangkan noise kecil di luar objek utama.
   - Operasi morfologi **Closing** (Dilasi diikuti Erosi) menggunakan kernel 3x3 diterapkan setelahnya untuk merapikan kontur objek dan menutup celah kecil di dalam objek.

---

## 5. Analisis Hasil Eksperimen

Berikut adalah ringkasan performa akurasi dari setiap skenario eksperimen pada data latih (*training*) dan data uji (*testing*):

### Tabel Perbandingan Akurasi Model

| Skenario Eksperimen | Pembagian Data (Train/Test) | Model Klasifikasi | Akurasi Training | Akurasi Testing | Preprocessing Utama |
| :--- | :---: | :--- | :---: | :---: | :--- |
| **Baseline** | 70 / 30 | Random Forest | 96.35% | 66.10% | Grayscale + Resize 128x128 |
| | | SVM (RBF) | 77.37% | 62.71% | |
| | | KNN (k=5) | 77.37% | 67.80% | |
| **Percobaan 1** | 75 / 25 | Random Forest | 95.92% | **73.47%** | Median Filter + Histogram Equalization |
| | | SVM (RBF) | 76.19% | **75.51%** | |
| | | KNN (k=5) | 83.67% | **67.35%** | |
| **Percobaan 2** | 75 / 25 | Random Forest | 93.88% | **75.51%** | Morphological Closing (Visualisasi saja)* |
| | | SVM (RBF) | 76.19% | **65.31%** | |
| | | KNN (k=5) | 75.51% | **63.27%** | |
| **Percobaan 3** | 80 / 20 | Random Forest | 97.44% | **72.50%** | Morphological Opening + Closing |
| | | SVM (RBF) | 75.64% | **70.00%** | |
| | | KNN (k=5) | 74.36% | **60.00%** | |

*\*Catatan Percobaan 2: Pada kode `percobaan2.ipynb`, operasi morfologi closing diimplementasikan dan divisualisasikan pada satu sampel, namun ekstraksi fitur akhir dijalankan langsung dari citra baseline dengan split data train/test sebesar 75/25.*

---

## 6. Analisis Hasil

- **Pengaruh Preprocessing**: Preprocessing memberikan dampak yang sangat signifikan terhadap akurasi. Pada model SVM, akurasi testing meningkat tajam dari **62.71% (Baseline)** menjadi **75.51% (Percobaan 1)** setelah ditambahkan *Median Filter* dan *Histogram Equalization*. Hal ini menunjukkan bahwa mereduksi noise dan menaikkan kontras membantu GLCM mengekstrak fitur tekstur pembeda yang lebih representatif.
- **Kestabilan Model SVM**: Model *Support Vector Machine* (SVM) dengan kernel RBF menunjukkan generalisasi terbaik dan paling stabil di antara model lainnya. Pada Percobaan 1, SVM memiliki perbedaan akurasi latih (76.19%) dan uji (75.51%) yang sangat kecil (0.68%), menandakan model bebas dari gejala overfitting yang parah.
- **Gejala Overfitting pada Random Forest**: Model *Random Forest* memiliki kecenderungan overfitting yang tinggi pada semua skenario eksperimen. Akurasi training mencapai 93%-97% namun akurasi testing turun menjadi 66%-75%. Hal ini disebabkan oleh jumlah estimator dan kedalaman pohon yang terlalu kompleks untuk ukuran dataset yang relatif kecil (196 citra).
- **Pengaruh Operasi Morfologi (Percobaan 3)**: Penggunaan kombinasi *Opening* dan *Closing* pada Percobaan 3 menghasilkan akurasi testing sebesar **72.50% (Random Forest)** dan **70.00% (SVM)**. Meskipun membantu menghaluskan objek biner, operasi morfologi berpotensi menghilangkan detail tekstur mikro (intensitas keabuan halus) yang sebenarnya dibutuhkan oleh GLCM untuk melakukan perhitungan korelasi dan entropi tekstur.
- **Performa KNN**: Model KNN dengan $k=5$ menunjukkan performa yang fluktuatif dan cenderung lebih rendah pada data uji dibanding model lainnya. Model berbasis jarak (distance-based) ini sensitif terhadap jumlah fitur dan skala fitur, sehingga akurasi terbaiknya hanya mencapai **67.80%** pada Baseline dan **67.35%** pada Percobaan 1.

---

## 7. Kesimpulan
1. Implementasi pengolahan citra digital untuk klasifikasi tumor otak/jaringan menggunakan GLCM dan machine learning berhasil dijalankan dengan baik.
2. Teknik preprocessing **Median Filter + Histogram Equalization (Percobaan 1)** terbukti merupakan skenario preprocessing terbaik karena mampu menghasilkan fitur GLCM yang berkualitas dan meningkatkan performa klasifikasi secara signifikan.
3. **Support Vector Machine (SVM)** dengan kernel RBF direkomendasikan sebagai model terbaik untuk sistem ini karena memiliki akurasi uji yang tinggi (**75.51%**) dan generalisasi yang sangat stabil (minim overfitting).
4. Untuk pengembangan selanjutnya, disarankan melakukan penyetelan hyperparameter (seperti membatasi `max_depth` pada Random Forest atau mencari nilai `k` optimal pada KNN), menguji kernel *sharpening* secara langsung pada dataset, serta menambahkan data dengan teknik *data augmentation* untuk menekan efek overfitting pada model-model kompleks.
