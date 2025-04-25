# Fungsi Calculate Fitness

## Deskripsi

Fungsi `calculate_fitness()` mengevaluasi kualitas setiap kromosom (solusi potensial) berdasarkan seberapa baik kromosom tersebut memenuhi batasan dan tujuan optimasi. Dalam konteks penjadwalan, fungsi ini mengukur seberapa optimal suatu jadwal dengan mempertimbangkan berbagai kriteria.

## Implementasi

```python
def calculate_fitness(chromosome):
    """
    Menghitung nilai fitness berdasarkan beberapa kriteria:
    1. Tidak ada guru yang mengajar di waktu yang sama (batasan utama)
    2. Prioritaskan guru tidak mengajar berturut-turut
    3. Proporsional distribusi mata pelajaran
    4. Mata pelajaran berat tidak di jam akhir
    """
    # Batasan utama: guru tidak boleh mengajar di waktu yang sama
    conflicts = 0
    teacher_times = {}
    
    # Untuk batasan tambahan
    consecutive_teachings = 0
    heavy_subjects_at_end = 0
    subject_count = {}
    
    # Daftar mata pelajaran berat
    heavy_subjects = ["M03"]  # Matematika
    late_hours = ["W4", "W5"]  # Jam-jam akhir
    
    # Untuk keperluan display, kita akan melacak konflik
    conflict_details = []
    
    # Urutkan gen berdasarkan kode jam untuk analisis pola berurutan
    sorted_genes = sorted(chromosome, key=lambda x: x[6:])
    
    # Analisis konflik dan batasan
    for i, gene in enumerate(sorted_genes):
        teacher = gene[:3]
        subject = gene[3:6]
        time = gene[6:]
        
        # Batasan 1: Konflik waktu
        if teacher in teacher_times and time in teacher_times[teacher]:
            conflicts += 1
            conflict_details.append({
                "Gene": gene,
                "Teacher": teacher,
                "Subject": subject,
                "Time": time,
                "Conflict": "Yes"
            })
        else:
            if teacher not in teacher_times:
                teacher_times[teacher] = []
            teacher_times[teacher].append(time)
            conflict_details.append({
                "Gene": gene,
                "Teacher": teacher,
                "Subject": subject,
                "Time": time,
                "Conflict": "No"
            })
        
        # Batasan 2: Guru mengajar berturut-turut
        if i > 0 and sorted_genes[i-1][:3] == teacher:
            consecutive_teachings += 1
        
        # Batasan 3: Proporsi mata pelajaran
        if subject in subject_count:
            subject_count[subject] += 1
        else:
            subject_count[subject] = 1
        
        # Batasan 4: Mata pelajaran berat di jam akhir
        if subject in heavy_subjects and time in late_hours:
            heavy_subjects_at_end += 1
    
    # Hitung ketidakseimbangan proporsi mata pelajaran
    subject_imbalance = sum(abs(count - (len(chromosome) / len(subject_count))) 
                           for count in subject_count.values())
    
    # Hitung nilai fitness final
    # Faktor pembobotan untuk setiap kriteria
    w1, w2, w3, w4 = 0.5, 0.2, 0.2, 0.1  # total = 1.0
    
    # Normalisasi nilainya ke range [0-1] dengan membalik (semakin kecil semakin baik)
    conflicts_norm = 1 / (1 + conflicts)
    consecutive_norm = 1 / (1 + consecutive_teachings)
    imbalance_norm = 1 / (1 + subject_imbalance)
    heavy_end_norm = 1 / (1 + heavy_subjects_at_end)
    
    # Fitness akhir
    fitness_value = (
        w1 * conflicts_norm +
        w2 * consecutive_norm +
        w3 * imbalance_norm +
        w4 * heavy_end_norm
    )
    
    return fitness_value, conflict_details, {
        "Konflik Jadwal": conflicts,
        "Jadwal Berturutan": consecutive_teachings,
        "Ketidakseimbangan Mapel": subject_imbalance,
        "Mapel Berat di Jam Akhir": heavy_subjects_at_end,
        "Fitness Parsial": {
            "Konflik": conflicts_norm,
            "Berturutan": consecutive_norm,
            "Ketidakseimbangan": imbalance_norm,
            "Mapel Berat di Akhir": heavy_end_norm
        }
    }
```

## Parameter

- `chromosome` (list): List gen yang merepresentasikan satu jadwal lengkap

## Kriteria Evaluasi

Fungsi `calculate_fitness()` menggunakan pendekatan multi-kriteria dengan bobot tertentu untuk setiap komponen:

1. **Konflik Jadwal** (50% bobot):
   - Mendeteksi apakah satu guru dijadwalkan mengajar di waktu yang sama
   - Pelanggaran batasan ini sangat kritik sehingga mendapat bobot tertinggi

2. **Jadwal Berturut-turut** (20% bobot):
   - Menghitung berapa kali guru dijadwalkan mengajar pada slot waktu yang berurutan
   - Mengurangi beban guru dengan mencegah jadwal mengajar terus-menerus

3. **Distribusi Mata Pelajaran** (20% bobot):
   - Mengukur keseimbangan distribusi mata pelajaran
   - Mata pelajaran sebaiknya tersebar merata dalam jadwal

4. **Penempatan Mata Pelajaran Berat** (10% bobot):
   - Mendeteksi apakah mata pelajaran berat (seperti Matematika) dijadwalkan di jam-jam akhir
   - Mata pelajaran berat lebih baik ditempatkan di awal hari saat konsentrasi masih tinggi

## Perhitungan Fitness

1. **Normalisasi Nilai**:
   - Setiap komponen dikonversi ke rentang [0-1] menggunakan formula `1 / (1 + nilai_pelanggaran)`
   - Semakin kecil nilai pelanggaran, semakin mendekati 1 nilai fitness komponennya

2. **Nilai Fitness Akhir**:
   - Penjumlahan tertimbang dari semua komponen fitness: `fitness = w1*f1 + w2*f2 + w3*f3 + w4*f4`
   - Rentang nilai fitness akhir: 0 (terburuk) hingga 1 (terbaik)

## Output

Fungsi mengembalikan 3 elemen:

1. **Nilai fitness total** (float): Nilai fitness keseluruhan dari kromosom
2. **Detail konflik** (list): Informasi detail tentang konflik pada setiap gen
3. **Komponen fitness** (dict): Rincian nilai untuk setiap kriteria evaluasi dan kontribusinya pada fitness total

## Keunggulan Implementasi

1. **Multi-kriteria**: Mempertimbangkan berbagai aspek kualitas jadwal
2. **Pembobotan**: Memberikan prioritas pada aspek yang lebih penting
3. **Fleksibilitas**: Dapat disesuaikan dengan menambah/mengurangi kriteria atau mengubah bobot
4. **Analisis Komprehensif**: Menyediakan detail lengkap untuk memahami kualitas solusi
