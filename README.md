# Klasifikasi Suara Manusia berdasarkan Umur dan Gender menggunakan Metode FFT
Proyek UAS Pengolahan Sinyal Digital Semester 3 Sains Data Unesa

### Anggota Kelompok 4
1. Ananda Keissa Az Zahra (24031554051)
2. Laili Nurrohmatul Fadhila Z. (24031554093)
3. Eka Putri Maharani (24031554121)

### Tujuan Proyek
1. Mengetahui karakteristik frekuensi suara manusia berdasarkan umur dan gender menggunakan metode FFT.
2. Menerapkan metode FFT untuk mengekstraksi ciri frekuensi dari sinyal suara manusia.
3. Mengklasifikasikan suara manusia berdasarkan umur dan gender berdasarkan hasil ekstraksi FFT.

### Alur Kerja Proyek
Alur sistem pengolahan sinyal suara dalam penelitian ini terdiri dari tahapan sebagai berikut:
1. Load sinyal suara
2. Konversi ke format yang sama 
3. Pra-pemrosesan sinyal
4. Transformasi domain waktu ke domain frekuensi menggunakan FFT 
5. Ekstraksi fitur spektral
6. Pengelompokkan suara berbasis aturan frekuensi yang ada di sumber referensi

   sumber 1: https://www.researchgate.net/publication/367729375_Application_Development_of_Male_and_Female_Voice_Differentiation_Based_on_Gender_Age_Range_Frequency_Class_Based_on_FFT_and_K-Means/fulltext/63da89c2c465a873a277248b/Application-Development-of-Male-and-Female-Voice-Differentiation-Based-on-Gender-Age-Range-Frequency-Class-Based-on-FFT-and-K-Means.pdf?origin=publication_detail&_tp=eyJjb250ZXh0Ijp7ImZpcnN0UGFnZSI6Il9kaXJlY3QiLCJwYWdlIjoicHVibGljYXRpb25Eb3dubG9hZCIsInByZXZpb3VzUGFnZSI6InB1YmxpY2F0aW9uIn19&__cf_chl_tk=tRB.9NHGeBYSjywzr2L8Zd67jZRmP8kMPhOCq9Jijkw-1764291620-1.0.1.1-dAMOwSVnmeJRmQYVIEBPTdbA1wGgGS9aWJP4nY50JAY

   sumber 2: https://media.neliti.com/media/publications/186539-ID-analisis-spektrum-suara-manusia-berdasar.pdf

7. Visualisasi dan penyimpanan hasil analisis
