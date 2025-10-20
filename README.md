-- Inisialisasi RNG (penting agar hasilnya acak)
math.randomseed(os.time())
math.random()
math.random()
math.random()

-- Variabel Global atau Status Pemain
local player_luck = 100  -- 🚀 NILAI KEBERUNTUNGAN TINGGI (Ditingkatkan dari 5) 🚀
local base_chance = 0.05 -- Peluang dasar untuk event langka (5%)
local luck_multiplier = 0.01 -- Seberapa besar 1 poin keberuntungan mempengaruhi peluang (1% per poin)

-- Fungsi untuk menghitung peluang sukses event langka
function calculate_success_chance(base, luck, multiplier)
    -- Peluang yang dimodifikasi: Base + (Luck * Multiplier)
    local chance_increase = luck * multiplier
    local total_chance = base + chance_increase

    -- Batasi peluang maksimal, misalnya tidak lebih dari 0.95 (95%)
    -- Jika keberuntungan 100, maka: 0.05 + (100 * 0.01) = 0.05 + 1.00 = 1.05
    -- Nilai yang dikembalikan akan dibatasi menjadi 0.95 (95%)
    return math.min(total_chance, 0.95)
end

-- Fungsi utama untuk mencoba event langka
function attempt_rare_event(luck_value)
    local success_chance = calculate_success_chance(base_chance, luck_value, luck_multiplier)
    local roll = math.random()

    print(string.format("Keberuntungan Pemain: %d", luck_value))
    print(string.format("Peluang Sukses: %.2f%%", success_chance * 100))
    print(string.format("Roll Acak (0.00-1.00): %.4f", roll))

    if roll < success_chance then
        print("🎉 SUKSES! Event langka berhasil!")
        return true
    else
        print("❌ GAGAL. Event langka tidak terjadi.")
        return false
    end
end

-- ===================================
-- ⬇️ Contoh Penggunaan Keberuntungan Tinggi ⬇️
-- ===================================

print("--- Percobaan Keberuntungan TINGGI (Luck = 100) ---")
attempt_rare_event(player_luck)
