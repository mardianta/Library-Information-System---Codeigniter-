# Tutorial SDLC: Membangun Fitur Peminjaman Buku dari Nol

> **Untuk siapa dokumen ini?**
> Dokumen ini ditulis untuk mahasiswa yang sedang belajar membuat sistem informasi, khususnya yang **bukan berasal dari jurusan IT**. Tidak ada asumsi bahwa kamu sudah mahir coding. Setiap langkah dijelaskan dengan bahasa sehari-hari dan analogi yang mudah dipahami.
>
> **Apa yang akan kamu pelajari?**
> Cara membangun fitur peminjaman buku secara bertahap mengikuti alur **SDLC (Software Development Life Cycle)** — yaitu cara profesional dalam membangun perangkat lunak, mulai dari perencanaan hingga pengujian.

---

## Daftar Isi

1. [Apa itu SDLC?](#1-apa-itu-sdlc)
2. [Gambaran Fitur yang Akan Dibuat](#2-gambaran-fitur-yang-akan-dibuat)
3. [Persiapan: Analisis Data yang Dibutuhkan](#3-persiapan-analisis-data-yang-dibutuhkan)
4. [Fitur 1 — Pencarian Anggota](#4-fitur-1--pencarian-anggota)
5. [Fitur 2 — Menampilkan Informasi Detail Anggota](#5-fitur-2--menampilkan-informasi-detail-anggota)
6. [Fitur 3 — Tambah Peminjaman Buku](#6-fitur-3--tambah-peminjaman-buku)
7. [Fitur 4 — Tombol Pengembalian Buku](#7-fitur-4--tombol-pengembalian-buku)
8. [Fitur 5 — Denda Keterlambatan](#8-fitur-5--denda-keterlambatan)
9. [Integrasi dan Pengujian Keseluruhan](#9-integrasi-dan-pengujian-keseluruhan)

---

## 1. Apa itu SDLC?

**SDLC** atau *Software Development Life Cycle* adalah **rangkaian tahapan** yang digunakan oleh tim pengembang untuk membangun perangkat lunak secara terstruktur dan terencana.

Bayangkan kamu ingin membangun sebuah rumah. Kamu tidak langsung pegang bata dan mulai membangun — kamu perlu:
1. **Merencanakan** rumah seperti apa yang dibutuhkan
2. **Mendesain** denah dan gambar tekniknya
3. **Membangun** rumah sesuai desain
4. **Menguji** apakah listrik, air, dan struktur sudah benar

Proses pembuatan perangkat lunak pun sama. Setiap fitur dibangun melalui tahapan berikut:

```
┌──────────────────────────────────────────────────────────────────┐
│                        SIKLUS SDLC                               │
│                                                                  │
│  1. ANALISIS      → Apa yang dibutuhkan? Siapa penggunanya?      │
│         ↓                                                        │
│  2. DESAIN        → Seperti apa tampilannya? Data apa yang perlu?│
│         ↓                                                        │
│  3. IMPLEMENTASI  → Tulis kodenya                                │
│         ↓                                                        │
│  4. PENGUJIAN     → Apakah fitur berjalan dengan benar?          │
└──────────────────────────────────────────────────────────────────┘
```

Pada tutorial ini, **setiap fitur** akan dibangun melalui keempat tahapan tersebut.

---

## 2. Gambaran Fitur yang Akan Dibuat

Sebelum masuk ke teknis, kita perlu tahu **apa yang sebenarnya ingin dibangun**.

### Konteks: Perpustakaan

Seorang **petugas perpustakaan** duduk di meja pelayanan. Seorang anggota datang ingin meminjam buku. Petugas perlu:

1. Mencari data anggota tersebut di komputer
2. Melihat informasi lengkap anggota (nama, kontak, riwayat pinjam)
3. Mencatat buku yang dipinjam beserta tanggal kembali
4. Suatu hari nanti, mencatat pengembalian buku tersebut

Itulah keempat fitur yang akan kita bangun:

| No | Nama Fitur | Apa yang Dilakukan Sistem |
|----|------------|--------------------------|
| 1 | **Pencarian Anggota** | Petugas ketik kode anggota → sistem mencarikan datanya |
| 2 | **Detail Informasi Anggota** | Menampilkan nama, email, telepon, alamat anggota |
| 3 | **Tambah Peminjaman** | Mencatat buku yang dipinjam + hitung batas kembali otomatis |
| 4 | **Pengembalian Buku** | Tombol untuk menandai buku sudah dikembalikan |
| 5 | **Denda Keterlambatan** | Petugas input denda saat pengembalian; sistem bantu tampilkan info keterlambatan |

---

## 3. Persiapan: Analisis Data yang Dibutuhkan

### Mengapa kita mulai dari data, bukan dari tampilan?

Ini adalah pertanyaan yang sering ditanyakan. Jawabannya: **tampilan (View) hanya memperlihatkan data. Kalau datanya tidak ada, tampilannya tidak bisa dibuat.**

Ibarat membangun toko online — kita harus tahu dulu produk apa yang dijual, baru bisa bikin halaman produknya.

---

### 3.1 Tabel Master Buku (`books`)

**"Master"** artinya data induk yang menjadi referensi. Tabel master buku berisi semua koleksi buku yang dimiliki perpustakaan.

> **Analogi:** Tabel master buku seperti katalog buku di rak perpustakaan. Setiap buku punya kartu katalog yang mencatat informasinya.

#### Atribut yang diperlukan:

| Nama Kolom | Tipe Data | Keterangan | Wajib? |
|------------|-----------|------------|--------|
| `id_book` | Angka (INT) | Nomor urut otomatis, kunci utama | Ya |
| `code_book` | Teks (VARCHAR 20) | Kode unik buku, misal: `BK-001` | Ya |
| `isbn_book` | Teks (VARCHAR 30) | Nomor ISBN internasional buku | Tidak |
| `title_book` | Teks (VARCHAR 150) | Judul buku | Ya |
| `author_book` | Teks (VARCHAR 100) | Nama pengarang | Ya |
| `publisher_book` | Teks (VARCHAR 100) | Nama penerbit | Ya |
| `published_year` | Tahun (YEAR) | Tahun terbit | Tidak |
| `description_book` | Teks panjang (TEXT) | Sinopsis atau deskripsi buku | Tidak |
| `created_at` | Waktu (DATETIME) | Kapan data ditambahkan (otomatis) | - |
| `updated_at` | Waktu (DATETIME) | Kapan data terakhir diubah (otomatis) | - |
| `deleted_at` | Waktu (DATETIME) | Tanggal dihapus (soft delete) | - |

> **Apa itu Soft Delete?** Buku yang "dihapus" tidak benar-benar hilang dari database. Kolom `deleted_at` hanya diisi tanggal penghapusan. Ini berguna agar riwayat peminjaman buku yang sudah dihapus tetap bisa dilacak.

---

### 3.2 Tabel Master Anggota (`members`)

Tabel master anggota berisi data semua anggota perpustakaan yang terdaftar.

> **Analogi:** Seperti buku daftar anggota di perpustakaan konvensional. Setiap anggota punya kartu anggota dengan kode uniknya.

#### Atribut yang diperlukan:

| Nama Kolom | Tipe Data | Keterangan | Wajib? |
|------------|-----------|------------|--------|
| `id_member` | Angka (INT) | Nomor urut otomatis, kunci utama | Ya |
| `code_member` | Teks (VARCHAR 20) | Kode unik anggota, misal: `MBR-001` | Ya |
| `name_member` | Teks (VARCHAR 100) | Nama lengkap anggota | Ya |
| `email_member` | Teks (VARCHAR 100) | Alamat email | Tidak |
| `phone_member` | Teks (VARCHAR 20) | Nomor telepon | Tidak |
| `address_member` | Teks panjang (TEXT) | Alamat lengkap | Tidak |
| `join_date` | Tanggal (DATE) | Tanggal mendaftar sebagai anggota | Tidak |
| `created_at` | Waktu (DATETIME) | Kapan data ditambahkan | - |
| `updated_at` | Waktu (DATETIME) | Kapan data diubah | - |
| `deleted_at` | Waktu (DATETIME) | Tanggal dihapus (soft delete) | - |

---

### 3.3 Tabel Transaksi Peminjaman (`peminjaman`)

Ini adalah tabel inti yang mencatat setiap kejadian peminjaman buku. Tabel ini **bukan master** melainkan tabel **transaksi** — artinya isinya terus bertambah seiring berjalannya waktu.

> **Analogi:** Seperti buku catatan transaksi kasir. Setiap ada peminjaman baru, ditambahkan satu baris baru. Tabel ini menghubungkan tabel `members` dan tabel `books`.

#### Atribut yang diperlukan:

| Nama Kolom | Tipe Data | Keterangan | Wajib? |
|------------|-----------|------------|--------|
| `id_peminjaman` | Angka (INT) | Nomor urut otomatis, kunci utama | Ya |
| `kode_peminjaman` | Teks (VARCHAR 30) | Kode unik transaksi, misal: `PJM202606170001` | Ya |
| `id_member` | Angka (INT) | Referensi ke tabel `members` | Ya |
| `id_book` | Angka (INT) | Referensi ke tabel `books` | Ya |
| `tanggal_pinjam` | Tanggal (DATE) | Tanggal mulai meminjam | Ya |
| `batas_kembali` | Tanggal (DATE) | Batas akhir pengembalian (dihitung otomatis) | Ya |
| `tanggal_dikembalikan` | Tanggal (DATE) | Tanggal buku benar-benar dikembalikan | Tidak |
| `denda` | Angka (INT) | Nominal denda keterlambatan dalam Rupiah (ditambahkan di Fitur 5) | Tidak |
| `durasi_pinjam` | Angka (INT) | Berapa hari masa pinjam (default: 3) | Ya |
| `status` | Pilihan (ENUM) | Nilai: `dipinjam` atau `dikembalikan` | Ya |
| `created_at` | Waktu (DATETIME) | Otomatis diisi sistem | - |
| `updated_at` | Waktu (DATETIME) | Otomatis diisi sistem | - |

---

### 3.4 Hubungan Antar Tabel (Relasi Database)

Ketiga tabel ini saling terhubung. Visualisasinya:

```
┌─────────────────┐         ┌──────────────────────┐         ┌──────────────┐
│    members      │         │      peminjaman       │         │    books     │
├─────────────────┤         ├──────────────────────┤         ├──────────────┤
│ id_member  (PK) │◄───────►│ id_member       (FK) │         │ id_book (PK) │
│ code_member     │         │ id_book         (FK) │◄───────►│ code_book    │
│ name_member     │         │ kode_peminjaman      │         │ title_book   │
│ email_member    │         │ tanggal_pinjam       │         │ author_book  │
│ phone_member    │         │ batas_kembali        │         │ ...          │
│ ...             │         │ durasi_pinjam        │         └──────────────┘
└─────────────────┘         │ status               │
                            │ tanggal_dikembalikan │
                            │ denda                │ ← ditambah di Fitur 5
                            └──────────────────────┘

Keterangan:
PK = Primary Key (kunci utama, nomor unik tiap baris)
FK = Foreign Key (kunci tamu, merujuk ke tabel lain)
```

> **Analogi relasi:** Bayangkan transaksi peminjaman seperti nota di toko. Nota tersebut mencantumkan ID pelanggan (mengacu ke buku daftar pelanggan) dan ID produk (mengacu ke katalog produk). Nota tidak menyalin ulang semua data pelanggan — cukup mencantumkan nomornya saja.

---

### 3.5 Membuat Tabel di Database (Migration)

Setelah memahami strukturnya, kita buat tabel menggunakan **Migration** — yaitu cara membuat tabel database lewat kode PHP.

> **Kenapa pakai Migration dan bukan phpMyAdmin langsung?**
> Migration bisa disimpan di Git (version control), sehingga seluruh tim bisa menjalankannya dan mendapatkan struktur database yang persis sama. Kalau pakai phpMyAdmin manual, tidak ada catatan perubahan strukturnya.

#### Buat file Migration untuk tabel `peminjaman`

Buat file baru di: `app/Database/Migrations/2026-06-17-000001_CreatePeminjamanTable.php`

> **Format nama file:** `TAHUN-BULAN-TANGGAL-NOMOR_NamaClass.php`
> Contoh: `2026-06-17-000001_CreatePeminjamanTable.php`
> Urutan nomor penting! Migration dijalankan berurutan dari nomor terkecil.

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class CreatePeminjamanTable extends Migration
{
    public function up()
    {
        $this->forge->addField([
            'id_peminjaman' => [
                'type'           => 'INT',
                'constraint'     => 11,
                'auto_increment' => true,
                'unsigned'       => true,
            ],
            'kode_peminjaman' => [
                'type'       => 'VARCHAR',
                'constraint' => 30,
            ],
            'id_member' => [
                'type'       => 'INT',
                'constraint' => 11,
                'unsigned'   => true,
            ],
            'id_book' => [
                'type'       => 'INT',
                'constraint' => 11,
                'unsigned'   => true,
            ],
            'tanggal_pinjam' => [
                'type' => 'DATE',
            ],
            'batas_kembali' => [
                'type' => 'DATE',
            ],
            'tanggal_dikembalikan' => [
                'type' => 'DATE',
                'null' => true,         // boleh kosong sebelum dikembalikan
            ],
            'durasi_pinjam' => [
                'type'       => 'INT',
                'constraint' => 3,
                'default'    => 3,      // default 3 hari
            ],
            'status' => [
                'type'       => 'ENUM',
                'constraint' => ['dipinjam', 'dikembalikan'],
                'default'    => 'dipinjam',
            ],
            'created_at' => ['type' => 'DATETIME', 'null' => true],
            'updated_at' => ['type' => 'DATETIME', 'null' => true],
        ]);

        $this->forge->addKey('id_peminjaman', true); // jadikan primary key
        $this->forge->createTable('peminjaman', true);
    }

    public function down()
    {
        $this->forge->dropTable('peminjaman'); // batalkan jika rollback
    }
}
```

#### Jalankan Migration

Buka **Terminal**, pastikan berada di folder project, lalu ketik:

```bash
php spark migrate
```

Kalau berhasil, akan muncul pesan:
```
Running all new migrations...
    Running: 2026-06-17-000001_CreatePeminjamanTable
Migrations complete.
```

Sekarang tabel `peminjaman` sudah tersedia di database.

---

## 4. Fitur 1 — Pencarian Anggota

### Tahap 1: Analisis

**Pertanyaan yang harus dijawab sebelum coding:**

- **Siapa pengguna fitur ini?** → Petugas perpustakaan
- **Apa yang ingin dilakukan?** → Mencari data anggota berdasarkan kode anggota
- **Data apa yang dikirim ke server?** → Kode anggota (teks)
- **Data apa yang dikembalikan server?** → Data anggota (nama, email, telepon, dll) atau pesan error
- **Bagaimana cara kerjanya?** → Tanpa refresh halaman (menggunakan AJAX)

> **Apa itu AJAX?** AJAX (*Asynchronous JavaScript and XML*) adalah teknik yang memungkinkan halaman web mengirim dan menerima data dari server **tanpa harus me-refresh seluruh halaman**. Bayangkan seperti chat WhatsApp — pesan terkirim dan masuk tanpa kamu perlu membuka ulang aplikasinya.

**Komponen yang dibutuhkan:**

| Komponen | File | Fungsi |
|----------|------|--------|
| Route | `app/Config/Routes.php` | Mendaftarkan URL `/peminjaman/get-anggota` |
| Controller | `app/Controllers/PeminjamanController.php` | Method `getAnggota()` memproses pencarian |
| Model | `app/Models/MemberModel.php` | Sudah ada, untuk query ke tabel `members` |
| View | `app/Views/peminjaman/index.php` | Form input kode anggota + JavaScript |

---

### Tahap 2: Desain

**Mockup tampilan fitur pencarian:**

```
┌────────────────────────────────────────────────────────┐
│  Cari Anggota                                          │
│  ─────────────────────────────────────────────────     │
│  Kode Anggota:  [ MBR-001_______________ ] [ Cari ]   │
│                                                        │
│  [Area ini kosong saat halaman pertama dibuka]         │
└────────────────────────────────────────────────────────┘
```

**Alur kerja (Flow):**

```
Petugas ketik kode → klik "Cari"
         ↓
JavaScript ambil nilai dari kotak input
         ↓
JavaScript kirim kode ke server (POST /peminjaman/get-anggota)
         ↓
Server (Controller) terima kode
         ↓
Model cari di tabel members WHERE code_member = kode
         ↓
   ┌─────────┴──────────┐
Ketemu               Tidak ketemu
   ↓                      ↓
Kirim JSON:           Kirim JSON:
{success: true,       {success: false,
 member: {...}}        message: "..."}
   ↓                      ↓
Tampilkan info        Tampilkan pesan
anggota di bawah      error berwarna
form                  kuning
```

---

### Tahap 3: Implementasi

#### Langkah 3.1 — Buat Model Peminjaman

Model adalah penghubung antara Controller dan database.

Buat file: `app/Models/PeminjamanModel.php`

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class PeminjamanModel extends Model
{
    protected $table      = 'peminjaman';       // nama tabel di database
    protected $primaryKey = 'id_peminjaman';    // kolom kunci utama

    // Daftar kolom yang boleh disimpan/diupdate
    // PENTING: kolom yang tidak ada di sini tidak akan tersimpan
    protected $allowedFields = [
        'kode_peminjaman',
        'id_member',
        'id_book',
        'tanggal_pinjam',
        'batas_kembali',
        'tanggal_dikembalikan',
        'durasi_pinjam',
        'status',
    ];

    protected $useTimestamps = true; // aktifkan created_at dan updated_at otomatis
}
```

#### Langkah 3.2 — Buat Method `getAnggota()` di Controller

Buat file: `app/Controllers/PeminjamanController.php`

Pada tahap ini, kita hanya membuat method untuk pencarian anggota dulu:

```php
<?php

namespace App\Controllers;

use App\Models\PeminjamanModel;
use App\Models\MemberModel;
use App\Models\BookModel;

class PeminjamanController extends BaseController
{
    // Tampilkan halaman utama peminjaman
    public function index(): string
    {
        $data['title'] = 'Peminjaman Buku';
        return view('peminjaman/index', $data);
    }

    // AJAX: Cari anggota berdasarkan kode yang dikirim dari form
    public function getAnggota()
    {
        // 1. Ambil kode yang dikirim dari form (method POST)
        $kode = $this->request->getPost('kode_anggota');

        // 2. Gunakan MemberModel untuk mencari di tabel 'members'
        $memberModel = new MemberModel();
        $member      = $memberModel->where('code_member', $kode)->first();

        // 3. Kalau tidak ketemu, kirim respons gagal
        if (!$member) {
            return $this->response->setJSON([
                'success' => false,
                'message' => 'Anggota tidak ditemukan.'
            ]);
        }

        // 4. Kalau ketemu, kirim data anggota dalam format JSON
        $peminjamanModel = new PeminjamanModel();
        $peminjaman      = $peminjamanModel->getByMember($member['id_member']);

        return $this->response->setJSON([
            'success'    => true,
            'member'     => $member,
            'peminjaman' => $peminjaman,
        ]);
    }
}
```

> **Apa itu `setJSON()`?** Fungsi CodeIgniter untuk mengirim data kembali ke browser dalam format JSON (teks berstruktur yang bisa dibaca JavaScript). Contoh hasilnya: `{"success":true,"member":{"name_member":"Budi",...}}`

#### Langkah 3.3 — Daftarkan Route

Buka: `app/Config/Routes.php`

Tambahkan baris berikut:

```php
// Peminjaman Routes
$routes->get('/peminjaman', 'PeminjamanController::index');
$routes->post('/peminjaman/get-anggota', 'PeminjamanController::getAnggota');
```

> **Penjelasan:**
> - `GET /peminjaman` → buka halaman utama peminjaman
> - `POST /peminjaman/get-anggota` → terima data dari JavaScript (kode anggota), proses, kembalikan JSON

#### Langkah 3.4 — Buat View: Form Pencarian

Buat folder baru: `app/Views/peminjaman/`

Buat file: `app/Views/peminjaman/index.php`

Untuk bagian pencarian, tuliskan kode HTML berikut:

```php
<?= $this->extend('layouts/template') ?>

<?= $this->section('header') ?>
<div class="row mb-2">
    <div class="col-sm-6">
        <h1><?= $title ?></h1>
    </div>
</div>
<?= $this->endSection() ?>

<?= $this->section('content') ?>

<!-- BAGIAN 1: Form Cari Anggota -->
<div class="card">
    <div class="card-header">
        <h3 class="card-title">
            <i class="fas fa-user mr-2"></i>Cari Anggota
        </h3>
    </div>
    <div class="card-body">
        <div class="row align-items-end">
            <div class="col-md-4">
                <div class="form-group mb-0">
                    <label for="kode_anggota">Kode Anggota</label>
                    <div class="input-group">
                        <input type="text"
                               id="kode_anggota"
                               class="form-control"
                               placeholder="Masukkan kode anggota...">
                        <div class="input-group-append">
                            <button class="btn btn-primary"
                                    id="btn-cari-anggota"
                                    type="button">
                                <i class="fas fa-search"></i> Cari
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Area info anggota (awalnya disembunyikan) -->
        <div id="info-anggota" class="mt-3" style="display:none;">
            <!-- Diisi oleh JavaScript setelah data diterima -->
        </div>

        <!-- Area pesan error (awalnya disembunyikan) -->
        <div id="info-anggota-error"
             class="alert alert-warning mt-3"
             style="display:none;">
            <i class="fas fa-exclamation-triangle mr-1"></i>
            <span id="pesan-error-anggota"></span>
        </div>
    </div>
</div>

<?= $this->endSection() ?>

<?= $this->section('js') ?>
<script>
    const BASE_URL = '<?= base_url() ?>';

    // Ketika tombol "Cari" diklik, jalankan fungsi cariAnggota
    document.getElementById('btn-cari-anggota')
        .addEventListener('click', cariAnggota);

    // Bisa juga tekan Enter di kotak input
    document.getElementById('kode_anggota')
        .addEventListener('keydown', function(e) {
            if (e.key === 'Enter') cariAnggota();
        });

    function cariAnggota() {
        // Ambil nilai dari kotak input, hapus spasi di awal/akhir
        const kode = document.getElementById('kode_anggota').value.trim();

        // Jangan lanjut kalau kotak input kosong
        if (!kode) return;

        // Sembunyikan hasil sebelumnya
        document.getElementById('info-anggota').style.display       = 'none';
        document.getElementById('info-anggota-error').style.display = 'none';

        // Kirim kode anggota ke server menggunakan fetch (AJAX)
        fetch(BASE_URL + 'peminjaman/get-anggota', {
            method: 'POST',
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
            body: 'kode_anggota=' + encodeURIComponent(kode)
        })
        .then(response => response.json())  // ubah respons menjadi objek JavaScript
        .then(function(res) {
            if (res.success) {
                tampilkanInfoAnggota(res.member);
            } else {
                // Tampilkan pesan error
                document.getElementById('pesan-error-anggota').textContent = res.message;
                document.getElementById('info-anggota-error').style.display = 'block';
            }
        });
    }
</script>
<?= $this->endSection() ?>
```

> **Penjelasan `style="display:none"`:** Elemen HTML dengan atribut ini tidak terlihat di halaman. JavaScript nanti akan mengubahnya menjadi `display:block` agar muncul. Ini teknik umum untuk menyembunyikan bagian halaman sampai data tersedia.

---

### Tahap 4: Pengujian Fitur 1

Setelah semua kode di atas ditulis, lakukan pengujian:

| Skenario Uji | Langkah | Hasil yang Diharapkan |
|---|---|---|
| Kode anggota valid | Ketik `MBR-001`, klik Cari | Tidak ada error di konsol browser |
| Kode anggota tidak ada | Ketik `XXXXX`, klik Cari | Muncul pesan "Anggota tidak ditemukan." berwarna kuning |
| Kotak input kosong | Klik Cari tanpa mengisi | Tidak terjadi apa-apa (tidak ada request ke server) |
| Tekan Enter | Ketik kode, tekan Enter | Sama seperti klik tombol Cari |

> **Cara cek apakah AJAX berhasil:** Buka browser, tekan `F12`, pilih tab **Network**. Klik Cari, lalu cari request ke `get-anggota`. Klik request tersebut dan lihat tab **Response** — di sana terlihat data JSON yang dikirim server.

---

## 5. Fitur 2 — Menampilkan Informasi Detail Anggota

### Tahap 1: Analisis

Fitur ini adalah kelanjutan langsung dari Fitur 1. Setelah server mengembalikan data anggota (JSON), JavaScript perlu **menampilkan informasi tersebut** di halaman.

**Pertanyaan yang harus dijawab:**

- **Data apa saja yang ditampilkan?** → Kode anggota, nama, email, telepon, alamat, tanggal bergabung
- **Di mana ditampilkan?** → Di bawah form pencarian, dalam sebuah tabel kecil
- **Kapan ditampilkan?** → Setelah server mengembalikan `success: true`
- **Apa yang terjadi kalau cari anggota berbeda?** → Informasi lama diganti dengan yang baru

**Komponen yang dibutuhkan:**

| Komponen | File | Fungsi |
|----------|------|--------|
| View | `app/Views/peminjaman/index.php` | Tambah area HTML untuk info anggota |
| JavaScript | Dalam file View yang sama | Fungsi `tampilkanInfoAnggota()` |

Tidak perlu perubahan di Controller atau Model karena data sudah dikembalikan pada Fitur 1.

---

### Tahap 2: Desain

**Mockup tampilan setelah anggota ditemukan:**

```
┌────────────────────────────────────────────────────────────────┐
│  Cari Anggota                                                  │
│  ──────────────────────────────────────────────────────────    │
│  Kode Anggota:  [ MBR-001_______________ ] [ Cari ]           │
│                                                                │
│  ────────────────── (pemisah) ─────────────────────────────── │
│                                                                │
│  Kode Anggota : MBR-001        │  Telepon : 081234567890      │
│  Nama         : Budi Santoso   │  Alamat  : Jl. Mawar No. 1  │
│  Email        : budi@mail.com  │  Bergabung: 2024-01-15       │
└────────────────────────────────────────────────────────────────┘
```

**Alur kerja:**

```
JavaScript terima JSON {success: true, member: {...}}
         ↓
Ambil setiap nilai dari objek member
(member.name_member, member.email_member, dst.)
         ↓
Isi ke dalam elemen HTML yang sudah disiapkan
(<span id="anggota-nama">, <span id="anggota-email">, dst.)
         ↓
Ubah style dari display:none → display:block
         ↓
Informasi anggota muncul di halaman
```

---

### Tahap 3: Implementasi

#### Langkah 3.1 — Perbarui Area Info Anggota di View

Ganti bagian `<div id="info-anggota">` yang kosong sebelumnya dengan:

```html
<!-- Area info anggota (awalnya disembunyikan) -->
<div id="info-anggota" class="mt-3" style="display:none;">
    <hr>
    <div class="row">
        <!-- Kolom kiri -->
        <div class="col-md-6">
            <table class="table table-sm table-borderless">
                <tr>
                    <td width="140"><strong>Kode Anggota</strong></td>
                    <td>: <span id="anggota-kode"></span></td>
                </tr>
                <tr>
                    <td><strong>Nama</strong></td>
                    <td>: <span id="anggota-nama"></span></td>
                </tr>
                <tr>
                    <td><strong>Email</strong></td>
                    <td>: <span id="anggota-email"></span></td>
                </tr>
            </table>
        </div>
        <!-- Kolom kanan -->
        <div class="col-md-6">
            <table class="table table-sm table-borderless">
                <tr>
                    <td width="140"><strong>Telepon</strong></td>
                    <td>: <span id="anggota-telepon"></span></td>
                </tr>
                <tr>
                    <td><strong>Alamat</strong></td>
                    <td>: <span id="anggota-alamat"></span></td>
                </tr>
                <tr>
                    <td><strong>Tgl Bergabung</strong></td>
                    <td>: <span id="anggota-join"></span></td>
                </tr>
            </table>
        </div>
    </div>
</div>
```

> **Kenapa pakai `<span id="...">`?** `<span>` adalah elemen HTML kosong yang digunakan sebagai "wadah" untuk diisi teks oleh JavaScript. Setiap span punya ID unik sehingga JavaScript bisa menemukan dan mengisinya dengan tepat.

#### Langkah 3.2 — Tambah Fungsi JavaScript `tampilkanInfoAnggota()`

Tambahkan fungsi ini di dalam tag `<script>`:

```javascript
function tampilkanInfoAnggota(member) {
    // Simpan ID member untuk digunakan saat tambah peminjaman nanti
    currentMemberId = member.id_member;

    // Isi setiap span dengan data dari objek member
    document.getElementById('anggota-kode').textContent    = member.code_member;
    document.getElementById('anggota-nama').textContent    = member.name_member;
    document.getElementById('anggota-email').textContent   = member.email_member  || '-';
    document.getElementById('anggota-telepon').textContent = member.phone_member  || '-';
    document.getElementById('anggota-alamat').textContent  = member.address_member || '-';
    document.getElementById('anggota-join').textContent    = member.join_date     || '-';

    // Simpan id_member di input tersembunyi (akan dipakai saat simpan peminjaman)
    document.getElementById('hidden-id-member').value = member.id_member;

    // Tampilkan area info anggota (ubah dari display:none ke display:block)
    document.getElementById('info-anggota').style.display = 'block';
}
```

> **Apa arti `|| '-'`?** Operator `||` artinya "atau". `member.email_member || '-'` artinya: "Tampilkan email anggota. Kalau emailnya kosong (null/undefined), tampilkan tanda `-` sebagai gantinya." Ini mencegah tampilnya tulisan "undefined" di layar.

#### Langkah 3.3 — Perbarui Fungsi `cariAnggota()` untuk Memanggil `tampilkanInfoAnggota()`

Ubah bagian `if (res.success)` di fungsi `cariAnggota()`:

```javascript
.then(function(res) {
    if (res.success) {
        tampilkanInfoAnggota(res.member);  // panggil fungsi yang baru dibuat
        // (Tabel peminjaman akan ditampilkan di Fitur 3)
    } else {
        document.getElementById('pesan-error-anggota').textContent = res.message;
        document.getElementById('info-anggota-error').style.display = 'block';
    }
});
```

Tambahkan juga variabel global dan input tersembunyi di awal JavaScript:

```javascript
let currentMemberId = null; // menyimpan id_member yang sedang aktif
```

Dan di bagian HTML (di dalam form modal yang akan dibuat di Fitur 3):

```html
<input type="hidden" id="hidden-id-member">
```

---

### Tahap 4: Pengujian Fitur 2

| Skenario Uji | Langkah | Hasil yang Diharapkan |
|---|---|---|
| Anggota dengan email lengkap | Cari anggota yang punya email | Email tampil dengan benar |
| Anggota tanpa nomor telepon | Cari anggota yang teleponnya kosong | Tampil tanda `-` bukan "null" |
| Cari anggota berbeda | Cari A → cari B | Info A hilang, diganti info B |
| Cari anggota tidak ada | Ketik kode salah | Info anggota disembunyikan kembali |

---

## 6. Fitur 3 — Tambah Peminjaman Buku

### Tahap 1: Analisis

**Pertanyaan yang harus dijawab:**

- **Kapan fitur ini muncul?** → Hanya setelah anggota ditemukan (Fitur 1 & 2 berhasil)
- **Data apa yang perlu diisi petugas?** → Kode buku, tanggal pinjam, lama pinjam (hari)
- **Data apa yang dihitung otomatis oleh sistem?** → Kode peminjaman unik, tanggal batas kembali
- **Validasi apa yang perlu dilakukan?** → Buku harus ada di database, buku tidak sedang dipinjam orang lain
- **Apa yang ditampilkan setelah berhasil?** → Tabel daftar peminjaman anggota diperbarui tanpa reload halaman

**Komponen yang dibutuhkan:**

| Komponen | File | Fungsi |
|----------|------|--------|
| Route | `app/Config/Routes.php` | Tambah `POST /peminjaman/store` |
| Controller | `PeminjamanController.php` | Method `store()` untuk simpan data |
| Model | `PeminjamanModel.php` | Method `generateCode()` dan `getByMember()` |
| View | `peminjaman/index.php` | Tabel daftar peminjaman + Modal pop-up form |
| JavaScript | Dalam View | Kirim data, terima respons, update tabel |

---

### Tahap 2: Desain

**Mockup tampilan tabel peminjaman + tombol:**

```
┌───────────────────────────────────────────────────────────────────┐
│  Buku yang Dipinjam                    [ + Tambah Peminjaman ]    │
├────┬──────────────┬───────────┬────────────────┬──────────┬──────┤
│ No │ Kode Pinjam  │ Kode Buku │ Judul          │ Status   │ Aksi │
├────┼──────────────┼───────────┼────────────────┼──────────┼──────┤
│  1 │ PJM20260617  │ BK-001    │ Pemrograman Web│ Dipinjam │[Kmb] │
│  2 │ PJM20260610  │ BK-005    │ Basis Data     │Dikembali.│  -   │
└────┴──────────────┴───────────┴────────────────┴──────────┴──────┘
```

**Mockup Modal Form Tambah Peminjaman:**

```
┌─────────────────────────────────────────────┐
│  + Tambah Peminjaman Buku              [✕]  │
├─────────────────────────────────────────────┤
│  Kode Buku *                                │
│  [ BK-001_________________________ ]        │
│  Masukkan kode buku yang akan dipinjam.     │
│                                             │
│  Tanggal Pinjam *                           │
│  [ 2026-06-17 __________________ ]          │
│                                             │
│  Lama Pinjam (hari) *                       │
│  [ 3 ]                                      │
│  Default 3 hari. Tanggal kembali otomatis.  │
│                                             │
│  Perkiraan Tanggal Kembali                  │
│  [ Kamis, 20 Juni 2026__________ ] (readonly)│
│                                             │
│  [ Batal ]                  [ 💾 Simpan ]   │
└─────────────────────────────────────────────┘
```

**Alur kerja penyimpanan:**

```
Klik "Tambah Peminjaman" → Modal muncul
         ↓
Isi kode buku, tanggal pinjam, lama pinjam
Sistem otomatis preview tanggal kembali
         ↓
Klik "Simpan"
         ↓
JavaScript validasi: field wajib tidak boleh kosong
         ↓
Kirim POST ke /peminjaman/store
         ↓
Controller: cek buku ada? → cek buku sedang dipinjam? → hitung batas kembali → simpan
         ↓
   ┌──────┴───────┐
Berhasil        Gagal
   ↓                ↓
Modal tutup    Tampilkan pesan
Tabel update    error di modal
(tanpa reload)
```

---

### Tahap 3: Implementasi

#### Langkah 3.1 — Tambah Method `getByMember()` dan `generateCode()` ke Model

Perbarui `app/Models/PeminjamanModel.php`:

```php
// Ambil semua peminjaman milik satu anggota (JOIN dengan tabel books)
public function getByMember($id_member)
{
    return $this->db->table('peminjaman p')
        ->select('p.*, b.code_book, b.title_book, b.author_book')
        ->join('books b', 'b.id_book = p.id_book')
        ->where('p.id_member', $id_member)
        ->orderBy('p.tanggal_pinjam', 'DESC')
        ->get()
        ->getResultArray();
}

// Buat kode peminjaman unik berurutan per hari
// Contoh hasil: PJM202606170001, PJM202606170002, dst.
public function generateCode()
{
    $prefix = 'PJM' . date('Ymd');  // contoh: PJM20260617

    // Cari kode terakhir dengan awalan hari ini
    $last = $this->db->table('peminjaman')
        ->like('kode_peminjaman', $prefix, 'after')
        ->orderBy('id_peminjaman', 'DESC')
        ->limit(1)
        ->get()
        ->getRowArray();

    $seq = 1;
    if ($last) {
        // Ambil 4 digit terakhir kode lalu tambah 1
        $seq = (int) substr($last['kode_peminjaman'], -4) + 1;
    }

    // str_pad: pastikan 4 digit, tambah nol di depan jika perlu
    // Contoh: 1 → "0001", 12 → "0012"
    return $prefix . str_pad($seq, 4, '0', STR_PAD_LEFT);
}
```

> **Apa itu JOIN?** JOIN adalah cara menggabungkan data dari dua tabel sekaligus. Pada `getByMember()`, kita mengambil data peminjaman sekaligus mengambil judul dan pengarang buku dari tabel `books` — tanpa perlu query dua kali. Seperti VLOOKUP di Excel, tapi untuk dua arah.

#### Langkah 3.2 — Tambah Method `store()` ke Controller

Tambahkan method `store()` di `app/Controllers/PeminjamanController.php`:

```php
// Simpan data peminjaman baru
public function store()
{
    // 1. Ambil semua data dari form
    $kode_buku      = $this->request->getPost('kode_buku');
    $id_member      = $this->request->getPost('id_member');
    $tanggal_pinjam = $this->request->getPost('tanggal_pinjam');
    $durasi_pinjam  = (int) $this->request->getPost('durasi_pinjam') ?: 3;

    // 2. Cek apakah buku ada di database
    $bookModel = new BookModel();
    $book      = $bookModel->where('code_book', $kode_buku)->first();
    if (!$book) {
        return $this->response->setJSON([
            'success' => false,
            'message' => 'Buku tidak ditemukan.'
        ]);
    }

    // 3. Cek apakah buku sedang dipinjam (status 'dipinjam').
    //    Jika statusnya sudah 'dikembalikan', buku BOLEH dipinjam lagi
    //    oleh siapa saja — termasuk anggota yang sama.
    $peminjamanModel = new PeminjamanModel();
    $sedangDipinjam  = $peminjamanModel
        ->where('id_book', $book['id_book'])
        ->where('status', 'dipinjam')
        ->first();

    if ($sedangDipinjam) {
        // Bedakan pesan: apakah peminjam aktif adalah anggota yang sama?
        $pesanBlokir = ($sedangDipinjam['id_member'] == $id_member)
            ? 'Buku ini masih Anda pinjam dan belum dikembalikan.'
            : 'Buku sedang dipinjam oleh anggota lain.';

        return $this->response->setJSON([
            'success' => false,
            'message' => $pesanBlokir
        ]);
    }

    // 4. Hitung tanggal batas kembali (tanggal pinjam + durasi hari)
    $batas_kembali = date('Y-m-d', strtotime($tanggal_pinjam . ' +' . $durasi_pinjam . ' days'));

    // 5. Simpan ke database
    $isSaved = $peminjamanModel->save([
        'kode_peminjaman' => $peminjamanModel->generateCode(),
        'id_member'       => $id_member,
        'id_book'         => $book['id_book'],
        'tanggal_pinjam'  => $tanggal_pinjam,
        'batas_kembali'   => $batas_kembali,
        'durasi_pinjam'   => $durasi_pinjam,
        'status'          => 'dipinjam',
    ]);

    if ($isSaved) {
        // Ambil data terbaru untuk diperbarui di tabel
        $peminjaman = $peminjamanModel->getByMember($id_member);
        return $this->response->setJSON([
            'success'    => true,
            'message'    => 'Peminjaman berhasil disimpan.',
            'peminjaman' => $peminjaman
        ]);
    }

    return $this->response->setJSON([
        'success' => false,
        'message' => 'Gagal menyimpan peminjaman.'
    ]);
}
```

> **Penjelasan `(int) $this->request->getPost('durasi_pinjam') ?: 3`:**
> - `getPost('durasi_pinjam')` → ambil nilai dari form
> - `(int)` → paksa menjadi bilangan bulat
> - `?: 3` → kalau hasilnya 0 atau kosong, gunakan nilai default 3

> **Mengapa buku bisa dipinjam lagi setelah dikembalikan?**
> Pengecekan di langkah 3 hanya memblokir buku yang saat ini berstatus `'dipinjam'`. Begitu buku dikembalikan (status berubah menjadi `'dikembalikan'`), query `->where('status', 'dipinjam')` tidak akan menemukan record tersebut, sehingga peminjaman baru bisa dilanjutkan. Satu buku hanya boleh punya **satu transaksi aktif** pada satu waktu, tetapi boleh dipinjam berkali-kali di waktu yang berbeda.

#### Langkah 3.3 — Daftarkan Route Baru

Di `app/Config/Routes.php`, tambahkan:

```php
$routes->post('/peminjaman/store', 'PeminjamanController::store');
```

#### Langkah 3.4 — Tambah Tabel Daftar Peminjaman dan Modal di View

Di `app/Views/peminjaman/index.php`, tambahkan **setelah** card pencarian anggota:

```html
<!-- BAGIAN 2: Tabel Daftar Peminjaman Anggota -->
<div class="card" id="card-peminjaman" style="display:none;">
    <div class="card-header">
        <h3 class="card-title">
            <i class="fas fa-book mr-2"></i>Buku yang Dipinjam
        </h3>
        <div class="card-tools">
            <button class="btn btn-success btn-sm"
                    id="btn-tambah-peminjaman"
                    type="button">
                <i class="fas fa-plus"></i> Tambah Peminjaman
            </button>
        </div>
    </div>
    <div class="card-body table-responsive p-0">
        <table class="table table-bordered table-striped table-sm mb-0">
            <thead class="thead-light">
                <tr>
                    <th width="50">No.</th>
                    <th>Kode Peminjaman</th>
                    <th>Kode Buku</th>
                    <th>Judul Buku</th>
                    <th>Pengarang</th>
                    <th>Tgl Pinjam</th>
                    <th>Batas Kembali</th>
                    <th>Durasi</th>
                    <th>Status</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody id="tbody-peminjaman">
                <tr>
                    <td colspan="10" class="text-center text-muted">
                        Belum ada data peminjaman.
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</div>

<!-- Modal Pop-up: Form Tambah Peminjaman -->
<div class="modal fade" id="modalTambahPeminjaman" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header bg-success text-white">
                <h5 class="modal-title">
                    <i class="fas fa-plus-circle mr-2"></i>Tambah Peminjaman Buku
                </h5>
                <button type="button" class="close text-white" data-dismiss="modal">
                    <span>&times;</span>
                </button>
            </div>
            <div class="modal-body">
                <!-- Pesan alert di dalam modal (tersembunyi) -->
                <div id="modal-alert" class="alert" style="display:none;"></div>

                <!-- Input tersembunyi menyimpan id_member yang sedang aktif -->
                <input type="hidden" id="hidden-id-member">

                <div class="form-group">
                    <label for="kode_buku">Kode Buku <span class="text-danger">*</span></label>
                    <input type="text" id="kode_buku" class="form-control"
                           placeholder="Masukkan kode buku..." autocomplete="off">
                    <small class="form-text text-muted">
                        Masukkan kode buku yang akan dipinjam.
                    </small>
                </div>

                <div class="form-group">
                    <label for="tanggal_pinjam">
                        Tanggal Pinjam <span class="text-danger">*</span>
                    </label>
                    <input type="date" id="tanggal_pinjam" class="form-control">
                </div>

                <div class="form-group">
                    <label for="durasi_pinjam">
                        Lama Pinjam (hari) <span class="text-danger">*</span>
                    </label>
                    <input type="number" id="durasi_pinjam" class="form-control"
                           value="3" min="1" max="60">
                    <small class="form-text text-muted">
                        Default 3 hari. Tanggal kembali dihitung otomatis.
                    </small>
                </div>

                <div class="form-group mb-0">
                    <label>Perkiraan Tanggal Kembali</label>
                    <!-- readonly: tidak bisa diedit, hanya tampilkan hasil kalkulasi -->
                    <input type="text" id="preview_batas_kembali"
                           class="form-control" readonly
                           style="background:#f4f6f9;">
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">
                    Batal
                </button>
                <button type="button" class="btn btn-success" id="btn-simpan-peminjaman">
                    <i class="fas fa-save mr-1"></i> Simpan
                </button>
            </div>
        </div>
    </div>
</div>
```

#### Langkah 3.5 — Tambah JavaScript untuk Modal dan Simpan

Tambahkan di dalam blok `<script>`:

```javascript
// Set tanggal hari ini sebagai default di form
document.getElementById('tanggal_pinjam').value = new Date().toISOString().split('T')[0];
updatePreviewBatasKembali();

// Hitung dan tampilkan prakiraan tanggal kembali secara real-time
function updatePreviewBatasKembali() {
    const tanggal = document.getElementById('tanggal_pinjam').value;
    const durasi  = parseInt(document.getElementById('durasi_pinjam').value) || 3;
    if (tanggal) {
        const batas = new Date(tanggal);
        batas.setDate(batas.getDate() + durasi);
        // Format tanggal ke Bahasa Indonesia
        document.getElementById('preview_batas_kembali').value =
            batas.toLocaleDateString('id-ID', {
                weekday: 'long', year: 'numeric', month: 'long', day: 'numeric'
            });
    }
}

// Perbarui preview setiap kali tanggal atau durasi berubah
document.getElementById('tanggal_pinjam').addEventListener('change', updatePreviewBatasKembali);
document.getElementById('durasi_pinjam').addEventListener('input', updatePreviewBatasKembali);

// Ketika klik tombol "Tambah Peminjaman", buka modal
document.getElementById('btn-tambah-peminjaman').addEventListener('click', function() {
    document.getElementById('kode_buku').value      = '';
    document.getElementById('tanggal_pinjam').value = new Date().toISOString().split('T')[0];
    document.getElementById('durasi_pinjam').value  = 3;
    document.getElementById('modal-alert').style.display = 'none';
    updatePreviewBatasKembali();
    $('#modalTambahPeminjaman').modal('show'); // Bootstrap 4 modal
});

// Ketika klik tombol "Simpan" di dalam modal
document.getElementById('btn-simpan-peminjaman').addEventListener('click', function() {
    const kode_buku      = document.getElementById('kode_buku').value.trim();
    const tanggal_pinjam = document.getElementById('tanggal_pinjam').value;
    const durasi_pinjam  = document.getElementById('durasi_pinjam').value;
    const id_member      = document.getElementById('hidden-id-member').value;

    // Validasi: semua field wajib harus diisi
    if (!kode_buku || !tanggal_pinjam || !durasi_pinjam) {
        showModalAlert('danger', 'Semua field wajib diisi.');
        return; // hentikan eksekusi, jangan kirim ke server
    }

    // Kirim data ke server
    fetch(BASE_URL + 'peminjaman/store', {
        method: 'POST',
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
        body: new URLSearchParams({ kode_buku, tanggal_pinjam, durasi_pinjam, id_member })
    })
    .then(r => r.json())
    .then(function(res) {
        if (res.success) {
            $('#modalTambahPeminjaman').modal('hide'); // tutup modal
            renderTabel(res.peminjaman);     // perbarui tabel
            showToast('success', res.message);
        } else {
            showModalAlert('danger', res.message);
        }
    });
});

// Perbarui tampilan dan tabel setelah anggota ditemukan
function tampilkanInfoAnggota(member, peminjaman) {
    currentMemberId = member.id_member;
    document.getElementById('anggota-kode').textContent    = member.code_member;
    document.getElementById('anggota-nama').textContent    = member.name_member;
    document.getElementById('anggota-email').textContent   = member.email_member   || '-';
    document.getElementById('anggota-telepon').textContent = member.phone_member   || '-';
    document.getElementById('anggota-alamat').textContent  = member.address_member || '-';
    document.getElementById('anggota-join').textContent    = member.join_date      || '-';
    document.getElementById('hidden-id-member').value      = member.id_member;

    document.getElementById('info-anggota').style.display    = 'block';
    document.getElementById('card-peminjaman').style.display = 'block';

    renderTabel(peminjaman);
}

// Render (gambar ulang) isi tabel peminjaman dari array data
function renderTabel(peminjaman) {
    const tbody = document.getElementById('tbody-peminjaman');

    if (!peminjaman || peminjaman.length === 0) {
        tbody.innerHTML = '<tr><td colspan="10" class="text-center text-muted">Belum ada data peminjaman.</td></tr>';
        return;
    }

    let html = '';
    peminjaman.forEach(function(p, i) {
        const badge = p.status === 'dipinjam'
            ? '<span class="badge badge-warning">Dipinjam</span>'
            : '<span class="badge badge-success">Dikembalikan</span>';

        // Tombol kembalikan hanya muncul kalau status masih "dipinjam"
        const aksi = p.status === 'dipinjam'
            ? `<button class="btn btn-xs btn-info btn-kembalikan"
                       data-id="${p.id_peminjaman}">
                   <i class="fas fa-undo-alt"></i> Kembalikan
               </button>`
            : '-';

        html += `<tr>
            <td>${i + 1}</td>
            <td>${p.kode_peminjaman}</td>
            <td>${p.code_book}</td>
            <td>${p.title_book}</td>
            <td>${p.author_book}</td>
            <td>${p.tanggal_pinjam}</td>
            <td>${p.batas_kembali}</td>
            <td>${p.durasi_pinjam} hari</td>
            <td>${badge}</td>
            <td>${aksi}</td>
        </tr>`;
    });

    tbody.innerHTML = html;
}

// Tampilkan pesan alert di dalam modal
function showModalAlert(type, pesan) {
    const el = document.getElementById('modal-alert');
    el.className      = 'alert alert-' + type;
    el.textContent    = pesan;
    el.style.display  = 'block';
}

// Tampilkan notifikasi pop-up kecil di pojok kanan atas
function showToast(type, pesan) {
    const warna = type === 'success' ? '#28a745' : '#dc3545';
    const div   = document.createElement('div');
    div.style.cssText = `
        position: fixed; top: 20px; right: 20px; z-index: 9999;
        background: ${warna}; color: #fff;
        padding: 12px 20px; border-radius: 6px;
        box-shadow: 0 4px 12px rgba(0,0,0,0.2); font-size: 14px;
    `;
    div.innerHTML = `<i class="fas fa-check-circle mr-2"></i>${pesan}`;
    document.body.appendChild(div);
    setTimeout(() => div.remove(), 3000); // hilangkan setelah 3 detik
}
```

> **Perbarui fungsi `cariAnggota()`** — sekarang fungsi `tampilkanInfoAnggota()` menerima dua argumen (member + peminjaman):
>
> ```javascript
> if (res.success) {
>     tampilkanInfoAnggota(res.member, res.peminjaman); // kirim keduanya
> }
> ```

---

### Tahap 4: Pengujian Fitur 3

| Skenario Uji | Langkah | Hasil yang Diharapkan |
|---|---|---|
| Simpan berhasil | Kode buku valid, isi semua field | Modal tutup, baris baru muncul di tabel, notifikasi hijau muncul |
| Kode buku tidak ada | Isi kode buku yang salah | Muncul pesan error merah di dalam modal |
| Buku sedang dipinjam | Isi kode buku yang sudah dipinjam | Muncul pesan "Buku sedang dipinjam..." |
| Form tidak lengkap | Klik Simpan tanpa isi kode buku | Muncul pesan "Semua field wajib diisi" |
| Kalkulasi tanggal | Ubah durasi pinjam | Preview tanggal kembali berubah real-time |

---

## 7. Fitur 4 — Tombol Pengembalian Buku

### Tahap 1: Analisis

**Pertanyaan yang harus dijawab:**

- **Siapa yang menggunakan?** → Petugas saat anggota mengembalikan buku
- **Bagaimana tombol ditampilkan?** → Hanya muncul pada baris dengan status "Dipinjam"
- **Apa yang terjadi saat tombol diklik?** → Konfirmasi dulu, lalu kirim request ke server
- **Apa yang diperbarui server?** → Status menjadi `dikembalikan`, isi `tanggal_dikembalikan` dengan hari ini
- **Apa yang diperbarui di halaman?** → Tabel diperbarui tanpa reload, tombol di baris tersebut hilang

**Komponen yang dibutuhkan:**

| Komponen | File | Fungsi |
|----------|------|--------|
| Route | `app/Config/Routes.php` | Tambah `POST /peminjaman/kembalikan/(:num)` |
| Controller | `PeminjamanController.php` | Method `kembalikan($id_peminjaman)` |
| View / JS | `peminjaman/index.php` | Event listener pada tombol Kembalikan |

---

### Tahap 2: Desain

**Alur kerja pengembalian:**

```
Petugas klik tombol [Kembalikan] di baris buku
         ↓
Muncul kotak konfirmasi:
"Kembalikan buku ini?"   [OK] [Batal]
         ↓
Klik OK
         ↓
JavaScript kirim POST ke /peminjaman/kembalikan/{id}
         ↓
Controller ambil data peminjaman berdasarkan id
         ↓
Update status = 'dikembalikan'
Isi tanggal_dikembalikan = tanggal hari ini
         ↓
Kirim JSON {success: true, peminjaman: [...]}
         ↓
JavaScript perbarui tabel (baris berubah: badge hijau, tombol hilang)
```

> **Kenapa perlu konfirmasi?** Pengembalian adalah aksi yang tidak mudah dibatalkan. Kotak konfirmasi mencegah petugas tidak sengaja klik tombol dan mengubah status buku.

---

### Tahap 3: Implementasi

#### Langkah 3.1 — Tambah Method `kembalikan()` ke Controller

Tambahkan di `app/Controllers/PeminjamanController.php`:

```php
// Proses pengembalian buku
public function kembalikan($id_peminjaman)
{
    $peminjamanModel = new PeminjamanModel();

    // 1. Cek apakah data peminjaman dengan ID ini ada
    $data = $peminjamanModel->find($id_peminjaman);
    if (!$data) {
        return $this->response->setJSON([
            'success' => false,
            'message' => 'Data peminjaman tidak ditemukan.'
        ]);
    }

    // 2. Update: ubah status dan isi tanggal dikembalikan
    $isUpdated = $peminjamanModel->update($id_peminjaman, [
        'status'               => 'dikembalikan',
        'tanggal_dikembalikan' => date('Y-m-d'), // tanggal hari ini
    ]);

    if ($isUpdated) {
        // 3. Ambil data terbaru milik anggota ini untuk dirender ulang
        $peminjaman = $peminjamanModel->getByMember($data['id_member']);
        return $this->response->setJSON([
            'success'    => true,
            'message'    => 'Buku berhasil dikembalikan.',
            'peminjaman' => $peminjaman
        ]);
    }

    return $this->response->setJSON([
        'success' => false,
        'message' => 'Gagal memproses pengembalian.'
    ]);
}
```

> **`$peminjamanModel->find($id_peminjaman)`** → mencari satu baris di tabel berdasarkan primary key. Seperti mencari data di Excel berdasarkan nomor baris.

> **`$peminjamanModel->update($id, $data)`** → memperbarui kolom tertentu pada baris dengan ID tersebut. Kolom lain tidak berubah.

#### Langkah 3.2 — Daftarkan Route

```php
$routes->post('/peminjaman/kembalikan/(:num)', 'PeminjamanController::kembalikan/$1');
```

> **`(:num)`** adalah placeholder untuk angka berubah-ubah. Misal: `/peminjaman/kembalikan/5` → memanggil `kembalikan(5)`. `$1` artinya ambil nilai dari placeholder pertama dan kirim sebagai argumen pertama method.

#### Langkah 3.3 — Tambah Event Listener di JavaScript

Di fungsi `renderTabel()`, setelah `tbody.innerHTML = html;`, tambahkan:

```javascript
// Pasang event listener pada setiap tombol "Kembalikan" yang baru dibuat
document.querySelectorAll('.btn-kembalikan').forEach(function(btn) {
    btn.addEventListener('click', function() {
        if (confirm('Kembalikan buku ini?')) {
            kembalikanBuku(this.dataset.id);
        }
    });
});
```

Tambahkan fungsi `kembalikanBuku()`:

```javascript
function kembalikanBuku(id_peminjaman) {
    fetch(BASE_URL + 'peminjaman/kembalikan/' + id_peminjaman, {
        method: 'POST'
    })
    .then(r => r.json())
    .then(function(res) {
        if (res.success) {
            renderTabel(res.peminjaman); // perbarui tabel
            showToast('success', res.message);
        } else {
            alert(res.message);
        }
    });
}
```

> **`this.dataset.id`** → mengambil nilai dari atribut `data-id` pada tombol HTML. Setiap tombol Kembalikan yang dibuat di `renderTabel()` sudah memiliki `data-id="${p.id_peminjaman}"`.

> **`querySelectorAll('.btn-kembalikan')`** → mencari semua elemen HTML yang memiliki class `btn-kembalikan`. Ini penting karena tombol-tombol dibuat oleh JavaScript (bukan langsung di HTML), sehingga event listener harus dipasang **setelah** tombol tersebut ada di halaman.

---

### Tahap 4: Pengujian Fitur 4

| Skenario Uji | Langkah | Hasil yang Diharapkan |
|---|---|---|
| Kembalikan buku aktif | Klik Kembalikan, konfirmasi OK | Badge berubah dari kuning "Dipinjam" ke hijau "Dikembalikan", tombol hilang |
| Batalkan pengembalian | Klik Kembalikan, pilih Batal di konfirmasi | Tidak terjadi perubahan apa pun |
| Kembalikan semua buku | Kembalikan semua buku satu per satu | Semua baris menampilkan "Dikembalikan", tidak ada tombol tersisa |
| Buku yang sudah kembali | Lihat baris status "Dikembalikan" | Kolom Aksi menampilkan `-`, tidak ada tombol |

---

## 8. Fitur 5 — Denda Keterlambatan

### Tahap 1: Analisis

**Konteks:** Anggota yang mengembalikan buku melebihi `batas_kembali` dikenakan denda. Nominal denda ditentukan dan diinput langsung oleh **petugas perpustakaan** saat memproses pengembalian.

**Pertanyaan yang harus dijawab:**

- **Kapan denda diinput?** → Saat petugas memproses pengembalian, melalui form di modal konfirmasi
- **Siapa yang menentukan denda?** → Petugas perpustakaan, sesuai kebijakan yang berlaku
- **Informasi apa yang membantu petugas?** → Sistem menampilkan otomatis berapa hari keterlambatan di modal
- **Di mana denda disimpan?** → Kolom `denda` di tabel `peminjaman`
- **Di mana denda ditampilkan?** → Kolom Denda di tabel peminjaman (halaman index dan daftar semua)
- **Bagaimana tampilan notifikasinya?** → Pesan sukses menyebutkan jumlah denda yang diinput

**Komponen yang dibutuhkan:**

| Komponen | File | Perubahan |
|----------|------|-----------|
| Migration | `Migrations/2026-06-18-000001_AddDendaToPeminjamanTable.php` | Tambah kolom `denda` ke tabel |
| Model | `PeminjamanModel.php` | Tambah `denda` ke `$allowedFields` |
| Controller | `PeminjamanController.php` | Terima input denda dari petugas di method `kembalikan()` |
| View | `peminjaman/index.php` | Modal konfirmasi pengembalian + input denda |
| View | `peminjaman/semua_peminjaman.php` | Tambah kolom Denda di tabel |

---

### Tahap 2: Desain

**Informasi yang membantu petugas mengisi denda:**

Sistem menghitung otomatis selisih hari keterlambatan di sisi tampilan (JavaScript) untuk membantu petugas. Namun petugas tetap yang menentukan nominal akhir sesuai kebijakan perpustakaan.

| Batas Kembali | Tgl Dikembalikan | Keterlambatan (info) | Denda (input petugas) |
|---|---|---|---|
| 20 Juni 2026 | 20 Juni 2026 | 0 hari (tepat waktu) | Rp 0 |
| 20 Juni 2026 | 23 Juni 2026 | 3 hari terlambat | contoh: Rp 3.000 |
| 20 Juni 2026 | 30 Juni 2026 | 10 hari terlambat | contoh: Rp 10.000 |
| 20 Juni 2026 | 18 Juni 2026 | 2 hari lebih awal | Rp 0 |

**Mockup Modal Konfirmasi Pengembalian:**

```
┌──────────────────────────────────────────────────┐
│  Konfirmasi Pengembalian Buku              [✕]   │
├──────────────────────────────────────────────────┤
│  Kode Buku        : BK-001                       │
│  Judul Buku       : Pemrograman Web Dasar        │
│  Batas Kembali    : 2026-06-20                   │
│  Tgl Dikembalikan : 2026-06-23   (Terlambat 3 hari) │
│  ─────────────────────────────────────────────── │
│  Denda (Rp)                                      │
│  [ 0 __________________ ]                        │
│  Masukkan nominal denda. Isi 0 jika tidak ada.   │
├──────────────────────────────────────────────────┤
│  [ Batal ]              [ ✓ Konfirmasi Pengembalian ] │
└──────────────────────────────────────────────────┘
```

**Mockup tampilan kolom Denda di tabel setelah pengembalian:**

```
┌──────────┬──────────┬──────────────────────────┐
│  Status  │  Denda   │  Aksi                    │
├──────────┼──────────┼──────────────────────────┤
│ Dipinjam │    -     │ [Kembalikan]             │
│ Kembali  │  Rp 0    │ -  (tepat waktu)         │
│ Kembali  │ Rp 3.000 │ -  (terlambat, merah)    │
└──────────┴──────────┴──────────────────────────┘
```

**Alur kerja lengkap pengembalian + denda:**

```
Klik [Kembalikan]
         ↓
Modal konfirmasi muncul
(tampil: kode buku, judul, batas kembali, info keterlambatan)
         ↓
Petugas isi nominal Denda (Rp) → klik [Konfirmasi Pengembalian]
         ↓
JavaScript kirim POST ke Controller:
  id_peminjaman (dari URL), denda (dari input)
         ↓
Controller simpan ke database:
  status='dikembalikan', tanggal_dikembalikan=hari_ini, denda=input
         ↓
Kirim JSON: {success, message (menyebut denda), peminjaman}
         ↓
JavaScript tampilkan notifikasi toast
         ↓
Tabel diperbarui: kolom Denda terisi
```

---

### Tahap 3: Implementasi

#### Langkah 3.1 — Buat Migration untuk Kolom `denda`

Buat file: `app/Database/Migrations/2026-06-18-000001_AddDendaToPeminjamanTable.php`

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class AddDendaToPeminjamanTable extends Migration
{
    public function up()
    {
        $this->forge->addColumn('peminjaman', [
            'denda' => [
                'type'       => 'INT',
                'constraint' => 11,
                'unsigned'   => true,
                'default'    => 0,
                'null'       => false,
                'after'      => 'tanggal_dikembalikan', // letakkan setelah kolom ini
            ],
        ]);
    }

    public function down()
    {
        $this->forge->dropColumn('peminjaman', 'denda');
    }
}
```

> **`addColumn()`** berbeda dengan `createTable()` — fungsi ini menambahkan kolom baru ke tabel yang **sudah ada**, bukan membuat tabel baru. Parameter `'after'` menentukan posisi kolom baru di antara kolom lainnya.

Jalankan migration:

```bash
php spark migrate
```

#### Langkah 3.2 — Update `$allowedFields` di Model

Buka `app/Models/PeminjamanModel.php`, tambahkan `'denda'` ke array `$allowedFields`:

```php
protected $allowedFields = [
    'kode_peminjaman',
    'id_member',
    'id_book',
    'tanggal_pinjam',
    'batas_kembali',
    'tanggal_dikembalikan',
    'durasi_pinjam',
    'status',
    'denda',   // ← tambahkan baris ini
];
```

> **Ingat:** Kolom yang tidak ada di `$allowedFields` tidak akan tersimpan ke database, meskipun datanya sudah dikirim dari Controller. Ini fitur keamanan CodeIgniter untuk mencegah *mass assignment*.

#### Langkah 3.3 — Terima Input Denda di Controller

Perbarui method `kembalikan()` di `app/Controllers/PeminjamanController.php`:

```php
public function kembalikan($id_peminjaman)
{
    $peminjamanModel = new PeminjamanModel();
    $data            = $peminjamanModel->find($id_peminjaman);

    if (!$data) {
        return $this->response->setJSON([
            'success' => false,
            'message' => 'Data peminjaman tidak ditemukan.'
        ]);
    }

    // Denda diinput langsung oleh petugas saat proses pengembalian
    $denda          = (int) $this->request->getPost('denda');
    $tanggalKembali = date('Y-m-d');

    // Hitung keterlambatan hanya untuk keperluan pesan informasi
    $hariTerlambat = (int) floor(
        (strtotime($tanggalKembali) - strtotime($data['batas_kembali'])) / 86400
    );

    $isUpdated = $peminjamanModel->update($id_peminjaman, [
        'status'               => 'dikembalikan',
        'tanggal_dikembalikan' => $tanggalKembali,
        'denda'                => $denda,
    ]);

    if ($isUpdated) {
        $peminjaman = $peminjamanModel->getByMember($data['id_member']);

        $message = $denda > 0
            ? "Buku berhasil dikembalikan. Denda Rp "
              . number_format($denda, 0, ',', '.')
              . ($hariTerlambat > 0 ? " (terlambat {$hariTerlambat} hari)." : '.')
            : 'Buku berhasil dikembalikan' . ($hariTerlambat <= 0 ? ' tepat waktu.' : '.');

        return $this->response->setJSON([
            'success'    => true,
            'message'    => $message,
            'denda'      => $denda,
            'peminjaman' => $peminjaman,
        ]);
    }

    return $this->response->setJSON([
        'success' => false,
        'message' => 'Gagal memproses pengembalian.'
    ]);
}
```

> **`$this->request->getPost('denda')`:** Membaca nilai field `denda` yang dikirim dari form JavaScript via metode POST. Petugas mengisi nilai ini di modal konfirmasi pengembalian.

> **Kenapa `hariTerlambat` masih dihitung?** Hanya untuk keperluan pesan notifikasi (misalnya "terlambat 3 hari"), bukan untuk menentukan denda. Nominal denda sepenuhnya dari input petugas.

#### Langkah 3.4 — Upgrade Tombol Kembalikan: dari `confirm()` ke Modal + Input Denda

> **Perhatian:** Langkah ini **memperbarui** kode yang dibuat di Fitur 4. Di Fitur 4, tombol Kembalikan menggunakan `confirm()` (kotak dialog bawaan browser) tanpa input denda. Di Fitur 5 ini, kita **mengganti** alur tersebut dengan sebuah modal konfirmasi yang lebih lengkap — menampilkan info buku, info keterlambatan, dan input nominal denda.

**Langkah 3.4.1 — Tambah kolom Denda di tabel**

Pada bagian `<thead>`:

```html
<th>Status</th>
<th>Denda</th>   <!-- ← tambahkan -->
<th>Aksi</th>
```

**Langkah 3.4.2 — Tambah data atribut pada tombol Kembalikan**

Tombol Kembalikan perlu membawa data buku agar modal bisa menampilkan informasi lengkap. Ubah bagian `aksi` di fungsi `renderTabel()`:

```javascript
const aksi = p.status === 'dipinjam'
    ? `<button class="btn btn-xs btn-info btn-kembalikan"
            data-id="${p.id_peminjaman}"
            data-kode="${p.code_book}"
            data-judul="${p.title_book}"
            data-batas="${p.batas_kembali}">
            <i class="fas fa-undo-alt"></i> Kembalikan
       </button>`
    : '-';
```

> `data-id`, `data-kode`, `data-judul`, `data-batas` adalah atribut HTML khusus yang menyimpan data di dalam elemen tombol. JavaScript bisa membacanya lewat `btn.dataset.id`, `btn.dataset.judul`, dst.

**Langkah 3.4.3 — Tambahkan HTML Modal Konfirmasi Pengembalian**

Letakkan di bawah modal Tambah Peminjaman, sebelum tag `<?= $this->endSection() ?>`:

```html
<!-- Modal Konfirmasi Pengembalian Buku -->
<div class="modal fade" id="modalKembalikan" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header bg-info text-white">
                <h5 class="modal-title">
                    <i class="fas fa-undo-alt mr-2"></i>Konfirmasi Pengembalian Buku
                </h5>
                <button type="button" class="close text-white" data-dismiss="modal">
                    <span>&times;</span>
                </button>
            </div>
            <div class="modal-body">
                <input type="hidden" id="kembalikan-id">
                <table class="table table-sm table-borderless mb-3">
                    <tr>
                        <td width="150"><strong>Kode Buku</strong></td>
                        <td>: <span id="kembalikan-kode-buku"></span></td>
                    </tr>
                    <tr>
                        <td><strong>Judul Buku</strong></td>
                        <td>: <span id="kembalikan-judul"></span></td>
                    </tr>
                    <tr>
                        <td><strong>Batas Kembali</strong></td>
                        <td>: <span id="kembalikan-batas"></span></td>
                    </tr>
                    <tr>
                        <td><strong>Tgl Dikembalikan</strong></td>
                        <td>: <span id="kembalikan-tgl-kembali"></span>
                            &nbsp;
                            <span id="kembalikan-info-terlambat" class="font-weight-bold"></span>
                        </td>
                    </tr>
                </table>
                <hr>
                <div class="form-group mb-0">
                    <label for="input_denda"><strong>Denda (Rp)</strong></label>
                    <input type="number" id="input_denda" class="form-control"
                           value="0" min="0" step="500" placeholder="0">
                    <small class="form-text text-muted">
                        Masukkan nominal denda yang dikenakan. Isi 0 jika tidak ada denda.
                    </small>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Batal</button>
                <button type="button" class="btn btn-info" id="btn-konfirmasi-kembalikan">
                    <i class="fas fa-check mr-1"></i> Konfirmasi Pengembalian
                </button>
            </div>
        </div>
    </div>
</div>
```

**Langkah 3.4.4 — Update event listener tombol Kembalikan di JavaScript**

Ganti bagian `querySelectorAll('.btn-kembalikan')` menjadi:

```javascript
document.querySelectorAll('.btn-kembalikan').forEach(btn => {
    btn.addEventListener('click', function() {
        const batasKembali = this.dataset.batas;
        const today        = new Date().toISOString().split('T')[0];

        // Isi data ke dalam modal
        document.getElementById('kembalikan-id').value               = this.dataset.id;
        document.getElementById('kembalikan-kode-buku').textContent  = this.dataset.kode;
        document.getElementById('kembalikan-judul').textContent      = this.dataset.judul;
        document.getElementById('kembalikan-batas').textContent      = batasKembali;
        document.getElementById('kembalikan-tgl-kembali').textContent = today;
        document.getElementById('input_denda').value                 = 0;

        // Hitung keterlambatan untuk info petugas (bukan untuk menentukan denda)
        const diffHari = Math.floor((new Date(today) - new Date(batasKembali)) / 86400000);
        const infoEl   = document.getElementById('kembalikan-info-terlambat');
        if (diffHari > 0) {
            infoEl.textContent = `(Terlambat ${diffHari} hari)`;
            infoEl.className   = 'font-weight-bold text-danger';
        } else {
            infoEl.textContent = '(Tepat waktu)';
            infoEl.className   = 'font-weight-bold text-success';
        }

        $('#modalKembalikan').modal('show');
    });
});
```

**Langkah 3.4.5 — Tambah event listener tombol Konfirmasi di modal**

```javascript
document.getElementById('btn-konfirmasi-kembalikan').addEventListener('click', function() {
    const id    = document.getElementById('kembalikan-id').value;
    const denda = parseInt(document.getElementById('input_denda').value) || 0;
    $('#modalKembalikan').modal('hide');
    kembalikanBuku(id, denda);
});
```

**Langkah 3.4.6 — Update fungsi `kembalikanBuku()` untuk kirim denda**

```javascript
function kembalikanBuku(id_peminjaman, denda) {
    fetch(BASE_URL + 'peminjaman/kembalikan/' + id_peminjaman, {
        method: 'POST',
        headers: {'Content-Type': 'application/x-www-form-urlencoded'},
        body: 'denda=' + encodeURIComponent(denda)
    })
    .then(r => r.json())
    .then(res => {
        if (res.success) {
            renderTabel(res.peminjaman);
            showToast('success', res.message);
        } else {
            alert(res.message);
        }
    });
}
```

> **Kenapa `encodeURIComponent(denda)`?** Fungsi ini mengubah nilai agar aman dikirim di URL-encoded body. Untuk angka ini tidak kritis, tapi merupakan praktik yang baik untuk semua nilai yang dikirim via form.

**Langkah 3.4.7 — Tambah logika sel Denda di `renderTabel()`**

```javascript
const denda = parseInt(p.denda) || 0;
let dendaCell;

if (p.status === 'dipinjam') {
    dendaCell = '<span class="text-muted">-</span>';
} else if (denda > 0) {
    dendaCell = `<span class="text-danger font-weight-bold">Rp ${denda.toLocaleString('id-ID')}</span>`;
} else {
    dendaCell = '<span class="text-success">Rp 0</span>';
}
```

#### Langkah 3.5 — Tambah Kolom Denda di View `peminjaman/semua_peminjaman.php`

File `semua_peminjaman.php` menampilkan seluruh data peminjaman dari semua anggota, dirender di sisi server (PHP), bukan JavaScript.

**Pada `<thead>`:**

```html
<th>Status</th>
<th>Denda</th>   <!-- ← tambahkan -->
```

**Pada setiap baris `<tbody>`**, tambahkan sel denda setelah sel status:

```php
<td>
    <?php if ($p['status'] === 'dipinjam'): ?>
        <span class="text-muted">-</span>
    <?php elseif ((int)$p['denda'] > 0): ?>
        <span class="text-danger font-weight-bold">
            Rp <?= number_format((int)$p['denda'], 0, ',', '.') ?>
        </span>
    <?php else: ?>
        <span class="text-success">Rp 0</span>
    <?php endif; ?>
</td>
```

> **`number_format($angka, 0, ',', '.')`:** Memformat angka menjadi format rupiah Indonesia.
> - Parameter ke-2 (`0`) = jumlah desimal
> - Parameter ke-3 (`','`) = pemisah desimal
> - Parameter ke-4 (`'.'`) = pemisah ribuan
> - Contoh: `3000` → `3.000`

---

### Tahap 4: Pengujian Fitur 5

| Skenario Uji | Cara Uji | Hasil yang Diharapkan |
|---|---|---|
| Klik Kembalikan → modal muncul | Klik tombol Kembalikan pada baris "Dipinjam" | Modal terbuka, menampilkan kode buku, judul, batas kembali, tanggal hari ini, info keterlambatan |
| Info tepat waktu di modal | Kembalikan buku yang `batas_kembali`-nya hari ini atau lebih awal | Modal menampilkan teks hijau "(Tepat waktu)" |
| Info terlambat di modal | Ubah `batas_kembali` 3 hari lalu di phpMyAdmin, klik Kembalikan | Modal menampilkan teks merah "(Terlambat 3 hari)" |
| Input denda lalu konfirmasi | Isi denda Rp 3.000 di modal, klik Konfirmasi | Notifikasi "Denda Rp 3.000", kolom Denda tabel tampil merah |
| Input denda Rp 0 | Isi denda 0 di modal, klik Konfirmasi | Notifikasi "dikembalikan", kolom Denda tampil "Rp 0" hijau |
| Batal di modal | Klik tombol Batal | Modal tertutup, tidak ada perubahan di tabel |
| Cek kolom denda buku belum kembali | Lihat baris status "Dipinjam" | Kolom Denda menampilkan `-` |

> **Tips simulasi pengujian:** Untuk menguji info keterlambatan di modal tanpa menunggu hari sungguhan, langsung ubah nilai kolom `batas_kembali` di phpMyAdmin menjadi tanggal yang sudah lewat (misalnya 3–10 hari yang lalu), lalu klik tombol Kembalikan di aplikasi.

---

## 9. Integrasi dan Pengujian Keseluruhan

### Langkah Terakhir: Tambah Menu di Sidebar

Setelah keempat fitur selesai, tambahkan menu di navigasi samping agar mudah diakses.

Buka: `app/Views/layouts/partials/sidebar.php`

Tambahkan blok menu Peminjaman:

```html
<li class="nav-item has-treeview">
    <a href="#" class="nav-link">
        <i class="nav-icon fas fa-exchange-alt"></i>
        <p>
            Peminjaman
            <i class="right fas fa-angle-left"></i>
        </p>
    </a>
    <ul class="nav nav-treeview">
        <li class="nav-item">
            <a href="<?= base_url('/peminjaman') ?>" class="nav-link">
                <i class="far fa-circle nav-icon"></i>
                <p>Tambah Peminjaman</p>
            </a>
        </li>
        <li class="nav-item">
            <a href="<?= base_url('/peminjaman/semua') ?>" class="nav-link">
                <i class="far fa-circle nav-icon"></i>
                <p>Daftar Peminjaman</p>
            </a>
        </li>
    </ul>
</li>
```

---

### Ringkasan Semua File yang Dibuat/Diubah

| No | File | Tindakan | Fungsi |
|----|------|----------|--------|
| 1 | `app/Database/Migrations/..._CreatePeminjamanTable.php` | Dibuat | Struktur tabel `peminjaman` |
| 2 | `app/Database/Migrations/..._AddDendaToPeminjamanTable.php` | Dibuat | Tambah kolom `denda` ke tabel |
| 3 | `app/Models/PeminjamanModel.php` | Dibuat | Query database peminjaman |
| 4 | `app/Controllers/PeminjamanController.php` | Dibuat | Logika semua fitur, denda diterima dari input petugas |
| 5 | `app/Views/peminjaman/index.php` | Dibuat | Tampilan + modal konfirmasi pengembalian + input denda |
| 6 | `app/Views/peminjaman/semua_peminjaman.php` | Dibuat | Daftar semua peminjaman + kolom denda |
| 7 | `app/Config/Routes.php` | Diubah | Daftarkan URL fitur |
| 8 | `app/Views/layouts/partials/sidebar.php` | Diubah | Tambah menu navigasi |

---

### Pengujian Skenario Lengkap (End-to-End)

Lakukan pengujian ini dari awal hingga akhir untuk memastikan semua fitur terhubung dengan benar:

**Skenario: Anggota meminjam buku lalu mengembalikannya**

1. Buka browser, akses `http://localhost/peminjaman`
2. Ketik kode anggota yang valid, klik **Cari** → Info anggota muncul
3. Klik **Tambah Peminjaman** → Modal muncul
4. Isi kode buku yang valid → Atur durasi menjadi 7 hari → Lihat preview tanggal kembali berubah
5. Klik **Simpan** → Modal tutup, tabel menampilkan baris baru dengan status "Dipinjam"
6. Klik **Kembalikan** pada baris tersebut → Modal konfirmasi muncul dengan info keterlambatan
7. Isi nominal denda → klik **Konfirmasi Pengembalian** → Status baris berubah menjadi "Dikembalikan", kolom Denda terisi, tombol hilang
8. Buka `http://localhost/peminjaman/semua` → Semua transaksi tampil

---

### Checklist Akhir Sebelum Selesai

Gunakan checklist ini untuk memastikan tidak ada yang terlewat:

- [ ] Tabel `peminjaman` sudah ada di database (jalankan `php spark migrate`)
- [ ] File `PeminjamanModel.php` sudah ada di `app/Models/`
- [ ] File `PeminjamanController.php` sudah ada di `app/Controllers/`
- [ ] Folder `app/Views/peminjaman/` sudah ada
- [ ] File `app/Views/peminjaman/index.php` sudah ada
- [ ] Route peminjaman sudah ditambahkan di `app/Config/Routes.php`
- [ ] Menu sidebar sudah ditambahkan
- [ ] Cari anggota valid → info muncul ✓
- [ ] Cari anggota tidak valid → pesan error muncul ✓
- [ ] Tambah peminjaman buku valid → berhasil disimpan ✓
- [ ] Tambah peminjaman buku sedang dipinjam oleh anggota lain → pesan error "Buku sedang dipinjam oleh anggota lain" muncul ✓
- [ ] Tambah peminjaman buku yang masih dipinjam oleh anggota yang sama → pesan error "Buku ini masih Anda pinjam" muncul ✓
- [ ] Buku yang sudah dikembalikan bisa dipinjam lagi oleh siapa saja ✓
- [ ] Tombol Kembalikan muncul hanya pada status "Dipinjam" ✓
- [ ] Setelah kembalikan → status berubah, tombol hilang ✓
- [ ] Klik Kembalikan → Modal konfirmasi pengembalian muncul ✓
- [ ] Modal menampilkan info keterlambatan (merah/hijau) secara otomatis ✓
- [ ] Input denda Rp 0, konfirmasi → kolom Denda menampilkan "Rp 0" hijau ✓
- [ ] Input denda > 0, konfirmasi → kolom Denda menampilkan nominal merah + notifikasi menyebut jumlah denda ✓
- [ ] Klik Batal di modal → tidak ada perubahan ✓
- [ ] Buku belum dikembalikan → kolom Denda menampilkan `-` ✓

---

### Troubleshooting: Masalah Umum

| Gejala | Kemungkinan Penyebab | Solusi |
|--------|---------------------|--------|
| Halaman 404 saat buka `/peminjaman` | Route belum didaftarkan | Cek `Routes.php`, tambahkan `$routes->get('/peminjaman', ...)` |
| "Anggota tidak ditemukan" padahal kode benar | Tabel `members` kosong atau kode berbeda | Cek di phpMyAdmin tabel `members`, pastikan data ada |
| "Buku tidak ditemukan" | Kode buku salah atau tabel `books` kosong | Cek tabel `books` di phpMyAdmin |
| Tabel peminjaman tidak muncul di halaman | `card-peminjaman` masih `display:none` | Pastikan fungsi `tampilkanInfoAnggota()` mengubah stylenya |
| Data tidak tersimpan meski tidak ada error | `$allowedFields` di Model tidak lengkap | Pastikan semua nama kolom ada di array `$allowedFields` |
| Tabel `peminjaman` tidak ada di database | Migration belum dijalankan | Jalankan `php spark migrate` di terminal |
| Error "Class PeminjamanController not found" | Namespace salah atau file belum disimpan | Cek baris pertama: `namespace App\Controllers;` dan nama file harus `PeminjamanController.php` |
| Buku tidak bisa dipinjam lagi setelah dikembalikan | Mungkin ada bug di status atau unique constraint | Cek di phpMyAdmin: status kolom harus `dikembalikan`, bukan `dipinjam` |
| Tombol Kembalikan tidak bereaksi | Event listener dipasang sebelum tombol ada | Pastikan `querySelectorAll` dipanggil SETELAH `tbody.innerHTML = html` |

---

## Penutup

Selamat! Kamu telah membangun fitur Peminjaman Buku secara penuh mengikuti alur SDLC — dari analisis kebutuhan, desain sistem, implementasi kode, hingga pengujian.

Rangkuman yang telah dipelajari:

| Konsep | Penjelasan Singkat |
|--------|-------------------|
| **SDLC** | Cara terstruktur membangun perangkat lunak: Analisis → Desain → Implementasi → Pengujian |
| **Migration** | Cara membuat/mengubah tabel database lewat kode PHP yang bisa diulang |
| **Model** | File PHP yang berkomunikasi dengan database |
| **Controller** | Otak fitur, menerima request dan mengkoordinasikan Model & View |
| **View** | Tampilan HTML yang dilihat pengguna di browser |
| **Route** | Daftar URL yang tersedia beserta Controller yang menanganinya |
| **AJAX / fetch()** | Teknik mengirim/menerima data tanpa reload halaman |
| **JSON** | Format data terstruktur untuk komunikasi antara PHP dan JavaScript |
| **JOIN** | Menggabungkan data dari dua tabel sekaligus dalam satu query |
| **Primary Key** | Kolom pengenal unik setiap baris (biasanya ID angka otomatis) |
| **Foreign Key** | Kolom yang merujuk ke baris di tabel lain (penghubung antar tabel) |
| **`strtotime()`** | Fungsi PHP mengubah teks tanggal menjadi angka detik, memudahkan perhitungan selisih hari |
| **`number_format()`** | Memformat angka menjadi format rupiah: `3000` → `3.000` |
| **`getPost('field')`** | Membaca nilai input yang dikirim dari form HTML via metode POST |
| **`data-*` atribut** | Cara menyimpan data di dalam elemen HTML agar bisa dibaca JavaScript |

---

*Dokumen tutorial ini dibuat sebagai panduan belajar membangun fitur Peminjaman Buku pada sistem Library Information System berbasis CodeIgniter 4.*
*Disusun dengan pendekatan SDLC per fitur, ditujukan untuk mahasiswa yang baru belajar pengembangan sistem informasi.*
