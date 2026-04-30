  import random
  import matplotlib.pyplot as plt

# =========================================================
# DATA (SUDAH FIX)
# =========================================================
  subjects = ["AI", "DB", "WEB", "ML", "IOT"]
 
  # 🔥 ditambah agar cukup
  times = ["Pagi", "Siang", "Sore", "Malam", "Dini"]
 
  POP_SIZE = 30
  GENERATIONS = 100
  MUTATION_RATE = 0.2

'Pada bagian data sudh fix'
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

'Pada bagian init'
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

# =========================================================
# FITNESS
# =========================================================
def fitness(schedule):
    conflicts = count_conflicts(schedule)
    return max(100 - conflicts * 20, 0)

# =========================================================
# SELECTION
# =========================================================
def selection(pop):
    return max(random.sample(pop, 3), key=fitness)

# =========================================================
# CROSSOVER
# =========================================================
def crossover(p1, p2):
    point = random.randint(1, len(p1)-1)
    return p1[:point] + p2[point:]

# =========================================================
# MUTATION
# =========================================================
def mutate(schedule):
    for i in range(len(schedule)):
        if random.random() < MUTATION_RATE:
            schedule[i] = random.choice(times)
    return schedule

# =========================================================
# DECODE
# =========================================================
def decode(schedule):
    return list(zip(subjects, schedule))

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

# RUN
GA()
