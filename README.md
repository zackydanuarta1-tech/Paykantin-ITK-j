// 1. Class Menu
class Menu(
    private val idMenu: String,
    private val namaMenu: String,
    private val harga: Double,
    private var stok: Int // Private agar tidak bisa diubah sembarangan
) {
    // Getter / Public accessors
    fun getNama(): String = namaMenu
    fun getHarga(): Double = harga
    fun getStok(): Int = stok

    // Fungsi aman untuk mengurangi stok
    fun kurangiStok() {
        if (stok > 0) {
            stok--
        }
    }
}

// 2. Class Mahasiswa
class Mahasiswa(
    private val nim: String,
    private val nama: String,
    private var saldo: Double, // Private: Data sensitif
    private val pin: String    // Private: Data sangat sensitif
) {
    fun getNama(): String = nama
    fun getSaldo(): Double = saldo

    // Enkapsulasi logika pembayaran dan verifikasi PIN di dalam objek
    fun bayar(totalTagihan: Double, inputPin: String): Boolean {
        // Aturan Bisnis 1: Pembelian sah jika PIN benar
        if (inputPin != this.pin) {
            println("Transaksi Gagal: PIN salah untuk mahasiswa $nama!")
            return false
        }
        
        // Aturan Bisnis 2: Saldo Mahasiswa cukup & tidak boleh minus
        if (this.saldo < totalTagihan) {
            println("Transaksi Gagal: Saldo $nama tidak mencukupi! (Saldo: Rp$saldo, Tagihan: Rp$totalTagihan)")
            return false
        }

        // Jika lolos verifikasi, potong saldo
        this.saldo -= totalTagihan
        return true
    }
}

// 3. Class StandMakanan
class StandMakanan(val namaStand: String) {
    
    // Aturan Bisnis 3: Proses interaksi antar entitas
    fun prosesPembelian(pembeli: Mahasiswa, menuDipesan: Menu, inputPin: String) {
        println(">>> Memproses pesanan ${menuDipesan.getNama()} untuk ${pembeli.getNama()}...")
        
        // Cek stok terlebih dahulu
        if (menuDipesan.getStok() <= 0) {
            println("Transaksi Gagal: Stok ${menuDipesan.getNama()} habis!")
            println("--------------------------------------------------")
            return
        }

        // Coba lakukan pembayaran (memanggil fungsi bayar yang melakukan verifikasi PIN & Saldo)
        val pembayaranBerhasil = pembeli.bayar(menuDipesan.getHarga(), inputPin)

        if (pembayaranBerhasil) {
            menuDipesan.kurangiStok() // Kurangi stok menu karena valid
            println("Transaksi BERHASIL!")
            println("Sisa Saldo ${pembeli.getNama()}: Rp${pembeli.getSaldo()}")
            println("Sisa Stok ${menuDipesan.getNama()}: ${menuDipesan.getStok()}")
        }
        println("--------------------------------------------------")
    }
}

// ================= FUNGSI MAIN =================
fun main() {
    // Inisialisasi Objek
    val ayamGeprek = Menu("M01", "Ayam Geprek", 15000.0, 2)
    val esTeh = Menu("M02", "Es Teh Manis", 5000.0, 0) // Stok sengaja 0 untuk test
    
    val standA = StandMakanan("Stand Bu Siti")
    
    // Mahasiswa saldo awal 20000, PIN 1234
    val mahasiswa1 = Mahasiswa("1022001", "Budi", 20000.0, "1234")

    println("=== SIMULASI KANTINPAY ITK ===")
    
    // Skenario 1: Pembelian Berhasil (PIN Benar, Saldo Cukup, Stok Ada)
    standA.prosesPembelian(mahasiswa1, ayamGeprek, "1234")
    
    // Skenario 2: Pembelian Gagal (PIN Salah)
    standA.prosesPembelian(mahasiswa1, ayamGeprek, "9999")
    
    // Skenario 3: Pembelian Gagal (Saldo tidak cukup - saldo sisa 5000, mau beli ayam 15000)
    standA.prosesPembelian(mahasiswa1, ayamGeprek, "1234")
    
    // Skenario 4: Pembelian Gagal (Stok Habis - Es Teh stok 0)
    // Beri saldo tambahan lewat bayangan (karena enkapsulasi, kita tak bisa asal tambah saldo di sini tanpa fungsi top up)
    val mahasiswa2 = Mahasiswa("1022002", "Andi", 50000.0, "5678")
    standA.prosesPembelian(mahasiswa2, esTeh, "5678")
}


