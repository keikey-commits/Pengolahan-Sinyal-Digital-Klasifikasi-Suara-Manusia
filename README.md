# Klasifikasi Suara Manusia berdasarkan umur dan gender menggunakan metode FFT
Proyek UAS Pengolahan Sinyal Digital Semester 3 Sains Data Unesa

### Anggota Kelompok 12
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
   ```bash
   !gdown --folder "https://drive.google.com/drive/folders/1Z4ijYI6ATbnAhUOJGUnxMeB6U8snR2GL"
   ```
2. Konversi ke format yang sama 
3. Pra-pemrosesan sinyal
   ```bash
   # library
   import os
   import numpy as np
   import matplotlib.pyplot as plt
   import librosa
   import librosa.display
   import scipy.signal
   from pydub import AudioSegment
   import io
   from IPython.display import Audio, display
   import warnings
   ```
   
5. Transformasi domain waktu ke domain frekuensi menggunakan FFT 
6. Ekstraksi fitur spektral
7. Pengelompokkan suara berbasis aturan frekuensi yang ada di sumber referensi
      ```bash
   def classify(feats):
       f0 = feats['F0_Hz']
       cent = feats['Centroid']
   
       prediksi_gender_temp = "Tidak Diketahui"
       prediksi_usia_temp = "Tidak Diketahui"
       kategori_suara_gabungan = "Tidak Diketahui"
       keterangan_depkes = "-"
   
       if f0 < 50:
           return "Noise / Hening", "-"
   
       if f0 >= 240:
           prediksi_usia_temp = "Balita"
           if f0 > 320:
               kategori_suara_gabungan = "Balita"
               keterangan_depkes = "1. Balita (0 - 5 tahun)"
           else: # 240 <= F0 <= 320
               if cent > 2800:
                   prediksi_usia_temp = "Anak"
                   prediksi_gender_temp = "Laki/Perempuan"
                   kategori_suara_gabungan = "Anak-anak (Laki/Perempuan)"
                   keterangan_depkes = "2. Anak-anak (6 - 16 tahun)"
               else:
                   kategori_suara_gabungan = "Balita"
                   keterangan_depkes = "1. Balita (0 - 5 tahun)"
       elif 165 <= f0 < 240:
           prediksi_gender_temp = "Perempuan"
           prediksi_usia_temp = "Dewasa"
           if f0 < 190 and cent < 2000:
               prediksi_usia_temp = "Lansia"
               kategori_suara_gabungan = "Lansia Perempuan"
               keterangan_depkes = "4. Lansia (46 - 65 tahun)"
           else:
               kategori_suara_gabungan = "Wanita Dewasa"
               keterangan_depkes = "3. Dewasa (17 - 45 tahun)"
       elif 85 <= f0 < 165:
           prediksi_gender_temp = "Laki-laki"
           prediksi_usia_temp = "Dewasa"
           if f0 < 110:
               prediksi_usia_temp = "Lansia"
               kategori_suara_gabungan = "Lansia Laki-laki"
               keterangan_depkes = "4. Lansia (46 - 65 tahun)"
           else:
               kategori_suara_gabungan = "Dewasa Laki-laki"
               keterangan_depkes = "3. Dewasa (17 - 45 tahun)"
       elif f0 < 85:
           prediksi_usia_temp = "Lansia"
           prediksi_gender_temp = "Tidak Spesifik"
           kategori_suara_gabungan = "Lansia"
           keterangan_depkes = "4. Lansia (> 65 tahun)"
   
       return kategori_suara_gabungan, keterangan_depkes
   ```

   sumber 1: https://www.researchgate.net/publication/367729375_Application_Development_of_Male_and_Female_Voice_Differentiation_Based_on_Gender_Age_Range_Frequency_Class_Based_on_FFT_and_K-Means/fulltext/63da89c2c465a873a277248b/Application-Development-of-Male-and-Female-Voice-Differentiation-Based-on-Gender-Age-Range-Frequency-Class-Based-on-FFT-and-K-Means.pdf?origin=publication_detail&_tp=eyJjb250ZXh0Ijp7ImZpcnN0UGFnZSI6Il9kaXJlY3QiLCJwYWdlIjoicHVibGljYXRpb25Eb3dubG9hZCIsInByZXZpb3VzUGFnZSI6InB1YmxpY2F0aW9uIn19&__cf_chl_tk=tRB.9NHGeBYSjywzr2L8Zd67jZRmP8kMPhOCq9Jijkw-1764291620-1.0.1.1-dAMOwSVnmeJRmQYVIEBPTdbA1wGgGS9aWJP4nY50JAY

   sumber 2: https://media.neliti.com/media/publications/186539-ID-analisis-spektrum-suara-manusia-berdasar.pdf
9. Visualisasi dan penyimpanan hasil analisis
