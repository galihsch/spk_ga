# Fungsi Selection

## Deskripsi

Fungsi `selection()` bertanggung jawab untuk memilih kromosom (individu) dari populasi saat ini yang akan menjadi "orang tua" untuk membentuk generasi berikutnya. Seleksi adalah proses penting yang menerapkan prinsip "survival of the fittest" dalam algoritma genetika.

## Implementasi

```python
def selection(population, display_details=False):
    """Melakukan seleksi menggunakan metode roulette wheel."""

    fitness_values = [calculate_fitness(chromosome)[0] for chromosome in population]
    total_fitness = sum(fitness_values)
    probabilities = [f / total_fitness for f in fitness_values]

    # Untuk keperluan display
    selection_details = []
    for i, (chromosome, fitness, prob) in enumerate(zip(population, fitness_values, probabilities)):
        selection_details.append({
            "Kromosom": f"Kromosom {i+1}",
            "Kromosom Value": str(chromosome),
            "Fitness": f"{fitness:.4f}",
            "Probabilitas": f"{prob:.4f}"
        })

    if display_details:
        print("\nDetail Seleksi:")
        display(pd.DataFrame(selection_details))

    new_population = []
    selection_results = []

    for j in range(len(population)):
        # Roulette wheel selection
        r = random.random()
        cumulative_probability = 0
        selected_index = -1

        for i, p in enumerate(probabilities):
            cumulative_probability += p
            if r <= cumulative_probability:
                new_population.append(population[i])
                selected_index = i
                break

        if display_details:
            selection_results.append({
                "Seleksi ke": j+1,
                "Nilai Random": f"{r:.4f}",
                "Kromosom Terpilih": f"Kromosom {selected_index+1}"
            })

    if display_details:
        print("\nHasil Seleksi:")
        display(pd.DataFrame(selection_results))

    return new_population
```

## Parameter

- `population` (list): Populasi kromosom saat ini yang akan diseleksi
- `display_details` (bool, default=False): Flag untuk menampilkan detail proses seleksi

## Metode Seleksi: Roulette Wheel

Roulette wheel (roda rolet) adalah metode seleksi yang memberi peluang lebih besar kepada kromosom dengan nilai fitness yang lebih tinggi untuk terpilih. Berikut adalah cara kerjanya:

1. **Perhitungan Probabilitas**:
   - Hitung fitness setiap kromosom
   - Konversi nilai fitness menjadi probabilitas dengan membagi fitness setiap kromosom dengan total fitness
   - Kromosom dengan fitness lebih tinggi akan mendapat probabilitas yang lebih besar

2. **Proses Seleksi**:
   - Untuk setiap posisi dalam populasi baru:
     - Bangkitkan angka acak antara 0 dan 1
     - Hitung probabilitas kumulatif untuk setiap kromosom
     - Pilih kromosom pertama yang probabilitas kumulatifnya melebihi angka acak
   - Proses ini mirip dengan memutar roda rolet, di mana kromosom dengan "irisan" lebih besar (fitness lebih tinggi) memiliki peluang lebih besar untuk terpilih

## Implementasi Detail

1. **Perhitungan Fitness dan Probabilitas**:

   ```python
   fitness_values = [calculate_fitness(chromosome)[0] for chromosome in population]
   total_fitness = sum(fitness_values)
   probabilities = [f / total_fitness for f in fitness_values]
   ```

2. **Seleksi Berdasarkan Roulette Wheel**:

   ```python
   r = random.random()
   cumulative_probability = 0
   for i, p in enumerate(probabilities):
       cumulative_probability += p
       if r <= cumulative_probability:
           new_population.append(population[i])
           selected_index = i
           break
   ```

## Karakteristik Roulette Wheel

1. **Proporsional terhadap Fitness**: Peluang seleksi proporsional dengan nilai fitness
2. **Mungkin Berulang**: Satu kromosom dapat terpilih beberapa kali
3. **Tidak Menjamin Kromosom Terbaik**: Kromosom terbaik mungkin tidak terpilih meskipun memiliki probabilitas tertinggi
4. **Bias pada Rentang Fitness Sempit**: Kurang efektif jika perbedaan fitness antar kromosom kecil

## Output

Fungsi mengembalikan populasi baru (list kromosom) yang telah dipilih melalui proses seleksi. Populasi baru ini akan menjadi "orang tua" untuk proses crossover dan kemudian mutasi.

## Kegunaan dalam Algoritma Genetika

Fungsi seleksi memastikan bahwa sifat-sifat baik dari kromosom dengan fitness tinggi memiliki peluang lebih besar untuk diteruskan ke generasi berikutnya. Ini mendorong konvergensi algoritma menuju solusi optimal dengan tetap mempertahankan keragaman populasi.
