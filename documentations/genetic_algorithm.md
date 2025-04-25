# Fungsi Genetic Algorithm

## Deskripsi

Fungsi `genetic_algorithm()` adalah implementasi utama algoritma genetika untuk masalah penjadwalan. Fungsi ini mengintegrasikan semua komponen algoritma genetika (pembangkitan populasi, evaluasi fitness, seleksi, crossover, dan mutasi) untuk menemukan solusi optimal atau mendekati optimal.

## Implementasi

```python
def genetic_algorithm(data, population_size=5, generations=10, crossover_rate=0.6, mutation_rate=0.1):
    """Implementasi algoritma genetika untuk penjadwalan."""

    print("Menjalankan Algoritma Genetika dengan parameter:")
    print(f"Population Size: {population_size}")
    print(f"Generations: {generations}")
    print(f"Crossover Rate: {crossover_rate}")
    print(f"Mutation Rate: {mutation_rate}")

    # Inisialisasi populasi
    population = generate_population(data, population_size)
    best_fitness = 0
    best_schedule = []

    # Tracking perkembangan fitness terbaik
    fitness_history = []

    for generation in range(generations):
        # Evaluasi Fitness
        fitness_values = [calculate_fitness(chromosome)[0] for chromosome in population]
        avg_fitness = sum(fitness_values) / len(fitness_values)

        # Cari individu terbaik
        max_fitness = max(fitness_values)
        if max_fitness > best_fitness:
            best_fitness = max_fitness
            best_schedule = population[fitness_values.index(max_fitness)]

        fitness_history.append({
            "Generation": generation + 1,
            "Best Fitness": max_fitness,
            "Average Fitness": avg_fitness
        })

        # Report progress setiap beberapa generasi
        if (generation + 1) % 5 == 0 or generation == 0 or generation == generations - 1:
            print(f"\nGenerasi {generation + 1}:")
            print(f"Fitness Terbaik: {max_fitness:.4f}")
            print(f"Fitness Rata-rata: {avg_fitness:.4f}")

        # Seleksi
        population = selection(population)

        # Crossover
        new_population = []
        for i in range(0, len(population), 2):
            parent1 = population[i]
            parent2 = population[i + 1] if i + 1 < len(population) else population[i] # Jika ganjil, kawinkan dengan diri sendiri
            child1, child2 = crossover(parent1, parent2, crossover_rate)
            new_population.append(child1)
            new_population.append(child2)
        population = new_population

        # Mutasi
        population = [mutate(chromosome, mutation_rate) for chromosome in population]

    print("\nProses Selesai!")
    print(f"Fitness Terbaik yang Ditemukan: {best_fitness:.4f}")

    # Tampilkan grafik perkembangan fitness
    fitness_df = pd.DataFrame(fitness_history)
    print("\nPerkembangan Fitness per Generasi:")
    display(fitness_df)

    return best_schedule, best_fitness
```

## Parameter

- `data` (list): Data input berisi guru, mata pelajaran, dan slot waktu
- `population_size` (int, default=5): Jumlah individu dalam populasi
- `generations` (int, default=10): Jumlah generasi/iterasi evolusi
- `crossover_rate` (float, default=0.6): Probabilitas terjadinya crossover
- `mutation_rate` (float, default=0.1): Probabilitas mutasi setiap gen

## Alur Algoritma Genetika

Algoritma genetika mengikuti proses evolusi alami dengan langkah-langkah berikut:

1. **Inisialisasi Populasi**:

   ```python
   population = generate_population(data, population_size)
   ```

   - Membangkitkan populasi awal dengan representasi gen yang valid
   - Setiap individu (kromosom) merepresentasikan satu solusi jadwal

2. **Loop Evolusi untuk Sejumlah Generasi**:

   ```python
   for generation in range(generations):
       # ...
   ```

   - Menjalankan proses evolusi selama jumlah generasi yang ditentukan

3. **Evaluasi Fitness**:

   ```python
   fitness_values = [calculate_fitness(chromosome)[0] for chromosome in population]
   ```

   - Mengevaluasi kualitas setiap kromosom berdasarkan fungsi fitness
   - Mencatat fitness terbaik dan rata-rata untuk pelacakan kemajuan

4. **Elitisme Implisit**:

   ```python
   if max_fitness > best_fitness:
       best_fitness = max_fitness
       best_schedule = population[fitness_values.index(max_fitness)]
   ```

   - Menyimpan kromosom terbaik sepanjang semua generasi (elitisme)

5. **Seleksi**:

   ```python
   population = selection(population)
   ```

   - Memilih individu untuk reproduksi menggunakan metode roulette wheel
   - Individu dengan fitness lebih tinggi memiliki peluang lebih besar terpilih

6. **Crossover**:

   ```python
   for i in range(0, len(population), 2):
       # ...
       child1, child2 = crossover(parent1, parent2, crossover_rate)
       # ...
   ```

   - Menggabungkan material genetik dari dua induk untuk menghasilkan keturunan
   - Dilakukan dengan probabilitas yang ditentukan oleh `crossover_rate`

7. **Mutasi**:

   ```python
   population = [mutate(chromosome, mutation_rate) for chromosome in population]
   ```

   - Memperkenalkan variasi acak ke dalam populasi untuk menjelajahi ruang solusi baru
   - Dilakukan dengan probabilitas yang ditentukan oleh `mutation_rate`

8. **Hasil Akhir**:

   ```python
   return best_schedule, best_fitness
   ```

   - Mengembalikan jadwal terbaik yang ditemukan beserta nilai fitnessnya

## Pelacakan Kinerja

Algoritma memiliki mekanisme pelacakan kinerja untuk memantau kemajuan evolusi:

```python
fitness_history.append({
    "Generation": generation + 1,
    "Best Fitness": max_fitness,
    "Average Fitness": avg_fitness
})
```

- Mencatat fitness terbaik dan rata-rata untuk setiap generasi
- Memungkinkan visualisasi konvergensi algoritma
- Berguna untuk analisis dan penyetelan parameter

## Kompleksitas Algoritma

- **Waktu**: O(generations × population_size × (fitness_evaluation + selection + crossover + mutation))
- **Ruang**: O(population_size × chromosome_length)

Di mana:

- `fitness_evaluation` tergantung pada kompleksitas fungsi fitness
- `chromosome_length` adalah jumlah gen dalam satu kromosom (jumlah slot waktu dalam jadwal)

## Pertimbangan Praktis

1. **Parameter Tuning**:
   - `population_size`: Populasi lebih besar memberikan keragaman lebih tetapi membutuhkan waktu komputasi lebih
   - `generations`: Lebih banyak generasi meningkatkan konvergensi tetapi dapat menyebabkan overfitting
   - `crossover_rate` dan `mutation_rate`: Mempengaruhi keseimbangan eksplorasi dan eksploitasi

2. **Trade-offs**:
   - Konvergensi cepat vs. keragaman populasi
   - Kualitas solusi vs. waktu komputasi
   - Eksplorasi ruang solusi baru vs. eksploitasi solusi yang sudah baik

## Output

Fungsi mengembalikan dua nilai:

1. `best_schedule`: Kromosom terbaik yang merepresentasikan jadwal optimal atau mendekati optimal
2. `best_fitness`: Nilai fitness dari jadwal terbaik, menunjukkan kualitas solusi (skala 0-1, di mana 1 adalah terbaik)
