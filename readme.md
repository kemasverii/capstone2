
# Analisis Airbnb Listings Bangkok

## 1. Business Understanding

Airbnb merupakan platform  penyedia akomodasi berbasis komunitas menghadirkan ribuan listing di kota besar Besar. Untuk memastikan optimalisasi performa listing dan sebaran wilayah yang merata, diperlukan pemahaman lebih dalam terhadap data listing, tipe kamar, harga, ketersediaan, dan performa.

Tujuan utama dari capstone ini adalah untuk:

- Menganalisis karakteristik listing berdasarkan aktivitas wilayah (jumlah listing per daerah).
- Menggali insight seputar distribusi harga, tipe kamar, ketersediaan, dan performa berdasarkan segmentasi wilayah.
- Memberikan rekomendasi bagi host dan manajemen Airbnb dalam mengembangkan strategi harga, lokasi, dan produk listing (Tipe ruangan/kamar).

## 2. Business Problem
Beberapa permasalahan utama yang ingin dijawab:

- Bagaimana distribusi listing Airbnb di Bangkok berdasarkan tingkat aktivitas wilayah?
- Apakah terdapat perbedaan harga dan tipe kamar berdasarkan tingkat aktivitas tersebut?
- Bagaimana ketersediaan dan performa listing bervariasi di setiap wilayah?
- Apa rekomendasi yang bisa diberikan kepada host untuk meningkatkan performa dan pemerataan listing?

## 3. Data Understanding

Dataset yang digunakan berasal dari open data Airbnb dengan fitur-fitur utama:

- **id** : id unik Airbnb untuk listing (daftar penginapan).    
- **name** : nama dari listing
- **host_id** : id unik Airbnb untuk host/pemilik
- **host_name** : nama host, biasanya hanya nama depan
- **neighbourhood** : wilayah atau lingkungan tempat listing berada, ditentukan oleh latitude dan longtitude
- **latitude** : garis lintang
- **longitude** : garis bujur
- **room_type** : tipe tempat penginapan (rumah,kamar bersama,rumah/apartement)
- **price** : harga harian dalam mata uang lokal.
- **number_of_reviews** : jumlah ulasan yang dimiliki oleh listing tersebut
- **last_review** : tanggal dari ulasan terakhir atau terbaru
- **calculated_host_listings_count** :jumlah listing yang dimiliki host di kota atau wilayah tersebut dalam data saat ini.
- **availability_365** : : ketersediaan properti dalam 365 hari ke depan
- **reviews_per_month** : review bulanan dari tiap tempat 
- **number_of_reviews_ltm** : jumlah ulasan yang diterima oleh listing dalam 12 bulan terakhir.

Dataset Airbnb Bangkok memiliki 17 fitur yang terdiri tiap fitur memiliki tipe data int, float dan object (biasanya merupakan teks). Namun terdapat kolom anomali yaitu Unnamed: 0 kolom ini nantinya akan dihapus dari dataset. Dataset ini juga memiliki baris data sebanyak 15854 data sehingga ukuran dataset ini 15854 baris x 16 kolom.

