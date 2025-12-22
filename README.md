# Klasifikasi Suara Manusia berdasarkan Umur dan Gender menggunakan Metode FFT
Proyek UAS Pengolahan Sinyal Digital Semester 3 Sains Data Unesa

### Anggota Kelompok 4
1. Ananda Keissa Az Zahra (24031554051)
2. Laili Nurrohmatul Fadhila Z. (24031554093)
3. Eka Putri Maharani (24031554121)

## Tujuan Proyek
1. Mengetahui karakteristik frekuensi suara manusia berdasarkan umur dan gender menggunakan metode FFT.
2. Menerapkan metode FFT untuk mengekstraksi ciri frekuensi dari sinyal suara manusia.
3. Mengklasifikasikan suara manusia berdasarkan umur dan gender berdasarkan hasil ekstraksi FFT.

## Alur Kerja Proyek
Alur sistem pengolahan sinyal suara dalam penelitian ini terdiri dari tahapan sebagai berikut:

### Install Library

``` bash
!pip install librosa numpy pandas scipy soundfile pydub matplotlib
!apt-get install -y ffmpeg
!pip install xlsxwriter
```

``` bash
import os
import numpy as np
import matplotlib.pyplot as plt
import librosa
import librosa.display
import scipy.signal
from pydub import AudioSegment
from IPython.display import Audio, display
import warnings
```

1. Load sinyal suara
   
Tahap pertama awal dalam penelitian ini adalah memuat data suara dari berkas audio. Data suara yang digunakan merupakan sinyal suara manusia yang direkam dalam format     .wav. Pada tahap ini, sinyal suara masih di domain waktu belum mengalami proses pengolahan apa pun.

2. Pra-pemrosesan sinyal

``` bash
# Bandpass Filter(75-400 Hz)
def bandpass_filter(sinyal, sr, f_low=75, f_high=400):
    nyquist = 0.5 * sr
    b, a = scipy.signal.butter(
        4,
        [f_low / nyquist, f_high / nyquist],
        btype='band'
    )
    return scipy.signal.filtfilt(b, a, sinyal)

# Menghitung panjang FFT sebagai pangkat dua terdekat agar efisien secara komputasi.
def next_power_of_two(n):
    return 1 << (n - 1).bit_length() if n > 0 else 1
```

Pra-pemrosesan yang dilakukan meliputi:
- Penyaringan pita (bandpass filtering) pada rentang frekuensi 75–400 Hz
- Penghapusan bagian hening (silence trimming) untuk menghilangkan segmen sinyal yang tidak mengandung suara.
- Normalisasi amplitudo untuk menyamakan skala amplitudo sinyal sehingga mempermudah proses analisis frekuensi.

3. Transformasi domain waktu ke domain frekuensi menggunakan FFT 

``` bash
def ekstraksi_fitur_fft(sinyal, sr):
    n_fft_min = 4096
    n_fft = max(n_fft_min, next_power_of_two(len(sinyal)))

    # Padding jika sinyal lebih pendek dari n_fft
    if len(sinyal) < n_fft:
        sinyal = np.pad(sinyal, (0, n_fft - len(sinyal)))

    # FFT domain waktu → frekuensi
    hasil_fft = np.fft.fft(sinyal)
    magnitude = np.abs(hasil_fft[:n_fft // 2 + 1])
    frekuensi = np.fft.fftfreq(n_fft, d=1/sr)[:n_fft // 2 + 1]

    # Estimasi F0 dari puncak spektrum (50–1000 Hz)
    mask_valid = (frekuensi >= 50) & (frekuensi <= 1000)
    if np.any(mask_valid):
        idx_puncak = np.argmax(magnitude[mask_valid])
        f0 = frekuensi[mask_valid][idx_puncak]
    else:
        f0 = 0
``` 
Setelah melalui pra-pemrosesan, sinyal suara ditransformasikan dari domain waktu ke domain frekuensi menggunakan metode Fast Fourier Transform (FFT). FFT digunakan untuk menguraikan sinyal suara menjadi komponen frekuensi penyusunnya sehingga distribusi energi sinyal pada berbagai frekuensi dapat dianalisis. Transformasi FFT memungkinkan identifikasi karakteristik spektral suara manusia yang tidak dapat diamati secara langsung pada domain waktu.

4. Ekstraksi fitur spektral

``` bash
    # Spectral centroid
    if np.sum(magnitude) > 0:
        centroid = np.sum(frekuensi * magnitude) / np.sum(magnitude)
    else:
        centroid = 0

    # Spectral spread (bandwidth)
    spread = librosa.feature.spectral_bandwidth(
        S=magnitude.reshape(-1, 1),
        sr=sr
    )[0, 0]

    return {
        "F0": f0,
        "Centroid": centroid,
        "Spread": spread,
        "Magnitude": magnitude,
        "Frekuensi": frekuensi
    }
```

