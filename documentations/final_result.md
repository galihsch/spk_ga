# Interpretasi Hasil Akhir Algoritma Genetika

## Deskripsi

Bagian ini menjelaskan cara menginterpretasikan hasil akhir dari algoritma genetika untuk masalah penjadwalan. Setelah algoritma selesai dijalankan, kita akan mendapatkan solusi terbaik berupa jadwal yang perlu didekodekan dan dianalisis untuk mendapatkan informasi yang berguna.

## Proses Pengambilan Hasil Akhir

```python
# 7. Tampilkan Hasil Akhir
print("\n# 7. Hasil Akhir Algoritma Genetika")
print("Jadwal Terbaik:", best_schedule)
print("Nilai Fitness Terbaik:", best_fitness)

# Decode jadwal terbaik
print("\nDecoding Jadwal Terbaik:")
decoded_schedule = []

for i, gene in enumerate(best_schedule):
    teacher_code = gene[:3]
    subject_code = gene[3:6]
    time_code = gene[6:]

    # Cari data lengkap
    teacher_name = next((item["Guru"] for item in data if item["Kd_Guru"] == teacher_code), "Unknown")
    subject_name = next((item["Mata_Pelajaran"] for item in data if item["Kd_Mata_Pelajaran"] == subject_code), "Unknown")
    time_slot = next((item["Jam"] for item in data if item["Kd_Jam"] == time_code), "Unknown")

    decoded_schedule.append({
        "Slot": i+1,
        "Gene": gene,
        "Guru": teacher_name,
        "Mata Pelajaran": subject_name,
        "Jam": time_slot,
        "Kode Guru": teacher_code,
        "Kode Mata Pelajaran": subject_code,
        "Kode Jam": time_code
    })

# Tampilkan jadwal terbaik dalam bentuk tabel
display(pd.DataFrame(decoded_schedule))

print("\nAnalisis Konflik pada Jadwal Terbaik:")
best_fitness, conflict_details, fitness_components = calculate_fitness(best_schedule)
display(pd.DataFrame(conflict_details))

print("\nKomponen Fitness dari Jadwal Terbaik:")
for key, value in fitness_components.items():
    if key != "Fitness Parsial":
        print(f"- {key}: {value}")

print("\nKontribusi Fitness Parsial:")
for key, value in fitness_components["Fitness Parsial"].items():
    print(f"- {key}: {value:.4f}")
```

## Elemen Hasil Akhir

### 1. Jadwal Terbaik (Dalam Bentuk Kromosom)

```
Jadwal Terbaik: ['T01M01W1', 'T02M02W2', 'T03M03W3', 'T04M04W4', 'T05M05W5']
```

- Merupakan representasi genetik dari solusi terbaik
- Setiap gen mengikuti format `KodeGuruKodeMapelKodeJam`
- Format ini perlu didekodekan untuk interpretasi manusia

### 2. Nilai Fitness Terbaik

```
Nilai Fitness Terbaik: 0.8923
```

- Nilai antara 0-1 yang mengindikasikan kualitas jadwal
- Semakin mendekati 1, semakin baik jadwal yang dihasilkan
- Merupakan hasil evaluasi dari fungsi fitness multi-kriteria

### 3. Jadwal Terbaik (Dalam Bentuk Tabel)

| Slot | Gene | Guru | Mata Pelajaran | Jam | Kode Guru | Kode Mata Pelajaran | Kode Jam |
|------|------|------|----------------|-----|-----------|---------------------|----------|
| 1 | T01M01W1 | May | PAI | 7.30-08.05 | T01 | M01 | W1 |
| ... | ... | ... | ... | ... | ... | ... | ... |

- Representasi jadwal yang mudah dibaca manusia
- Menampilkan informasi lengkap tentang guru, mata pelajaran, dan waktu
- Mengkonversi kode-kode dalam gen menjadi informasi deskriptif

### 4. Analisis Konflik

