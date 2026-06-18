# Library Information System

Sistem Informasi Perpustakaan berbasis web yang dibangun menggunakan **CodeIgniter 4** dan **AdminLTE 3**. Aplikasi ini menyediakan fitur pengelolaan data buku, data anggota (member), laporan, dan cetak label buku.

---

## Fitur Utama

| Modul | Fitur |
|---|---|
| **Master Buku** | Tambah, lihat, edit, hapus data buku |
| **Master Member** | Tambah, lihat, edit, hapus data anggota perpustakaan |
| **Laporan Data Buku** | Tampilkan semua data buku dalam format laporan |
| **Cetak PDF Buku** | Cetak laporan data buku sebagai PDF |
| **Cetak Label Buku** | Cetak label/stiker untuk semua buku atau per buku |

---

## Teknologi yang Digunakan 

- **PHP** 8.2+
- **CodeIgniter** 4.7
- **MySQL** / MariaDB
- **AdminLTE** 3.2 (antarmuka)
- **Composer** (manajemen dependensi)

---

## Persyaratan Sistem

Pastikan perangkat Anda sudah terinstal:

- PHP 8.2 atau lebih tinggi
- MySQL / MariaDB
- Composer
- Web server (Apache/Nginx) — disarankan menggunakan **XAMPP** atau **Laragon**
- Extension PHP yang wajib aktif: `intl`, `mbstring`, `mysqlnd`, `json`, `libcurl`

---

## Instalasi

### Langkah 1 — Clone Repository

```bash
git clone https://github.com/grayos14/library_information_system.git
cd library_information_system
```

### Langkah 2 — Install Dependensi

```bash
composer install
```

### Langkah 3 — Konfigurasi Environment

Salin file `env` menjadi `.env`:

```bash
cp env .env
```

Buka file `.env` dan sesuaikan konfigurasi berikut:

```env
# Base URL aplikasi
app.baseURL = 'http://localhost:8081/'

# Ubah ke 'development' saat pengembangan
CI_ENVIRONMENT = development

# Konfigurasi Database
database.default.hostname = localhost
database.default.database = library_information_system
database.default.username = root
database.default.password =
database.default.DBDriver = MySQLi
database.default.port     = 3306
```

> **Catatan:** Port database di project ini dikonfigurasi ke `3307` (default Laragon). Sesuaikan dengan port MySQL yang Anda gunakan (XAMPP umumnya `3306`).

### Langkah 4 — Buat Database

Buka phpMyAdmin atau MySQL client, lalu buat database baru:

```sql
CREATE DATABASE library_information_system;
```

### Langkah 5 — Jalankan Migration

Migration akan membuat semua tabel yang diperlukan secara otomatis:

```bash
php spark migrate
```

Tabel yang akan dibuat:
- `books` — data buku
- `members` — data anggota perpustakaan
- `members_spark` — data anggota (versi Spark CLI)

### Langkah 6 — Jalankan Aplikasi

#### Opsi A — Menggunakan Server Bawaan CodeIgniter

```bash
php spark serve --port 8081
```

Buka browser dan akses: `http://localhost:8081`

#### Opsi B — Menggunakan XAMPP / Laragon

Letakkan folder project di dalam `htdocs` (XAMPP) atau `www` (Laragon), lalu akses melalui:

```
http://localhost/library_information_system/public
```

---

## Struktur Halaman

| URL | Keterangan |
|---|---|
| `/` | Halaman beranda |
| `/dashboard` | Dashboard utama |
| `/list/books` | Daftar semua buku |
| `/create/book` | Form tambah buku baru |
| `/edit/book/{id}` | Form edit data buku |
| `/list/members` | Daftar semua anggota |
| `/create/member` | Form tambah anggota baru |
| `/edit/member/{id}` | Form edit data anggota |
| `/laporan/buku` | Halaman laporan data buku |
| `/laporan/cetak-buku` | Cetak laporan buku (PDF) |
| `/laporan/label-buku` | Halaman cetak label buku |
| `/laporan/cetak-label-buku` | Cetak semua label buku |
| `/laporan/cetak-label-buku/{id}` | Cetak label satu buku |

---

## Struktur Folder Project

```
library_information_system/
├── app/
│   ├── Config/
│   │   ├── Routes.php          ← Daftar semua URL/route aplikasi
│   │   └── Database.php        ← Konfigurasi koneksi database
│   ├── Controllers/
│   │   ├── BookController.php
│   │   ├── MemberController.php
│   │   ├── MemberSparkController.php
│   │   ├── LaporanController.php
│   │   ├── DashboardController.php
│   │   └── UserController.php
│   ├── Models/
│   │   ├── MemberModel.php
│   │   └── MemberSparkModel.php
│   ├── Views/
│   │   ├── layouts/            ← Template utama (sidebar, header, footer)
│   │   ├── books/              ← View halaman buku
│   │   ├── members/            ← View halaman member
│   │   ├── members_spark/      ← View halaman member (Spark)
│   │   └── laporan/            ← View halaman laporan & cetak
│   └── Database/
│       └── Migrations/         ← File migration (struktur tabel database)
├── public/                     ← Document root web server
│   └── index.php
├── tutorial/                   ← Panduan langkah-demi-langkah (lihat bagian bawah)
├── vendor/                     ← Dependensi Composer (jangan diedit)
├── env                         ← Template konfigurasi environment
└── spark                       ← CLI tool CodeIgniter
```

---

## Perintah Spark yang Berguna