Berdasarkan statistik deskriptif dari kolom yang perlu diperhatikan ada di kolom price, terdapat harga listing yang bernilai 0. Penyewaan listing tidak mungkin 0, untuk itu akan dilakukan pengecekan pada data tersebut dan akan dilakukan pengahpusan atau tidak di analisis nanti.
![image-2](https://github.com/user-attachments/assets/6999dc88-9692-4053-8ed6-eb891256d732)

Berdasarkan pengecekan data, hanya 1 listing yang memilki harga 0. Pada kolom data lainnya, minimum_night, number_reviwes,last_review, hingga kolom calculated_host_list tidak ada informasi tambahan hanya nilai nan dan 0. Selain itu host yang mempunyai listing ini hanya satu saja listing yang dimiliki. Mungkin saja listing ini sudah off atau belum terjamah oleh wisatawan. Oleh karena itu data ini akan di drop dan dianggap anaomali
![image-1](https://github.com/user-attachments/assets/e2ee14ae-8152-4703-8a1a-55e822fff91d)


## 4. Data Preparation

Langkah-langkah persiapan data:
- Menghapus kolom unamed
  
  ![image-3](https://github.com/user-attachments/assets/14455135-a8be-4272-9476-ad3a71108c7e)



- Transformasi Tipe data kolom
  Berdasarkan pemahaman data sebelumnya, kolom last_review dapat diubah tipe datanya menjadi tipe data date time dan juga kolom price diubah tipe datanya menjadi numeric agar nanto bisa dilakukan operasi aritmatika
  ![image-4](https://github.com/user-attachments/assets/323f2836-3bcf-4b9b-86d4-43684b95bae2)


- Menghapus/mengabaikan nilai kosong dan ekstrem (outlier) pada harga dan availability.
  Terlihat kolom last_review dan reviews_per_month memiliki persentase missing value sebesar 36.5 % Selain itu kolom name memiliki persentase missing value sebesar 5%. Kolom host_name juga memiliki persentase missing value sebesar 0.006%
  ![image-6](https://github.com/user-attachments/assets/3b4505d4-ff0a-4b5b-bb48-8c64e7bd9934)


  - Missing value pada kolom **last_review** dan **reviews_per_month** dapat didrop atau tidak dilakukan drop.Kolom tersebut dapat dirop jika analisa tidak terlalu membutuhkan kolom tersebut,namun akan dicek terlebih dahulu apakah bisa langsung dihapus atau tidak.

  - Missing value pada kolom **name** dapat didrop agar konsistensi data tetap terjaga sehingga informasi yang nanti disampaikan terkati nama listing tidak membingungkan.

  - Missing value pada kolom **host_name** dapat didrop dikarenakan hanya terdapat 1 baris data saja yang memiliki missing value pada kolom tersebut. 
  
- Menghapus data duplikat
  Pengecekan duplikasi data di seluruh kolom, tidak ditemukan data duplikat. Namun hal ini belum tentu benar, perlu penggalian informasi lagi terkait data duplikat yang ada pada dataset.

  Ditemukan bahwa 1 host dapat memiliki lebih dari 1 listing, artinya bisa jadi ada potensi data duplikat untuk 1 host yang memiliki banyak listing.
  ![image-7](https://github.com/user-attachments/assets/23fe447a-4bf1-418d-a4bf-099329c2e3cd)

  
  Ditemukan bahwa 1 host dapat memiliki lebih dari 1 listing, artinya bisa jadi ada potensi data duplikat untuk 1 host yang memiliki banyak listing.
  
  Menggunakan kombinasi kolom **'name','host_name','host_id','neighbourhood'** ditemukan data duplikat sebanyak 873 baris data. Hal ini sesuai dengan pendugaan sebelumnya bahwa 1 host dapat memiliki lebih dari 1 listing. Namun pada permasalahan ini, data duplikasi tidak dihapus untuk memperkaya informasi data yang digunakan dianalisa mendalam.


## 📈 5. Analisis / Hasil Temuan
 > **Analisis Harga Penginapan Airbnb berdasarkan kelompok Neighbourhood Bangkok: Perbandingan Harga dan Tipe Kamar, Lokasi listing dan Ketersediaan Listing**

### a. Pengelompokkan Data Berdasarkan Neigbourhood:
  Tahapan awal untuk menganalisis kelompok aktivitas listing berdasarkan neighbourhood, dihitung terlebih dahulu jumlah listing yang ada di setiap neigbourhood. Setelah itu dilanjutkan dengan analisa-analisa mendalam setiap kelompok aktivitas listing

  ![image-8](https://github.com/user-attachments/assets/2553d582-6ef9-4082-9547-80509fc0925b)

  
  ![image-9](https://github.com/user-attachments/assets/939c3884-c690-4eac-b59e-6aba0d8e3b6c)


  Berdasarkan plot bar terlihat 5 Neighbourhood di Bangkok yang mempunyai total listing tertinggi, didapatkan daerah teramai listing yaitu di daerah vadhana dengan total listing yaitu 2151 listing dilanjut dengan 4 neighbourhood lainnya.

  Berdasarkan histogram jumlah listing per Neigbourhood di Bangkok didapatkan kesimpulan
  - Arah histogram **right-skewed** yang artinya sebagian besar neighbourhood di Bangkok memiliki jumlah listing yang relatif rendah.
  - Jumlah listing yang sangat besar, memiliki jumlah neighbourhoud yang rendah.

  Selanjutnya melakukan pengelompokkan daerah aktif listing. Pengelompokkan didasari oleh kuartil untuk membagi kelompok sesuai ukuran data dimana dibagi menjadi 3 kelompok, yaitu:

  - Rendah
  - Sedang 
  - Tinggi

  Pengelompokkan daerah aktif listing dibagi menjadi 3, berdasrakan persentase distribusi yaitu Q1, Q2, dan Q3.Selein itu untuk mempermudah intepretasi analisa lebih lanjut. 

![image-10](https://github.com/user-attachments/assets/c11ba347-e1ec-42f5-be85-9d59cc0e97c2)


Grafik diatas menunjukkan top 3 daerah neigbourhood disetiap kelompoknya, mulai dari aktivitas rendah, sedang hingga tinggi. Setiap kelompok ini nantinya akan dianalisis untuk melihat perbandingan harga dan tipe kamar. sehingga nantinya turis ataupun pengembang Airbnb dapat mengetahui perbedaan harga ditiap kelompok tersebut.

### b. **Analisis Harga Penginapan Berdasarkan Tingkat Listing**
Sebelum menganalisis lebih lanjut untuk setiap segmen kelas, kolom harga akan dianalisis terlebih dahulu secara umum. Setalah itu akan terdapat penambahan kolom: activity_level
  - **activity_level** : kelas aktivitas listing tiap data
  
![image-11](https://github.com/user-attachments/assets/f443a123-7306-4277-b299-a18a02cbe796)


Berdasarkan grafik di atas, kelompok listing tertinggi memiliki rata-rata harga sebesar 4353 BHT. Namun jika melihat total listing yang ada pada kelompok tinggi, banyaknya listing yang ada sangatlah sedikit dibandingkan dengan 2 kelompok lainnya yaitu kelompok Rendah dan Sedang.

Untuk memperjelas konteks harga pada masing-masing kelompok, dilakukan perbandingan tipe kamar/bagungan yang menjadi faktor mengapa harga bisa berbeda di tiap kelas aktivitas listing.

![image-12](https://github.com/user-attachments/assets/d49ef6ce-a9cd-428e-af04-de8ac4b05373)

Berdasarkan tabel deskripsi dan grafik di atas, tipe kamar yang menjadi faktor tingginya harga di kelompok aktivitas tinggi yaitu Entire home/apt. Dengan persentase sebesar 69 % dengan rata - rata harga 4322 THB dikelasnya sebagai faktor tingginya harga dikelas tersebut. Namun untuk tipe shared room, mendapatkan persentase yang sangat kecil sebesar 0.2% hal ini jelas jauh sekali nilainya persentasenya dibandingkan Entire home/apt

Untuk kelompok aktivitas sedang tipe Entire home/apt juga menjadi tipe room yang memiliki proporsi harga tertinggi sebesar 61 % dengan rata - rata harga 3327 THB.

Untuk kelompok rendah persentase harga dengan tipe kamar private room dan Entire home/apt memiliki harga yang nyaris mirip dengan masing-masing persentase dan harganya nya 47 %, 2778 THB dan 48 %, 2678 THB.

Berdasarkan informasi ini, didapatkan insight:
 > Tipe kamar Entire Home/apt menjadi tipe kamar termahal untuk setiap kelompok aktivitas listing. Baik daerah listing itu sepi atau tidak, harga tipe kamar Entire home/apt menjadi tipe termahal

### **c. Analisa Ketersedian Listing Berdasarkan Tingkat Listing**

Analisis ini bertujuan untuk melihat persebaran ketersedian listing di setiap kelompok aktivitas listing.

![image-13](https://github.com/user-attachments/assets/e1a22e3e-3572-4a31-a35c-bde530024543)


Berdasarkan boxplot di atas, persebaran jumlah hari di q1 untuk setiap kelompok aktivitas listing berbeda jauhh dimana ini mengindikasikan bahwa jumlah hari tersedia setiap kelompok berbeda. Kelompok listing Tinggi memiliki sebaran yang lebih luas yang dapat diartikan bahwa listing yang ada di kelompok ini, selalu dipesan oleh wisatawan atau di nonaktifkan oleh host.

Selain itu, kelompok listing aktivitas rendah terlihat memiliki sebaran yang tidak seluas 2 kelompok lainnya, yang meindikasikan bahwa kelompok listing ini selalu tersedia listingnya sepanjang tahun.

![image-14](https://github.com/user-attachments/assets/b50d6030-cf24-4be5-939f-e24d96bb4563)


![image-15](https://github.com/user-attachments/assets/73ad9b2c-d150-4cb4-9881-203252d6cced)


Berdasarlam grafik dan tabel proporsi untuk setiap kategori di tiap kelas aktivitas listing, didapatkan proporsi dari kelas listing tertinggi yaitu kategori ketersedian rendah. Hal ini disebabkan tingginya harga penginapan untuk kategori ketersedian rendah dengan harga 5272 THB. Besarnya proporsi untuk kategori aktivitas tinggi dengan ketersediaan rendah sebesar 41 %.

Pada kelas aktivitas sedang, ketersedian ruangan selama setahun penuh mayoritas ketersediannya sedang. Artinya listing yang ada dikelompok tersebut seimbang antara yang memesan dan tidak dipesan. Melihat dari rata-rata harga untuk setiap kategori ketersedian, ketersedian sedang berada di harga 3423 THB sebagai harga ketersedian tertinggi. Besarnya proporsi untuk kategori aktivitas sedang dengan ketersediaan ruangan sedang sebesar 39 %.

Pada kelas aktivitas rendah, ketersedian ruangan selama setahun penuh mayoritas ketersediaanya tinggi. Artinya listing yang ada di kelompok tersebut selalu tersedia ruangannya sepanjanga tahun. Melihat dari rata-rata harga untuk setiap kategori ketersedian, ketersedian sedang berada di harga 3175 THB sebaga harga ketersedian tertinggi. Besarnya proporsi untuk kategori aktivitas sedang dengan ketersedian ruangan tinggi yaitu 32 %


### **d. Visualisasi Geografis**
![image-16](https://github.com/user-attachments/assets/82d6ac41-97c6-477c-83ed-0562931bd02a)


Berdasarkan grafik geografis di atas, sebaran listing membentuk daerahnya sendiri. Terlihat darah berwana hijau merupakan titik tengah dari kumpulan kelompok lainnya.Titik ini merupakan **kelompok listing tinggi**, ha ini dapat menandakan bahwa pusat keramaian berada di daerah listing tersebut. Melebar ke samping, kumpulan warnanya adalah biru yang menandakan daerah **kelompok listing sedang** dan terakhir **kelompok listing rendah** berada di daerah terluar yang menandakan daerah tersebut kurang diminati untuk penggunaan listingnya walaupun tersebar di berbagai daerah di kelompok tersebut.

## 6. Kesimpulan 

Berdasarkan analisis yang telah dilakukan dan mendapatkan insight setiap analisinya, didapatkan kesimpulan

1. Sebagian besar neigbourhood memiliki jumlah listing yang kecil dikarenakan persebaran distribusinya lebih banyak dibawah 500 listing.
2. Pengelompokkan kategori listing berdasarkan jumlah listingnya disetiap neighbourhood dapat memudahkan intepretasi aktif tidaknya listing di kelompok tersebut.
3. Tipe kamar termahal yang didapatkan dari setiap kelas aktivitas list yaitu Entire Home/apt
4. Neighbourhood yang ramai melakukan listing diatas 1000 listing, terdapat di pusat persebaran listing yaitu Vadhana dan Khlong Thei. Hal ini dikarenakan 2 daerah tersebut ramai dikunjungi pengujung/wisatawaan sehingga banyak hot membuka listing di daerah tersebut.
5. Tipe kamar/ruangan yang tersedia di ketiga kelas aktivitas listing, didominasi oleh tipe Entire home/apt.
6. Ketersedian ruangan dapat dipengaruhi oleh harga listing dan juga tipe kamar yang dipesan oleh wisatawan

## 7. Rekomendasi

Rekomendasi yang dapat dikerahkan berdasarkan analisis dan insight yang didapatkan adalah:

1. Optimasi Strategi Harga Berdasarkan Tingkat Aktivitas Daerah
  
   >Host yang memiliki listing di daerah kelompok aktivitas listing rendah, dapat melakukan penyesuaian harga yang kompetitif atau menonjolkan kelebihan listing mereka.

2. Pengembangan Listing di Daerah Aktivitas Rendah
 
   > Pihak Airbnb ataupun pihak terkait lainnya dapat melakukan promosi ataupun iklan di daerah dengan tingkat aktivitas rendah, guna menarik wisatawan untuk menginap di daerah tersebut
3. Penyesuaian Tipe Kamar Berdasarkan Preferensi Pasar
   
   > Di wilayah dengan aktivitas tinggi dan sedang, host dapat mempertimbangkan menambah listing tipe “Private room” atau “Hotel room” untuk mengakomodasi pelancong hemat atau jangka pendek, apalagi jika berada di lokasi strategis.

4. Penyesuaian Ketersediaan (Availability) untuk Meningkatkan Performa
   
   > Host di daerah aktivitas tinggi dapat meningkatkan ketersediaan kalender agar peluang pemesanan lebih besar. Airbnb dapat memberikan notifikasi kepada host untuk membuka slot tanggal lebih luas.