| Gene | Teacher | Subject | Time | Conflict |
|------|---------|---------|------|----------|
| T01M01W1 | T01 | M01 | W1 | No |
| ... | ... | ... | ... | ... |

- Menunjukkan apakah ada konflik jadwal (guru mengajar di waktu yang sama)
- "No" berarti tidak ada konflik, "Yes" berarti ada konflik
- Jadwal optimal seharusnya tidak memiliki konflik

### 5. Komponen Fitness

```
Komponen Fitness dari Jadwal Terbaik:
- Konflik Jadwal: 0
- Jadwal Berturutan: 1
- Ketidakseimbangan Mapel: 0.0
- Mapel Berat di Jam Akhir: 0

Kontribusi Fitness Parsial:
- Konflik: 1.0000
- Berturutan: 0.5000
- Ketidakseimbangan: 1.0000
- Mapel Berat di Akhir: 1.0000
```

- Menampilkan rincian dari setiap komponen yang berkontribusi pada nilai fitness
- Nilai pelanggaran untuk setiap kriteria evaluasi
- Kontribusi parsial (ternormalisasi) dari setiap kriteria ke nilai fitness total

## Cara Interpretasi Hasil

### 1. Kualitas Jadwal Keseluruhan

- **Nilai Fitness Tinggi (>0.8)**: Jadwal sangat baik, memenuhi hampir semua kriteria
- **Nilai Fitness Menengah (0.5-0.8)**: Jadwal cukup baik tapi masih ada ruang perbaikan
- **Nilai Fitness Rendah (<0.5)**: Jadwal kurang optimal, banyak batasan tidak terpenuhi

### 2. Analisis Konflik

- **Tidak Ada Konflik**: Setiap guru hanya dijadwalkan pada satu slot waktu yang unik
- **Ada Konflik**: Perlu revisi jadwal atau pertimbangan penambahan guru/waktu

### 3. Komponen Fitness

- **Konflik Jadwal**: Nilai 0 berarti tidak ada konflik (ideal)
- **Jadwal Berturutan**: Nilai rendah menunjukkan minimal jadwal berturutan untuk guru
- **Ketidakseimbangan Mapel**: Nilai rendah menunjukkan distribusi mata pelajaran seimbang
- **Mapel Berat di Jam Akhir**: Nilai rendah menunjukkan mata pelajaran berat ditempatkan di awal hari

## Penggunaan Praktis Hasil

1. **Pembuatan Jadwal Final**:
   - Implementasikan jadwal sesuai dengan tabel hasil dekode
   - Pastikan informasi lengkap tentang guru, mata pelajaran, dan waktu

2. **Identifikasi Area Perbaikan**:
   - Periksa komponenen fitness dengan nilai kontribusi terendah
   - Fokus perbaikan manual pada aspek tersebut jika diperlukan

3. **Validasi dengan Stakeholder**:
   - Diskusikan jadwal dengan guru untuk konfirmasi ketersediaan waktu
   - Pertimbangkan preferensi guru yang mungkin belum tercakup dalam algoritma

4. **Analisis Parameter Algoritma**:
   - Evaluasi apakah perlu meningkatkan jumlah generasi atau ukuran populasi
   - Pertimbangkan penyesuaian bobot pada fungsi fitness berdasarkan prioritas

## Kesimpulan

Hasil akhir algoritma genetika memberikan solusi penjadwalan yang:

1. **Optimal atau Mendekati Optimal**: Berdasarkan kriteria dan batasan yang ditetapkan
2. **Komputasional Efisien**: Menemukan solusi baik dalam waktu yang wajar
3. **Multifaktor**: Mempertimbangkan berbagai aspek penjadwalan secara simultan
4. **Dapat Dianalisis**: Memberikan insight tentang kualitas solusi dan area perbaikan

Meski demikian, hasil algoritma sebaiknya digunakan sebagai rekomendasi yang dapat disesuaikan oleh pengambil keputusan manusia berdasarkan pertimbangan tambahan yang mungkin tidak tercakup dalam model.