```bash
# Menjalankan server development
php spark serve --port 8081

# Menjalankan semua migration
php spark migrate

# Rollback migration terakhir
php spark migrate:rollback

# Membuat file migration baru
php spark make:migration NamaTable

# Membuat model baru
php spark make:model NamaModel

# Membuat controller baru
php spark make:controller NamaController

# Melihat semua route yang terdaftar
php spark routes
```

---

## Folder `tutorial/`

Folder ini berisi panduan langkah-demi-langkah untuk **mahasiswa yang mengerjakan atau mengembangkan project ini**. Setiap tutorial ditulis dengan penjelasan detail cocok untuk pemula.

### Daftar Tutorial

| File | Topik |
|---|---|
| [`membuat-modul-member.md`](tutorial/membuat-modul-member.md) | Ringkasan membuat modul Master Member (2 cara) |
| [`cara-1-copas-master-member.md`](tutorial/cara-1-copas-master-member.md) | Cara Copy-Paste: membuat modul Member dari awal |
| [`cara-2-spark-cli-master-member.md`](tutorial/cara-2-spark-cli-master-member.md) | Cara Spark CLI: membuat modul Member via terminal |
| [`tutorial-laporan-data-buku.md`](tutorial/tutorial-laporan-data-buku.md) | Menambah menu Laporan dan fitur Cetak PDF |
| [`tutorial-cetak-label-buku.md`](tutorial/tutorial-cetak-label-buku.md) | Membuat fitur cetak label/stiker buku |
| [`tutorial-sdlc-fitur-peminjaman-buku.md`](tutorial/tutorial-sdlc-fitur-peminjaman-buku.md) | Membangun fitur peminjaman buku mengikuti alur SDLC |

### Penjelasan Isi Tutorial

#### `membuat-modul-member.md`
Dokumen ringkas yang menjelaskan **dua pendekatan** untuk membuat modul Master Member. Cocok dibaca pertama kali sebelum memilih pendekatan mana yang akan diikuti (copy-paste atau Spark CLI). Berisi perbandingan kedua cara dan checklist akhir.

#### `cara-1-copas-master-member.md`
Tutorial **paling detail** untuk membuat modul Member dengan cara menduplikasi file-file modul Buku yang sudah ada. Cocok untuk mahasiswa yang lebih nyaman bekerja di VS Code tanpa terminal. Setiap langkah dijelaskan termasuk:
- Cara membuat file migration, model, controller, dan view
- Penjelasan mengapa setiap baris kode ditulis seperti itu
- Tabel perbandingan kode Buku vs kode Member
- Daftar error yang sering terjadi beserta solusinya

#### `cara-2-spark-cli-master-member.md`
Tutorial membuat modul Member menggunakan perintah `php spark` di terminal. File dibuat otomatis oleh CodeIgniter sehingga nama class dan namespace dijamin benar. Cocok untuk yang sudah familiar dengan terminal.

#### `tutorial-laporan-data-buku.md`
Panduan menambah menu **Laporan** di sidebar dan fitur **Cetak PDF** data buku. Tutorial ini mencakup pembuatan `LaporanController`, view laporan, dan halaman cetak yang dioptimalkan untuk printer/PDF.

#### `tutorial-cetak-label-buku.md`
Tutorial lanjutan dari Laporan. Menjelaskan cara membuat fitur **cetak label buku** — stiker yang ditempel di punggung atau sampul buku berisi kode, judul, penulis, dan informasi lainnya. Ada dua mode cetak: semua buku sekaligus dan satu buku saja.

#### `tutorial-sdlc-fitur-peminjaman-buku.md`
Tutorial membangun **fitur peminjaman buku dari nol** mengikuti alur **SDLC (Software Development Life Cycle)**. Setiap fitur dibangun melalui empat tahapan: Analisis, Desain, Implementasi, dan Pengujian. Tutorial ini mencakup:

- **Fitur 1 — Pencarian Anggota:** Mencari data anggota berdasarkan kode menggunakan AJAX (tanpa refresh halaman)
- **Fitur 2 — Detail Informasi Anggota:** Menampilkan data lengkap anggota setelah ditemukan
- **Fitur 3 — Tambah Peminjaman Buku:** Form modal untuk mencatat peminjaman, lengkap dengan kalkulasi tanggal kembali otomatis dan validasi ketersediaan buku
- **Fitur 4 — Tombol Pengembalian Buku:** Mencatat pengembalian buku dengan konfirmasi sebelum proses
- **Fitur 5 — Denda Keterlambatan:** Petugas menginput nominal denda saat pengembalian; sistem menampilkan info keterlambatan secara otomatis

Cocok untuk mahasiswa yang ingin memahami cara membangun fitur web secara terstruktur dan profesional, termasuk pembuatan migration, model (JOIN, generate kode unik), controller (AJAX handler), dan view dengan JavaScript dinamis.

---

## Troubleshooting

**Error: `Can't find a route for 'GET: ...'`**
Pastikan route sudah ditambahkan di `app/Config/Routes.php` dan file sudah disimpan.

**Error: `Class "App\Controllers\..." not found`**
Nama class di dalam file harus sama persis dengan nama file-nya (huruf besar/kecil harus cocok).

**Tabel tidak ditemukan di database**
Jalankan `php spark migrate` untuk membuat tabel. Pastikan konfigurasi database di `.env` sudah benar.

**Halaman tampil kosong / error 500**
Ubah `CI_ENVIRONMENT = development` di `.env` untuk melihat pesan error secara detail.

**Port database tidak bisa connect**
Sesuaikan nilai `database.default.port` di `.env` — XAMPP umumnya `3306`, Laragon umumnya `3307`.

---

## Lisensi

Project ini menggunakan lisensi **MIT**.
