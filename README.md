# Struktur Proyek Algoritma Genetika untuk Penjadwalan

Proyek ini mengimplementasikan algoritma genetika untuk menyelesaikan masalah penjadwalan mata pelajaran dan guru. Algoritma genetika dipilih karena kemampuannya dalam menyelesaikan masalah optimasi kompleks dengan banyak batasan.

## Tujuan Proyek

Proyek ini bertujuan untuk:

1. Membuat jadwal mata pelajaran yang optimal
2. Memastikan tidak ada konflik jadwal (satu guru mengajar di waktu yang sama)
3. Mempertimbangkan berbagai batasan dan preferensi (jadwal berurutan, distribusi mata pelajaran, penempatan mata pelajaran berat)

## Struktur File

Proyek terdiri dari satu file utama `tugas_spk.py` yang berisi implementasi lengkap algoritma genetika dengan komponen-komponennya:

1. **Data Awal**: Mendefinisikan data guru, mata pelajaran, dan slot waktu
2. **Pembangkitan Populasi**: Menghasilkan populasi awal secara acak namun terstruktur
3. **Evaluasi Fitness**: Menghitung nilai fitness berdasarkan berbagai kriteria
4. **Seleksi**: Memilih individu terbaik menggunakan metode roulette wheel
5. **Crossover**: Melakukan pindah silang antar individu terpilih
6. **Mutasi**: Mengubah komponen kromosom secara acak untuk menjelajahi ruang pencarian baru
7. **Algoritma Utama**: Mengintegrasikan semua komponen dalam proses evolusioner

## Representasi Data

### Data Input

Data input berupa informasi tentang:

- Guru (kode dan nama)
- Mata pelajaran (kode dan nama)
- Slot waktu (kode dan rentang jam)

### Representasi Genetika

- **Kromosom**: Merepresentasikan jadwal lengkap berisi beberapa gen
- **Gen**: Merepresentasikan satu slot jadwal dengan format `KodeGuruKodeMapelKodeWaktu`
  Contoh: "T01M01W1" berarti guru dengan kode T01 mengajar mata pelajaran M01 pada slot waktu W1

## Alur Eksekusi

1. Inisialisasi populasi awal
2. Evaluasi fitness setiap individu
3. Seleksi individu terbaik untuk reproduksi
4. Lakukan crossover/pindah silang
5. Lakukan mutasi untuk menjaga keragaman
6. Ulangi langkah 2-5 untuk sejumlah generasi
7. Ambil jadwal terbaik dari populasi akhir

## Visualisasi Hasil

Hasil akhir ditampilkan dalam bentuk:

- Jadwal lengkap dengan informasi guru, mata pelajaran, dan waktu
- Analisis konflik untuk memvalidasi solusi
- Komponen fitness untuk menunjukkan kualitas jadwal dari berbagai aspek
