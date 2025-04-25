# Fungsi Generate Population

## Deskripsi

Fungsi `generate_population()` bertanggung jawab untuk membuat populasi awal dalam algoritma genetika. Fungsi ini membangkitkan sejumlah kromosom yang merepresentasikan solusi potensial untuk masalah penjadwalan.

## Implementasi

```python
def generate_population(data, population_size=5):
    """
    Membuat populasi awal dengan representasi jadwal yang valid.
    Setiap kromosom merepresentasikan jadwal lengkap.
    Setiap gen merepresentasikan satu slot waktu dengan guru dan mata pelajaran terkait.
    """
    population = []
    
    # Ekstrak daftar slot waktu yang tersedia
    available_time_slots = [item["Kd_Jam"] for item in data]
    
    for _ in range(population_size):
        # Setiap kromosom akan memiliki gen sebanyak slot waktu yang tersedia
        chromosome = []
        
        # Daftar guru yang sudah digunakan dalam kromosom ini
        used_teachers = set()
        # Daftar mata pelajaran yang sudah digunakan dalam kromosom ini
        used_subjects = set()
        
        # Shuffle daftar waktu untuk randomisasi jadwal
        time_slots = available_time_slots.copy()
        random.shuffle(time_slots)
        
        for time_slot in time_slots:
            # Filter data yang belum digunakan
            available_data = []
            for item in data:
                if (item["Kd_Guru"] not in used_teachers and 
                    item["Kd_Mata_Pelajaran"] not in used_subjects):
                    available_data.append(item)
            
            # Jika tidak ada data yang tersedia, mulai ulang dengan semua guru dan mata pelajaran
            if not available_data and len(chromosome) < len(available_time_slots):
                available_data = data
            
            # Pilih satu entri data secara acak
            selected_item = random.choice(available_data)
            
            # Buat gen dengan format yang konsisten
            gene = f"{selected_item['Kd_Guru']}{selected_item['Kd_Mata_Pelajaran']}{time_slot}"
            chromosome.append(gene)
            
            # Tandai guru dan mata pelajaran sudah digunakan
            used_teachers.add(selected_item["Kd_Guru"])
            used_subjects.add(selected_item["Kd_Mata_Pelajaran"])
        
        population.append(chromosome)
    
    return population
```

## Parameter

- `data` (list): List of dictionaries berisi data guru, mata pelajaran, dan slot waktu
- `population_size` (int, default=5): Jumlah kromosom/individu dalam populasi

## Cara Kerja

1. **Inisialisasi Populasi**:
   - Populasi awal berupa list kosong yang akan diisi dengan kromosom
   - Ekstrak daftar slot waktu yang tersedia dari data

2. **Pembangkitan Kromosom**:
   - Untuk setiap individu dalam populasi, buat kromosom baru
   - Setiap kromosom memiliki gen sebanyak slot waktu yang tersedia
   - Lacak guru dan mata pelajaran yang sudah digunakan untuk menghindari duplikasi

3. **Pembuatan Gen**:
   - Acak urutan slot waktu untuk variasi jadwal
   - Untuk setiap slot waktu, pilih kombinasi guru dan mata pelajaran
   - Prioritaskan kombinasi yang belum digunakan untuk meminimalkan konflik
   - Format gen: `KodeGuruKodeMapelKodeWaktu`

4. **Validasi dan Diversifikasi**:
   - Jika tidak ada kombinasi valid tersisa, gunakan semua data untuk memastikan kromosom lengkap
   - Tandai guru dan mata pelajaran yang sudah digunakan untuk gen berikutnya

## Keunggulan Implementasi

1. **Validitas Struktural**: Setiap kromosom memiliki jumlah gen yang konsisten sesuai dengan jumlah slot waktu
2. **Keunikan Elemen**: Memprioritaskan guru dan mata pelajaran yang belum digunakan untuk meminimalkan konflik
3. **Diversifikasi**: Pengacakan urutan slot waktu memberikan variasi dalam populasi awal
4. **Keberlanjutan**: Memastikan kromosom lengkap bahkan jika kombinasi ideal tidak tersedia dengan fallback ke semua data

## Output

Fungsi mengembalikan list populasi berisi kromosom, di mana:

- Setiap kromosom adalah list gen
- Setiap gen adalah string berformat `KodeGuruKodeMapelKodeWaktu`
