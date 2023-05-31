# Capstone-Project-Module-3

# Konten:
1. Business Problem Understanding
2. Data Understanding
3. Data Preparation
4. Modelling
5. Conclusion
6. Recommendation

## Business Problem Understanding
1. Latar Belakang

Capital Bikeshare adalah sebuah perusahaan penyedia jasa penyewaan sepeda yang berbasis di Ibukota AS, Washington DC. Saat ini, Capital Bikeshare memiliki ketersediaan 5000 sepeda dan lebih dari 700 stasiun penyewaan yang tersebar di 7 wilayah. Penyewaan sepeda ini tidak hanya ditujukkan untuk penggunaan sehari-hari melainkan juga untuk wisatawan asing maupun lokal yang ingin menikmati pengalaman menjelajah Washington DC dengan mudah dan harga yang terjangkau.

2. Pernyataan Masalah

Penggunaan sepeda sebagai media transportasi harus mempertimbangkan kenyamanan dan keamanan saat berkendara. Salah satu pertimbangan tersebut adalah faktor cuaca. Faktor cuaca ini yang kemudian dapat menjadi salah satu faktor yang mempengaruhi minat seseorang dalam berkendara. Oleh karena itu, perusahaan ingin mengetahui faktor apa saja yang dapat mempengaruhi permintaan penyewaan sepeda.

3. Tujuan

Berdasarkan pernyataan masalah tersebut, Capital Bikeshare perlu untuk memiliki sistem yang dapat memperkirakan jumlah permintaan penyewa sepeda kedepannya.

4. Analytic Approach

Yang perlu kita lakukan adalah menganalisa pola-pola penggunaan sepeda pada setiap musim berdasarkan data yang ada. Kemudian kita akan membuat model regresi yang dapat membantu perusahaan untuk dapat memprediksi jumlah permintaan penyewaan sepeda.

5. Metric Evaluation

Ada tiga metode evaluasi metrik yang akan digunakan. Pertama, MAPE. MAPE mengukur rata-rata persentase selisih absolut antara nilai prediksi dan nilai asli. Semakin rendah nilai MAPE, semakin baik kinerja model regresi tersebut. Kedua, MAE. MAE mengukur rata-rata dari selisih absolut antara nilai prediksi dan nilai asli. Sama dengan MAPE, semakin rendah nilai MAE, semakin baik kinerja model regresi tersebut. Terakhir, RMSE. RMSE mengukur akar kuadrat rata-rata dari selisih kuadrat antara nilai prediksi dan nilai asli. RMSE memberikan gambaran tentang seberapa baik model dalam memprediksi dengan mempertimbangkan perbedaan antara nilai prediksi dan nilai asli. Semakin mendekati 1 nilai dari RMSE, semakin baik kinerja model regresi tersebut.

## Data Understanding

Dataset yang digunakan adalah data penggunaan sepeda Capital Bikeshare sepanjang tahun 2011 hingga 2012.


### Atribut data

| **Attribute** | **Data Type** | **Description** |
| --- | --- | --- |
| dteday | Object | Tanggal Penyewaan |
| hum | Float | Kelembaban Udara |
| weathersit | Int | Kondisi Cuaca (1-4) |
| holiday | Int | Hari Libur (0-1) |
| season | Int | Musim (1-4) |
| atemp | Float | Feeling Temprature |
| temp | Float | Temprature |
| hr | Int | Jam Penyewaan |
| casual | Int | Jumlah Pengguna Umum |
| registered | Int | Jumlah Pengguna Terdaftar |
| cnt | Int | Total Jumlah Pengguna |

Penjelasan Numerik:
1. weathersit :
  1. Cerah, cerah berawan
  2. Berkabut, mendung
  3. Sedikit bersalju, gerimis
  4. Hujan besar, hujan + petir, bersalju + berkabut

2. holiday :
  0. Bukan hari libur
  1. Hari libur

3. season :
  1. Winter
  2. Spring
  3. Summer
  4. Fall

Secara umum, kita dapat melihat bahwa pada data ini terdapat:

Dataset ini memiliki 11 kolom dan 12165 baris
Dataset ini memiliki tipe data object, int, dan float
Dataset ini memiliki 0% missing value


# Data Preparation

Pada bagian ini kita akan menyiapkan data agar dapat dilakukan modeling

Correlation

Melihat korelasi kolom non kategorikal terhadap kolom count

df_corr = df_copy[['hum','atemp', 'temp', 'hr', 'cnt']].corr()
df_corr

