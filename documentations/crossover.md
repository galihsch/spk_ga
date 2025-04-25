# Fungsi Crossover

## Deskripsi

Fungsi `crossover()` mengimplementasikan proses rekombinasi atau pindah silang dalam algoritma genetika. Fungsi ini mengambil dua kromosom induk, menukar bagian material genetik mereka, dan menghasilkan dua kromosom anak dengan kombinasi sifat dari kedua induk. Crossover adalah mekanisme utama dalam algoritma genetika untuk menjelajahi ruang solusi.

## Implementasi

```python
def crossover(parent1, parent2, crossover_rate=0.6, display_details=False):
    """Melakukan crossover (pindah silang) dengan probabilitas tertentu."""

    crossover_happened = False
    point = -1

    if random.random() < crossover_rate:
        # Single-point crossover
        point = random.randint(1, len(parent1) - 1)
        child1 = parent1[:point] + parent2[point:]
        child2 = parent2[:point] + parent1[point:]
        crossover_happened = True
    else:
        child1 = parent1.copy()
        child2 = parent2.copy()

    if display_details:
        crossover_info = {
            "Terjadi Crossover": "Ya" if crossover_happened else "Tidak",
            "Titik Potong": point if crossover_happened else "N/A",
            "Parent 1": str(parent1),
            "Parent 2": str(parent2),
            "Child 1": str(child1),
            "Child 2": str(child2)
        }
        print("\nDetail Crossover:")
        for key, value in crossover_info.items():
            print(f"{key}: {value}")

    return child1, child2
```

## Parameter

- `parent1` (list): Kromosom induk pertama
- `parent2` (list): Kromosom induk kedua
- `crossover_rate` (float, default=0.6): Probabilitas terjadinya crossover
- `display_details` (bool, default=False): Flag untuk menampilkan detail proses crossover

## Teknik Crossover: Single-Point Crossover

Implementasi ini menggunakan teknik single-point crossover (pindah silang satu titik), di mana:

1. Satu titik potong dipilih secara acak di sepanjang kromosom
2. Kromosom induk dipotong pada titik tersebut
3. Kedua kromosom anak terbentuk dengan menukar bagian kedua dari kedua induk

**Contoh Visualisasi:**

```
Parent 1: [A, B, C, D, E] 
Parent 2: [V, W, X, Y, Z]

Titik potong acak: 2

Child 1: [A, B, X, Y, Z]  (Parent1[:2] + Parent2[2:])
Child 2: [V, W, C, D, E]  (Parent2[:2] + Parent1[2:])
```

## Kontrol Crossover

**Crossover Rate**:

- Parameter `crossover_rate` (default 0.6 atau 60%) menentukan probabilitas terjadinya crossover
- Jika angka acak > `crossover_rate`, tidak terjadi crossover dan anak identik dengan induk
- Nilai antara 0.6-0.8 umumnya efektif untuk banyak masalah optimasi

**Keseimbangan Eksplorasi-Eksploitasi**:

- Crossover rate yang lebih tinggi meningkatkan eksplorasi ruang solusi baru
- Crossover rate yang lebih rendah mempertahankan solusi yang sudah baik (eksploitasi)

## Implementasi Detail

1. **Pengambilan Keputusan Crossover**:

   ```python
   if random.random() < crossover_rate:
       # Lakukan crossover
   else:
       # Tidak melakukan crossover, anak sama dengan induk
   ```

2. **Single-Point Crossover**:

   ```python
   point = random.randint(1, len(parent1) - 1)
   child1 = parent1[:point] + parent2[point:]
   child2 = parent2[:point] + parent1[point:]
   ```

## Konsekuensi dalam Penjadwalan

Dalam konteks penjadwalan, crossover dapat:

1. **Mengkombinasikan Solusi Parsial**: Menggabungkan bagian jadwal yang baik dari dua solusi
2. **Memperkenalkan Variasi**: Menghasilkan kombinasi jadwal baru yang mungkin lebih optimal
3. **Mungkin Menciptakan Konflik**: Dapat menghasilkan jadwal dengan konflik baru yang perlu diselesaikan oleh mutasi atau seleksi

## Output

Fungsi mengembalikan dua kromosom anak (dua list gen) hasil dari proses crossover, yang akan menjadi bagian dari populasi generasi berikutnya.

## Peran dalam Algoritma Genetika

Crossover adalah komponen utama algoritma genetika yang:

1. Memungkinkan pencarian solusi baru yang mengkombinasikan sifat baik dari solusi yang ada
2. Mendorong konvergensi populasi menuju solusi optimal
3. Menjaga keragaman populasi dengan menciptakan individu-individu baru
