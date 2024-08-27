# Laporan Proyek Machine Learning Terapan (Recommendation System) - Nurul Fatimah

## Project Overview
Di era digital saat ini, dengan akses informasi yang sangat luas, menemukan buku yang sesuai dengan preferensi individu dapat menjadi tantangan. Dengan ribuan judul buku yang tersedia, baik dalam format fisik maupun digital, pembaca sering kali merasa kesulitan untuk menemukan buku yang benar-benar memenuhi minat mereka. Dari permasalahan tersebut, maka diperlukan sebuah sistem untuk menyelesaikannya dalam hal ini seperti sistem rekomendasi. Proyek sistem rekomendasi buku dirancang untuk mengatasi masalah ini dengan memberikan saran yang relevan dan personal kepada pengguna berdasarkan preferensi mereka dan perilaku pembaca lain yang memiliki minat serupa serta pengguna dapat melihat buku yang paling populer berdasarkan rating yang diberikan. 

## Business Understanding
### Problem Statements:

Berdasarkan pada latar belakang di atas, permasalahan yang dapat diselesaikan pada proyek ini adalah bagaimana membangun suatu sistem rekomendasi buku menggunakan metode collaborative filtering guna untuk mempermudah pengguna memperoleh rekomendasi sesuai kebutuhannya.

### Goals:
Tujuan proyek ini adalah untuk mempermudah pengguna dalam menemukan buku yang diinginkan seperti memberikan rekomendasi buku terpopuler serta rekomendasi yang dipersonalisasi dari orang-orang yang memiliki minat membaca yang serupa.

### Solution Statement
Solusi yang diajukan yaitu dengan menggunakan collaborative filtering  untuk membangun sistem rekomendasi berdasarkan cara pengguna menilai buku dengan beberapa pertimbangan seperti pengguna yang telah menilai setidaknya 200 buku dan hanya akan memasukkan buku yang sudah menerima 50 rating.  