Dari hasil transformasi FFT, dilakukan ekstraksi fitur spektral utama yang digunakan sebagai dasar analisis suara, yaitu:
- Frekuensi dasar (F0), yang merepresentasikan frekuensi dominan pada sinyal suara dan berkaitan erat dengan karakteristik fisiologis pembicara. Diambil dari puncak tertinggi spektrum pada rentang 50–1000 Hz.
- Spectral centroid, yang menunjukkan pusat distribusi energi spektrum frekuensi yang mencerminkan warna suara.
- Spectral spread (bandwidth), yang menggambarkan sebaran energi spektrum terhadap centroid.

5. Pengelompokkan suara berbasis aturan frekuensi 

``` bash
def klasifikasi_suara(f0):
    if f0 < 50:
        return "Noise / Hening"

    if 210 <= f0 <= 270:
        return "Anak-anak Laki-laki"

    if 200 <= f0 < 210:
        return "Anak-anak Perempuan"
    if f0 >= 290:
        return "Anak-anak (frekuensi tinggi)"

    if 270 < f0 < 290:
        return "Anak-anak Perempuan"

    if 120 <= f0 <= 150:
        return "Dewasa Laki-laki (17–45 tahun)"

    if 200 <= f0 <= 280:
        return "Dewasa Perempuan (17–45 tahun)"

    if 100 <= f0 < 120:
        return "Lansia Laki-laki (45–65 tahun)"

    if 150 < f0 < 160:
        return "Lansia Laki-laki (45–65 tahun)"

    if 160 <= f0 <= 200:
        return "Lansia Perempuan (45–65 tahun)"

    return "Tidak terklasifikasi"
```
Pengelompokan suara dilakukan menggunakan pendekatan berbasis aturan dengan membandingkan nilai frekuensi dasar (F0) terhadap rentang frekuensi yang diperoleh dari berbagai jurnal ilmiah. Pengelompokan dilakukan untuk membedakan suara anak-anak, dewasa, dan lansia, serta mengidentifikasi perbedaan antara suara laki-laki dan perempuan. Adanya tumpang tindih rentang frekuensi antar kategori dianggap sebagai variasi alami suara manusia, sehingga hasil pengelompokan dipandang sebagai estimasi berbasis karakteristik frekuensi.Berdasarkan nilai F0 dan Spectral Centroid yang didapat, program menerapkan logika Rule-Based (If-Else) untuk menentukan kategori.

6. Visualisasi dan penyimpanan hasil analisis

Waveform
``` bash
    librosa.display.waveshow(sinyal_asli, sr=sr, alpha=0.5, label="Asli")
    librosa.display.waveshow(sinyal_bersih, sr=sr, alpha=0.8, label="Bersih")
    plt.title("Waveform Sinyal Suara")
    plt.legend()
    plt.grid()
```

FFT Spectrum
``` bash
    plt.subplot(3, 1, 2)
    plt.plot(fitur["Frekuensi"], fitur["Magnitude"])
    plt.axvline(fitur["F0"], color='r', linestyle='--', label=f'F0 = {fitur["F0"]:.1f} Hz')
    plt.xlim(0, 1000)
    plt.title("Spektrum Frekuensi (FFT)")
    plt.xlabel("Frekuensi (Hz)")
    plt.ylabel("Magnitudo")
    plt.legend()
    plt.grid()
```

Spectrogram
``` bash
    plt.subplot(3, 1, 3)
    S = librosa.stft(sinyal_bersih, n_fft=1024)
    S_db = librosa.amplitude_to_db(np.abs(S), ref=np.max)
    librosa.display.specshow(S_db, sr=sr, x_axis='time', y_axis='hz')
    plt.colorbar(format="%+2.0f dB")
    plt.title("Spectrogram")
    plt.tight_layout()
    plt.show()
```

   sumber 1: https://www.researchgate.net/publication/367729375_Application_Development_of_Male_and_Female_Voice_Differentiation_Based_on_Gender_Age_Range_Frequency_Class_Based_on_FFT_and_K-Means/fulltext/63da89c2c465a873a277248b/Application-Development-of-Male-and-Female-Voice-Differentiation-Based-on-Gender-Age-Range-Frequency-Class-Based-on-FFT-and-K-Means.pdf?origin=publication_detail&_tp=eyJjb250ZXh0Ijp7ImZpcnN0UGFnZSI6Il9kaXJlY3QiLCJwYWdlIjoicHVibGljYXRpb25Eb3dubG9hZCIsInByZXZpb3VzUGFnZSI6InB1YmxpY2F0aW9uIn19&__cf_chl_tk=tRB.9NHGeBYSjywzr2L8Zd67jZRmP8kMPhOCq9Jijkw-1764291620-1.0.1.1-dAMOwSVnmeJRmQYVIEBPTdbA1wGgGS9aWJP4nY50JAY

   sumber 2: https://media.neliti.com/media/publications/186539-ID-analisis-spektrum-suara-manusia-berdasar.pdf
