    import random
    import matplotlib.pyplot as plt

# =========================================================
# DATA (SUDAH FIX)
# =========================================================
    subjects = ["AI", "DB", "WEB", "ML", "IOT"]
   
    times = ["Pagi", "Siang", "Sore", "Malam", "Dini"]
   
    POP_SIZE = 30
    GENERATIONS = 100
    MUTATION_RATE = 0.2

  Pada bagian data sudh fix
 bagian ini berisi data prameter utama yang di gunakan dalam algoritma. subjects merupakan daftar mata pelajaran
 yang akan di jadwalkan, sedangkan times adalah pilihan slot waktu yang tersediah. POP_SIZE menentukan jumlah solusi
 (jadwal) dalam setiap generasi, GENERATIONS menunjukan berapa kali proses pencarian solusi
 dilakukan, dan MUNTATION_RATE adalah peluang terjadinya perubahan acak pada solusi untuk menjadi variasi.
 INTINYA: bagian ini mengatur apa yang di proses dan bagaimana algorima berjalan.
 
# =========================================================
# INIT
# =========================================================
      def create_schedule():
          return [random.choice(times) for _ in subjects]
      
      def init_population():
          return [create_schedule() for _ in range(POP_SIZE)]

Pada bagian init
Bagian ini digunakan untuk membuat solusi awal (inisialisasi populasi). fungsi create_schedule() menghasilkan satu jadwal dengan cara memilih 
waktu secara acak dari times untuk setiap mata pelajaran di subject. sementara itu, fungsi init_population() membuat sekumpulan jadwal (populasi awal
sebanyak POP_SIZE, yang nantinya akan di gunakan sebagai titik awal proses Genetic Algorithm. Intinya bagian ini membentuk kumpulan solusi awal secara acak.

# =========================================================
# HITUNG KONFLIK
# =========================================================
    def count_conflicts(schedule):
        used = set()
        conflicts = 0
    
        for t in schedule:
            if t in used:
                conflicts += 1
            else:
                used.add(t)

    return conflicts

Fungsi count_conflicts (schedule) digunakan untuk menghitung jumlah benrokan waktuu dalam sebuah jadwal, Variabel (used) dipakai untuk menyimpan waktu yang sudah di gunakan sedangkan (conflicts) untuk menghitunag jumlah komflik. program akan mencekatak setiap waktu dalam jadwal jika waktu tersebut sudah pernah muncul sebelumnya maka di anggap bentrok dan nilai konflik di tambah. jika belum waktu tersebut di simpan di dalam (used) di akhir, fungsi mengembalikan total konflik, yang menunjukan seburuk jadwal tersebut.

# =========================================================
# FITNESS
# =========================================================
    def fitness(schedule):
        conflicts = count_conflicts(schedule)
        return max(100 - conflicts * 20, 0)
        
Fungsi fitness(schedule) digunakan untuk menilai kualitas sebuah jadwal. Nilai konflik dihitung terlebih dahulu menggunakan count_conflicts(schedule), lalu dikonversi menjadi skor fitness dengan rumus 100 - konflik × 20. Semakin sedikit konflik, semakin tinggi nilai fitness. Fungsi max(..., 0) memastikan nilai tidak menjadi negatif, sehingga batas minimum adalah 0. Intinya, jadwal tanpa bentrok akan mendapat nilai tertinggi (100), sedangkan semakin banyak bentrok, nilainya semakin rendah.

# =========================================================
# SELECTION
# =========================================================
    def selection(pop):
        return max(random.sample(pop, 3), key=fitness)

Fungsi selection(pop) digunakan untuk memilih individu (jadwal) terbaik dari populasi dengan metode tournament selection. Program mengambil 3 jadwal secara acak dari populasi (random.sample(pop, 3)), lalu memilih yang memiliki nilai fitness tertinggi menggunakan max(..., key=fitness). Intinya, fungsi ini memilih solusi yang lebih baik untuk dijadikan induk dalam proses selanjutnya.

# =========================================================
# CROSSOVER
# =========================================================
    def crossover(p1, p2):
        point = random.randint(1, len(p1)-1)
        return p1[:point] + p2[point:]

Fungsi crossover(p1, p2) digunakan untuk menggabungkan dua solusi (parent) menjadi satu solusi baru (child). Program memilih titik potong secara acak (point), lalu mengambil sebagian jadwal dari p1 sebelum titik tersebut dan menggabungkannya dengan sisa jadwal dari p2 setelah titik potong. Intinya, fungsi ini menukar sebagian informasi dari dua parent untuk menghasilkan kombinasi jadwal baru yang diharapkan lebih baik.

# =========================================================
# MUTATION
# =========================================================
    def mutate(schedule):
        for i in range(len(schedule)):
            if random.random() < MUTATION_RATE:
                schedule[i] = random.choice(times)
        return schedule

Fungsi mutate(schedule) digunakan untuk mengubah sebagian isi jadwal secara acak (mutasi). Program mengecek setiap elemen dalam jadwal, lalu dengan peluang sebesar MUTATION_RATE (20%), waktu pada posisi tersebut akan diganti dengan waktu lain yang dipilih secara acak dari times. Tujuannya adalah menambah variasi solusi agar algoritma tidak terjebak pada hasil yang kurang optimal.

# =========================================================
# DECODE
# =========================================================
    def decode(schedule):
        return list(zip(subjects, schedule))

Fungsi decode(schedule) digunakan untuk mengubah representasi jadwal menjadi bentuk yang mudah dibaca. Fungsi ini menggabungkan setiap mata pelajaran dalam subjects dengan waktu yang ada di schedule menggunakan zip, lalu mengubahnya menjadi list. Hasilnya berupa pasangan (mata pelajaran, waktu), sehingga jadwal lebih jelas dan mudah dipahami.

# =========================================================
# GA
# =========================================================
    def GA():
        pop = init_population()
        best_hist = []
    
        print("\n===== OPTIMASI JADWAL BELAJAR =====\n")
    
        for gen in range(GENERATIONS):
    
            pop = sorted(pop, key=fitness, reverse=True)
    
            best = pop[0]
            best_fit = fitness(best)
    
            best_hist.append(best_fit)
    
            if gen % 5 == 0:
                print(f"Gen {gen:3d} | Fitness: {best_fit}")
    
            if best_fit == 100 and gen > 5:
                print("\n🎯 Jadwal optimal ditemukan!")
                break
    
            new_pop = pop[:2]
    
            while len(new_pop) < POP_SIZE:
                p1 = selection(pop)
                p2 = selection(pop)
    
                child = crossover(p1, p2)
                child = mutate(child)
    
                new_pop.append(child)
    
            pop = new_pop

Fungsi GA() merupakan inti proses Genetic Algorithm yang menjalankan optimasi jadwal. Program dimulai dengan membuat populasi awal (init_population()) dan menyiapkan list best_hist untuk menyimpan perkembangan nilai fitness. Pada setiap generasi, populasi diurutkan berdasarkan fitness dari yang terbaik, lalu diambil solusi terbaik (best) beserta nilainya. Nilai ini disimpan dan ditampilkan setiap 5 generasi. Jika sudah mencapai fitness maksimal (100), proses dihentikan lebih awal karena solusi optimal sudah ditemukan. Selanjutnya dibuat populasi baru dengan mempertahankan 2 individu terbaik (elitism), lalu sisanya diisi melalui proses seleksi, crossover, dan mutasi untuk menghasilkan solusi baru. Proses ini diulang hingga mencapai jumlah generasi atau menemukan solusi terbaik.

    # =====================================================
    # HASIL AKHIR
    # =====================================================
        best = sorted(pop, key=fitness, reverse=True)[0]
        conflicts = count_conflicts(best)
    
        print("\n===== HASIL AKHIR =====")
        print("Fitness :", fitness(best))
    
        for subj, t in decode(best):
            print(f"{subj} → {t}")
    
        print("\nPenjelasan:")
        if conflicts == 0:
            print("Tidak ada bentrok → jadwal optimal.")
        else:
            print(f"Terdapat {conflicts} bentrok waktu.")
            if len(subjects) > len(times):
                print("Penyebab: slot waktu tidak cukup.")

Bagian ini digunakan untuk menampilkan hasil akhir dari proses Genetic Algorithm. Program memilih jadwal terbaik dari populasi dengan fitness tertinggi, lalu menghitung jumlah konflik yang masih ada. Setelah itu, ditampilkan nilai fitness serta jadwal lengkap (mata pelajaran dan waktu) menggunakan fungsi decode(). Terakhir, program memberikan penjelasan apakah jadwal sudah optimal (tidak ada bentrok) atau masih memiliki konflik, serta kemungkinan penyebabnya jika jumlah slot waktu tidak mencukupi.

    # =====================================================
    # VISUAL
    # =====================================================
        plt.figure()
        plt.plot(best_hist)
        plt.title("Perkembangan Fitness")
        plt.xlabel("Generasi")
        plt.ylabel("Fitness")
        plt.grid()
        plt.show()

Bagian ini digunakan untuk menampilkan grafik perkembangan nilai fitness selama proses evolusi. plt.figure() membuat area grafik baru, lalu plt.plot(best_hist) menggambar perubahan nilai fitness terbaik di setiap generasi. Judul dan label sumbu ditambahkan untuk memperjelas informasi, sedangkan plt.grid() membantu pembacaan grafik. Terakhir, plt.show() menampilkan grafik tersebut sehingga dapat dilihat apakah solusi semakin membaik seiring bertambahnya generasi.

# RUN
    GA()