## Data Understanding
Data yang digunakan berasal dari Kaggle dengan nama Book Recommendation Dataset atau dapat didownload [disini]([https://www.kaggle.com/datasets/vibgyor24/new-york-ecommerce-customers/data](https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset/data)).
Dalam proyek ini, terdapat 3 dataset yang akan digunakan yaitu data buku, rating, dan user.

- Data Buku 
  - Dataset berdimensi 271360 rows × 8 columns
   
    ![image](https://github.com/user-attachments/assets/c8392e5d-3389-4975-b972-5516271ae6a5)

  - Semua fitur bertipe object

    ![image](https://github.com/user-attachments/assets/bc97673b-9623-4dcc-b862-2ae340595637)
 
  - Terdapat nilai kosong dalam column Book-Author sebanyak 2, Publisher sebanyak 2, dan Image-URL-L sebanyak 3 raw.
   
    ![image](https://github.com/user-attachments/assets/7e0f233b-1ebb-429c-a3fa-00e99522bdf6)
     
  - Fitur dataset buku
    - ISBN: Nomor Standar Buku Internasional yang unik untuk setiap buku. Ini adalah kunci utama untuk mengidentifikasi buku.
    - Book-Title: Judul buku.
    - Book-Author: Nama penulis buku.
    - Year-Of-Publication: Tahun publikasi buku.
    - Publisher: Penerbit buku.
    - Image-URL-S: URL untuk gambar sampul buku dengan ukuran kecil.
    - Image-URL-M: URL untuk gambar sampul buku dengan ukuran sedang.
    - Image-URL-L: URL untuk gambar sampul buku dengan ukuran besar.
   
- Data Rating 
  - Dataset berdimensi 1149780 rows × 3 columns
   
    ![image](https://github.com/user-attachments/assets/5c5a84f1-bfe7-4bd2-a82c-f84d065b621e)

  - Terdapat 2 fitur bertipe int64 yaitu fitur User-ID dan Book-Rating, dan 1 fitur bertipe object yaitu ISBN 

    ![image](https://github.com/user-attachments/assets/c5ebfd3e-79f1-4f6e-b4bc-4d96cd0dcc1d)

   - Tidak ditemukan nilai kosong/missing value
   
      ![image](https://github.com/user-attachments/assets/81714f33-cff0-4e22-8449-53fa0380244e)
     
  - Fitur dataset rating
    - User-ID: ID unik untuk setiap pengguna. Ini berfungsi sebagai kunci utama untuk mengidentifikasi pengguna.
    - ISBN: Merujuk ke ISBN dalam tabel buku, menghubungkan penilaian dengan buku yang spesifik.
    - Book-Rating: Nilai atau rating yang diberikan oleh pengguna untuk buku tersebut, biasanya dalam skala 1 hingga 10.
   
- Data User
  - Dataset berdimensi 278858 rows × 3 columns
   
    ![image](https://github.com/user-attachments/assets/9bc85957-36cc-45ff-b2d4-d7f923ac1359)

  - Terdapat fitur bertipe int64 yaitu fitur User-ID, fitur bertipe object yaitu fitur Location, dan fitur bertipe float64 yaitu fitur Age

    ![image](https://github.com/user-attachments/assets/74c04be7-e90d-4381-b603-608995a4c528)

   - Terdapat nilai kosong pada column Age sebanyak 110762
   
     ![image](https://github.com/user-attachments/assets/2a5c9438-648b-41e9-ac19-73b7f2ca40a0)

     
  - Fitur dataset user
    - User-ID: ID unik untuk setiap pengguna, yang juga digunakan dalam tabel ratings untuk mengidentifikasi siapa yang memberi rating.
    - Location: Lokasi geografis pengguna, biasanya berupa kota, negara bagian, dan negara.
    - Age: Usia pengguna.

## Data Preparation
-	Menggabungkan dua DataFrame, yaitu df_ratings dan df_books, berdasarkan kolom ISBN dengan perintah merge yang disimpan dalam variabel books_rating.
-	Mengecek missing value dari books_rating, yang kemudian dilakukan pembersihan missing value dari books_rating dengan perintah .dropna(). Setelah dilakukan pembersihan di cek kembali untuk memastikan sudah tidak ada missing value dikarenakan misisng value tersebut dapat mempengaruhi hasil analisisnya untuk mencari buku yang terpopuler berdasarkan jumlah rating dari pengguna.
-	Mengelompokkan data books_rating berdasarkan kolom User-ID dengan perintah .groupby() dan menghitung jumlah ratingnya menggunakan .count(), agar nantinya kita bisa melakukan pengecekan apakah jumlah rating yang diberikan oleh setiap pengguna, dalam hal ini lebih besar dari 200
-	Memfilter pengguna yang aktif memberikan rating dan menyimpannya dalam variabel rating_filter
-	Mengelompokkan data rating_filter berdasarkan kolom Book-Title dengan perintah .groupby() dan menghitung jumlah rating yang diterima oleh setiap buku, agar nantinya kita bisa melakukan pengecekan apakah jumlah rating yang diberikan oleh setiap pengguna, dalam hal ini lebih besar sama dengan dari 50
-	Memfilter buku yang sudha menerima rating lebih dari sama dengan 50 dari pengguna dan menyimpannya dalam variabel result_rate
-	Membuat tabel pivot dengan judul buku sebagai indeks, ID pengguna sebagai kolom, dan rating buku sebagai nilai

## Modelling and Result

Collaborative filtering adalah metode dalam sistem rekomendasi yang memprediksi preferensi pengguna dengan menganalisis interaksi pengguna dengan item lain yang dalam proyek ini kita akan mengetahui bagaimana cosine similarity digunakan untuk menghitung kesamaan antara item.. 

**Cosine similarity** adalah ukuran yang digunakan untuk menentukan seberapa mirip dua vektor dalam ruang n-dimensi. Dalam konteks sistem rekomendasi, vektor biasanya mewakili item, di mana setiap elemen vektor adalah rating dari pengguna yang berbeda.

![image](https://github.com/user-attachments/assets/5660a1fa-7e92-428b-8310-f13fae1f8990)

Nilai cosine similarity berkisar antara -1 (sangat tidak mirip) hingga 1 (sangat mirip), dengan 0 menunjukkan tidak adanya kesamaan.

Untuk membangun sistem rekomendasi berdasarkan cara pengguna menilai buku, memerlukan dua dataset: satu berisi informasi tentang buku dan yang lainnya berisi peringkat pengguna. Sehingga akan dilakukan penggabungan dataset ini untuk membuat dataset baru.

Namun, untuk fokus pada pembaca berpengalaman, hanya akan mempertimbangkan pengguna yang telah menilai setidaknya 200 buku. Hal ini memastikan bahwa sistem ini mempertimbangkan pendapat pengguna yang memiliki sejarah membaca dan menilai buku yang substansial.

Selain itu, sistem ini hanya akan memasukkan buku yang telah menerima minimal 50 rating. Dengan demikian, akan memprioritaskan buku yang telah diulas oleh sejumlah besar pembaca, yang menunjukkan tingkat popularitas atau perhatian yang lebih tinggi.

**Hasil Rekomendasi**

Untuk mendapatkan rekomendasi disini saya menggunakan buku yang berjudul 'Year of Wonders' yang kemudian akan menampilkan 5 rekomendasi buku yang sesuai kriteria yang sudah disebutkan tadi dan buku yang paling mirip dengan buku yang dimaksud berdasarkan kemiripan cosine. 

![image](https://github.com/user-attachments/assets/3fa59f12-1572-4b35-b0a6-71bb1b7905db)


## Evaluation
Metrik evaluasi yang digunakan adalah Mean Average Precision (MAP). MAP (Mean Average Precision) mengukur kinerja sistem rekomendasi dengan menghitung rata-rata dari precision di berbagai posisi relevan dalam daftar rekomendasi. Precision diukur pada setiap posisi relevan dalam daftar rekomendasi dan kemudian dirata-ratakan untuk seluruh item.

![image](https://github.com/user-attachments/assets/189dd863-55bf-41f1-90d9-08a36a64d9ea)

![image](https://github.com/user-attachments/assets/da650b99-a23d-4049-afa3-e470ac6401ec)

![image](https://github.com/user-attachments/assets/f8fa98ac-3590-4feb-8441-0eebedeaa21f)


Fungsi ini mengukur kualitas peringkat rekomendasi dengan menghitung precision pada berbagai posisi dan kemudian rata-ratanya.

**Kesimpulan :**

Hasil MAP sebesar 78.51% menunjukkan bahwa sistem rekomendasi ini memiliki performa yang baik dalam hal menyajikan rekomendasi relevan di posisi yang lebih tinggi dalam daftar. Ini adalah indikator positif bahwa sistem dapat memberikan nilai tambah yang signifikan kepada pengguna dengan menampilkan item relevan secara efektif. Dari hal tersebut dapat disimpulkan bahwa proyek ini berhasil mencapai tujuannya karena berhasil memberikan rekomendasi sesuai syarat yang sudah disebutkan dan bisa mengetahui buku-buku yang populer berdasarkan jumlah rating yang diberikan.


  
 