sns.heatmap(df_corr, annot= True)
plt.show()

Dari korelasi diatas, kita dapat melihat beberapa hal sebagai berikut:

temp, atemp, dan hr memiliki korelasi positif paling tinggi terhadap cnt
hum memiliki korelasi negatif paling rendah terhadap cnt
temp dan atemp memiliki korelasi yang positif paling tinggi terhadap satu sama lainnya. temperature adalah skala suhu yang bisa diukur, sementara atemp atau apparent temperatur adalah suhu yang lebih kompleks melibatkan kelembaban udara dankecepatan angin. karena kelembaban udara sudah diwakilkan oleh kolom hum, maka kita akan menghapus kolom temp.

Outliers

fig, axes = plt.subplots(2,2, figsize=(10, 10))
sns.boxplot(df_copy['temp'], ax = axes[0,0])
sns.boxplot(df_copy['hr'], ax = axes[0,1])
sns.boxplot(df_copy['hum'], ax = axes[1,0])
sns.boxplot(df_copy['cnt'], ax = axes[1,1])

axes[0,0].set_title('Temperature')
axes[0,1].set_title('Hour')
axes[1,0].set_title('Humidity')
axes[1,1].set_title('Count')
plt.show()

Dari visualisasi outliers diatas, kita dapat melihat bahwa tidak ada outliers pada kolom hour dan temperature. kemudian kita dapat melihat ada sedikit outliers pada humidity dan ada banyak pada count.

Berdasarkan pengetahuan umum, udara tidak dapat memiliki tingkat kelembaban 0% karena itu artinya tidak ada partikel air di udara. oleh karena itu kita akan menghapus baris yang memiliki nilai 0 pada kolom humidity.

Pada kolom count terdapat banyak outliers yang jika dihapus akan mempengaruhi data secara signifikan. oleh karena itu, kita tidak akan menghapus outliers pada kolom count.


pada kolom dteday berisi data type berupa object. kita akan merubah data type tersebut menjadi datetime dan akan membuat kolom baru (bulan dan tahun) sesuai data pada kolom dteday.

Setelah membuat kolom year, month dan day, kita akan mengapus kolom dteday. Selain itu, kita juga akan menghapus kolom casual dan registered karena sudah terwakilkan oleh kolom cnt

Terakhir, kita akan melakukan penamaan ulang pada kolom yang memiliki nama yang berupa singkatan dan atau sulit dipahami menjadi nama yang umum.
1. hum = humidity
2. cnt = count
3. temp = temperature
4. weathersit = weather
5. hr = hour

# Modelling

pada bagian ini, kita akan melakukan proses modeling sebagai berikut:
1. upload library yang dibutuhkan
2. melakukan proses encoding
3. split data memisahkan variable independen dengan variable dependen
4. melakukan train test split
5. melakukan pemilihan algoritma terbaik
6. melakukan prediksi test set pada algoritma terbaik
7. melakukan hyper parameter tuning algoritma terbaik
8. melakukan komparasi skor algoritma sebelum dan sesudah dilakukan tuning
9. melihat visualisasi nilai prediksi dan nilai asli
10. meilhat visualisasi fitur-fitur apa saja yang berpengaruh terhadap target


# Conclusion
1.berdasarkan modelling yang sudah dilakukan, kita dapat melihat bahwa fitur 'hour' menjadi yang paling berpengaruh terhadap 'count'

metrics evaluasi yang digunakan adalah nilai MAPE, MAE, dan RMSE dimana jika kita lihat bahwa performa pada nilai MAPE setelah hyper tuning adalah 35% yang bisa kita gunakan sebagai perkiraan jumlah pengguna dimana akan meleset sekitar 35% dari jumlah aslinya.

bila dilihat dari grafik actual vs predicted count, tidak terlalu bias antara hasil prediksi dengan jumlah aslinya.

model masih dapat diperbaiki lebih baik jika ditambahkan fitur fitur lain agar melengkapi data.

# Recommendation
penambahan fitur lain yang memiliki korelasi terhadap jumlah pengguna sangat disarankan. bisa menambahkan fitur seperti kecepatan angin, kepadatan lalu lintas, letak stasiun, dan lain sebagainya.

jika fitur-fitur seperti yang telah disebutkan ditambahkan ke dalam dataset, kita juga akan mungkin untuk melakukan analisa terhadap efektifitas masing-masing lokasi stasiun, atau meyiapkan jumlah sepeda pada stasiun-stasiun tertentu agar dapat memenuhi permintaan pengguna sepeda.
