# Fungsi Mutate

## Deskripsi

Fungsi `mutate()` mengimplementasikan operasi mutasi dalam algoritma genetika. Mutasi berperan penting dalam memperkenalkan keragaman genetik dengan memodifikasi gen secara acak dalam kromosom. Dalam konteks penjadwalan, mutasi membantu menjelajahi ruang solusi baru yang tidak dapat ditemukan melalui crossover saja.

## Implementasi

```python
def mutate(chromosome, mutation_rate=0.1, display_details=False):
    """
    Melakukan mutasi dengan pertukaran slot waktu antar gen.
    Ini mempertahankan validitas jadwal dengan hanya menukar jam.
    """
    original_chromosome = chromosome.copy()
    mutation_details = []

    for i in range(len(chromosome)):
        if random.random() < mutation_rate:
            # Pilih gen kedua secara acak untuk menukar jam
            j = random.randint(0, len(chromosome) - 1)
            while j == i:  # Pastikan berbeda dengan gen pertama
                j = random.randint(0, len(chromosome) - 1)
            
            # Pecah gen menjadi komponen-komponennya
            gene1 = chromosome[i]
            gene2 = chromosome[j]
            
            teacher1 = gene1[:3]
            subject1 = gene1[3:6]
            time1 = gene1[6:]
            
            teacher2 = gene2[:3]
            subject2 = gene2[3:6] 
            time2 = gene2[6:]
            
            # Tukar kode jam
            new_gene1 = teacher1 + subject1 + time2
            new_gene2 = teacher2 + subject2 + time1
            
            # Update kromosom
            chromosome[i] = new_gene1
            chromosome[j] = new_gene2
            
            mutation_details.append({
                "Posisi Gen 1": i+1,
                "Posisi Gen 2": j+1,
                "Gen 1 Asli": gene1,
                "Gen 2 Asli": gene2,
                "Gen 1 Hasil Mutasi": new_gene1,
                "Gen 2 Hasil Mutasi": new_gene2
            })

    if display_details:
        print("\nDetail Mutasi:")
        print(f"Kromosom Asli: {original_chromosome}")
        print(f"Kromosom Setelah Mutasi: {chromosome}")

        if mutation_details:
            print("\nGen yang Dimutasi:")
            display(pd.DataFrame(mutation_details))
        else:
            print("Tidak ada gen yang mengalami mutasi.")

    return chromosome
```

## Parameter

- `chromosome` (list): Kromosom yang akan dimutasi
- `mutation_rate` (float, default=0.1): Probabilitas mutasi untuk setiap gen
- `display_details` (bool, default=False): Flag untuk menampilkan detail proses mutasi

## Teknik Mutasi: Pertukaran Slot Waktu

Implementasi ini menggunakan teknik pertukaran (swap mutation) khusus yang:

1. **Mempertahankan Struktur**: Hanya menukar slot waktu antar gen, bukan seluruh gen
2. **Mempertahankan Konsistensi**: Kombinasi guru dan mata pelajaran tetap utuh
3. **Menghindari Solusi Invalid**: Pertukaran parsial memastikan bahwa gen tetap valid

Proses mutasi untuk setiap gen:

1. Setiap gen memiliki peluang `mutation_rate` untuk dimutasi
2. Jika gen terpilih untuk mutasi, pilih gen kedua secara acak
3. Hanya komponen waktu (slot jam) yang ditukar antar kedua gen
4. Komponen guru dan mata pelajaran tetap tidak berubah

**Contoh Visualisasi:**

```
Gen 1 sebelum mutasi: "T01M01W1" (Guru T01 mengajar M01 pada waktu W1)
Gen 2 sebelum mutasi: "T02M02W2" (Guru T02 mengajar M02 pada waktu W2)

Setelah mutasi:
Gen 1 setelah mutasi: "T01M01W2" (Guru T01 mengajar M01 pada waktu W2)
Gen 2 setelah mutasi: "T02M02W1" (Guru T02 mengajar M02 pada waktu W1)
```

## Kontrol Mutasi

**Mutation Rate**:

- Parameter `mutation_rate` (default 0.1 atau 10%) menentukan probabilitas mutasi setiap gen
- Nilai antara 0.01-0.1 umumnya efektif untuk banyak masalah optimasi
- Nilai terlalu tinggi dapat menyebabkan pencarian acak tanpa konvergensi
- Nilai terlalu rendah dapat menyebabkan konvergensi prematur ke solusi lokal

## Implementasi Detail

1. **Iterasi Setiap Gen**:

   ```python
   for i in range(len(chromosome)):
       if random.random() < mutation_rate:
           # Lakukan mutasi
   ```

2. **Pemilihan Gen untuk Pertukaran**:

   ```python
   j = random.randint(0, len(chromosome) - 1)
   while j == i:  # Pastikan berbeda dengan gen pertama
       j = random.randint(0, len(chromosome) - 1)
   ```

3. **Dekomposisi Gen**:

   ```python
   teacher1 = gene1[:3]
   subject1 = gene1[3:6]
   time1 = gene1[6:]
   ```

4. **Rekonstruksi Gen dengan Komponen Tertukar**:

   ```python
   new_gene1 = teacher1 + subject1 + time2
   new_gene2 = teacher2 + subject2 + time1
   ```

## Keunggulan Implementasi

1. **Validitas Terjaga**: Mutasi hanya menukar slot waktu, mempertahankan integritas komponen guru dan mata pelajaran
2. **Selektif**: Tidak semua gen dimutasi, dikendalikan oleh mutation rate
3. **Efektif untuk Penjadwalan**: Mengarah pada eksplorasi variasi jadwal yang masih realistis
4. **Pertukaran Pasangan**: Memastikan setiap slot waktu tetap digunakan tepat sekali

## Peran dalam Algoritma Genetika

Mutasi adalah operasi penting yang:

1. Mencegah konvergensi prematur dengan mempertahankan keragaman populasi
2. Memungkinkan eksplorasi ruang solusi yang tidak dapat diakses melalui crossover saja
3. Membantu meloloskan diri dari optimum lokal menuju optimum global
4. Menyeimbangkan eksplorasi (mencari ruang solusi baru) dan eksploitasi (memanfaatkan solusi yang baik)
