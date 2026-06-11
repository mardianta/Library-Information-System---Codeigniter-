# Tutorial: Menambah Menu Laporan dan Cetak PDF Data Buku

> **Untuk siapa tutorial ini?**
> Tutorial ini dibuat untuk mahasiswa yang belum terbiasa pemrograman. Setiap langkah dijelaskan detail — mulai dari membuka file, apa yang diubah, mengapa harus diubah, hingga cara uji coba. Ikuti urutannya dari atas ke bawah.

---

## Apa yang Akan Kita Buat?

1. **Menu "Laporan"** di sidebar kiri — berupa menu yang bisa diklik dan memiliki sub-menu di dalamnya
2. **Sub-menu "Laporan Data Buku"** — halaman yang menampilkan seluruh data buku
3. **Tombol "Cetak PDF"** — membuka halaman khusus yang siap dicetak/disimpan sebagai PDF

---

## Gambaran Hasil Akhir

**Tampilan Sidebar setelah selesai:**
```
Dashboard
Master Buku
Master Member
▼ Laporan              ← menu baru (bisa diklik untuk buka/tutup)
   └─ Laporan Data Buku  ← sub-menu baru
```

**Alur Cetak PDF:**
```
Klik "Laporan Data Buku"
       ↓
Muncul halaman tabel data buku + tombol [Cetak PDF]
       ↓
Klik [Cetak PDF] → terbuka tab baru
       ↓
Tab baru: halaman bersih tanpa sidebar, hanya tabel buku
       ↓
Dialog Print browser muncul otomatis → pilih "Save as PDF" atau printer
```

---

## Gambaran File yang Akan Dibuat / Diedit

```
app/
├── Controllers/
│   └── LaporanController.php         ← BARU
├── Views/
│   └── laporan/
│       ├── buku.php                  ← BARU (halaman laporan utama)
│       └── cetak_buku.php            ← BARU (halaman khusus cetak/PDF)
├── Config/
│   └── Routes.php                    ← EDIT (tambah route laporan)
└── Views/layouts/partials/
    └── sidebar.php                   ← EDIT (tambah menu Laporan)
```

---

## LANGKAH 1 — Tambahkan Menu "Laporan" di Sidebar

### Apa itu Sidebar?

Sidebar adalah panel menu di sebelah kiri layar. Kita perlu menambahkan menu "Laporan" beserta sub-menu-nya di sana.

### Buka File Sidebar

Buka file:
```
app/Views/layouts/partials/sidebar.php
```

### Isi File Saat Ini

```php
<aside class="main-sidebar sidebar-dark-primary elevation-4">

    <a href="<?= base_url('/') ?>" class="brand-link">
        <span class="brand-text font-weight-light ml-3">Library Information System</span>
    </a>

    <div class="sidebar">
        <nav class="mt-2">
            <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview">

                <li class="nav-item">
                    <a href="<?= base_url('/dashboard') ?>" class="nav-link">
                        <i class="nav-icon fas fa-tachometer-alt"></i>
                        <p>Dashboard</p>
                    </a>
                </li>

                <li class="nav-item">
                    <a href="<?= base_url('/list/books') ?>" class="nav-link">
                        <i class="nav-icon fas fa-book"></i>
                        <p>Master Buku</p>
                    </a>
                </li>

                <li class="nav-item">
                    <a href="<?= base_url('/list/users') ?>" class="nav-link">
                        <i class="nav-icon fas fa-users"></i>
                        <p>Master Member</p>
                    </a>
                </li>

            </ul>
        </nav>
    </div>
</aside>
```

### Tambahkan Menu Laporan di Bagian Bawah

Cari baris yang berisi `</ul>` paling akhir (sebelum `</nav>`), dan **tambahkan kode berikut SEBELUM baris `</ul>` tersebut**:

```php
                <li class="nav-item has-treeview">
                    <a href="#" class="nav-link">
                        <i class="nav-icon fas fa-chart-bar"></i>
                        <p>
                            Laporan
                            <i class="right fas fa-angle-left"></i>
                        </p>
                    </a>
                    <ul class="nav nav-treeview">
                        <li class="nav-item">
                            <a href="<?= base_url('/laporan/buku') ?>" class="nav-link">
                                <i class="far fa-circle nav-icon"></i>
                                <p>Laporan Data Buku</p>
                            </a>
                        </li>
                </li>
```

### Hasil Akhir File `sidebar.php`:

```php
<aside class="main-sidebar sidebar-dark-primary elevation-4">

    <a href="<?= base_url('/') ?>" class="brand-link">
        <span class="brand-text font-weight-light ml-3">Library Information System</span>
    </a>

    <div class="sidebar">
        <nav class="mt-2">
            <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview">

                <li class="nav-item">
                    <a href="<?= base_url('/dashboard') ?>" class="nav-link">
                        <i class="nav-icon fas fa-tachometer-alt"></i>
                        <p>Dashboard</p>
                    </a>
                </li>

                <li class="nav-item">
                    <a href="<?= base_url('/list/books') ?>" class="nav-link">
                        <i class="nav-icon fas fa-book"></i>
                        <p>Master Buku</p>
                    </a>
                </li>

                <li class="nav-item">
                    <a href="<?= base_url('/list/users') ?>" class="nav-link">
                        <i class="nav-icon fas fa-users"></i>
                        <p>Master Member</p>
                    </a>
                </li>

                <li class="nav-item has-treeview">
                    <a href="#" class="nav-link">
                        <i class="nav-icon fas fa-chart-bar"></i>
                        <p>
                            Laporan
                            <i class="right fas fa-angle-left"></i>
                        </p>
                    </a>
                    <ul class="nav nav-treeview">
                        <li class="nav-item">
                            <a href="<?= base_url('/laporan/buku') ?>" class="nav-link">
                                <i class="far fa-circle nav-icon"></i>
                                <p>Laporan Data Buku</p>
                            </a>
                        </li>
                    </ul>
                </li>

            </ul>
        </nav>
    </div>
</aside>
```

### Penjelasan Kode Menu

| Bagian Kode | Artinya |
|---|---|
| `has-treeview` | Menandai menu ini punya sub-menu yang bisa dibuka/tutup |
| `href="#"` | Menu induk tidak menuju ke halaman manapun, hanya toggle buka/tutup |
| `fas fa-chart-bar` | Ikon grafik batang (dari Font Awesome) untuk menu Laporan |
| `right fas fa-angle-left` | Ikon panah kecil yang berputar saat menu dibuka/tutup |
| `nav-treeview` | `<ul>` daftar sub-menu yang tersembunyi/muncul |
| `far fa-circle nav-icon` | Ikon bulat kecil untuk sub-menu |
| `href="...laporan/buku"` | URL yang dituju saat sub-menu diklik |

**Simpan file** (Ctrl+S / Cmd+S).

---

## LANGKAH 2 — Buat Controller

### Apa itu Controller di Fitur Ini?

Controller `LaporanController` bertugas:
1. Mengambil data buku dari database
2. Mengirim data tersebut ke halaman View untuk ditampilkan

### Buat File Baru

Navigasi ke folder `app/Controllers/`.

Buat file baru bernama `LaporanController.php`.

> **Cara buat di VS Code:** Klik kanan folder `Controllers` → **New File** → ketik `LaporanController.php` → Enter

### Isi File `LaporanController.php`

Tulis kode berikut dari awal (jangan ada baris lain sebelumnya):

```php
<?php

namespace App\Controllers;
use App\Models\BookModel;

class LaporanController extends BaseController
{
    public function buku()
    {
        $bookModel = new BookModel();
        $data['books'] = $bookModel->findAll();
        $data['title'] = 'Laporan Data Buku';

        return view('laporan/buku', $data);
    }

    public function cetakBuku()
    {
        $bookModel = new BookModel();
        $data['books'] = $bookModel->findAll();
        $data['title'] = 'Laporan Data Buku';

        return view('laporan/cetak_buku', $data);
    }
}
```

### Penjelasan Dua Fungsi

| Fungsi | Tugasnya | View yang Dipanggil |
|---|---|---|
| `buku()` | Menampilkan halaman laporan utama dengan tombol Cetak | `laporan/buku.php` |
| `cetakBuku()` | Menampilkan halaman bersih khusus untuk cetak/PDF | `laporan/cetak_buku.php` |

Mengapa ada dua fungsi? Karena:
- Halaman laporan utama (`buku`) memiliki sidebar, navbar, tombol, dll
- Halaman cetak (`cetakBuku`) harus **bersih** — hanya tabel, tanpa sidebar/navbar, agar hasilnya PDF yang rapi

**Simpan file.**

---

## LANGKAH 3 — Tambahkan Routes

### Buka File Routes

Buka file:
```
app/Config/Routes.php
```

### Tambahkan di Bagian Paling Bawah

```php
// Laporan Routes
$routes->get('/laporan/buku', 'LaporanController::buku');
$routes->get('/laporan/cetak-buku', 'LaporanController::cetakBuku');
```

### Penjelasan

| Route | Artinya |
|---|---|
| `GET /laporan/buku` | Saat URL ini dibuka, jalankan fungsi `buku()` di LaporanController |
| `GET /laporan/cetak-buku` | Saat URL ini dibuka (dari tombol Cetak PDF), jalankan `cetakBuku()` |

**Simpan file.**

---

## LANGKAH 4 — Buat Folder dan File View

### Buat Folder `laporan`

Navigasi ke folder `app/Views/`.

Buat folder baru bernama `laporan`:
- Klik kanan folder `Views/` → **New Folder** → ketik `laporan` → Enter

---

### Buat File `buku.php` — Halaman Laporan Utama

Di dalam folder `app/Views/laporan/`, buat file baru bernama `buku.php`.

**Isi dengan kode berikut:**

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
<div class="card">
    <div class="card-header">
        <h3 class="card-title">Data Buku</h3>
        <div class="card-tools">
            <a href="<?= base_url('/laporan/cetak-buku') ?>" target="_blank" class="btn btn-danger btn-sm">
                <i class="fas fa-file-pdf"></i> Cetak PDF
            </a>
        </div>
    </div>

    <div class="card-body table-responsive">
        <table id="tabelLaporanBuku" class="table table-bordered table-striped">
            <thead>
                <tr>
                    <th width="50">No.</th>
                    <th>Judul Buku</th>
                    <th>Kode Buku</th>
                    <th>ISBN</th>
                    <th>Penulis</th>
                    <th>Penerbit</th>
                    <th>Tahun Terbit</th>
                    <th>Keterangan</th>
                </tr>
            </thead>
            <tbody>
                <?php $no = 0; ?>
                <?php foreach($books as $book): ?>
                    <?php $no++; ?>
                    <tr>
                        <td><?= $no ?></td>
                        <td><?= $book['title_book'] ?></td>
                        <td><?= $book['code_book'] ?></td>
                        <td><?= $book['isbn_book'] ?></td>
                        <td><?= $book['author_book'] ?></td>
                        <td><?= $book['publisher_book'] ?></td>
                        <td><?= $book['published_year'] ?></td>
                        <td><?= $book['description_book'] ?></td>
                    </tr>
                <?php endforeach; ?>
            </tbody>
        </table>
    </div>
</div>
<?= $this->endSection() ?>

<?= $this->section('js') ?>
<link rel="stylesheet" href="<?= base_url('adminlte/plugins/datatables-bs4/css/dataTables.bootstrap4.min.css') ?>">

<script src="<?= base_url('adminlte/plugins/datatables/jquery.dataTables.min.js') ?>"></script>
<script src="<?= base_url('adminlte/plugins/datatables-bs4/js/dataTables.bootstrap4.min.js') ?>"></script>

<script>
    $(document).ready(function () {
        $('#tabelLaporanBuku').DataTable({
            "language": {
                "search": "Cari:",
                "lengthMenu": "Tampilkan _MENU_ data",
                "info": "Menampilkan _START_ sampai _END_ dari _TOTAL_ data",
                "infoEmpty": "Tidak ada data",
                "zeroRecords": "Data tidak ditemukan",
                "paginate": {
                    "first": "Pertama",
                    "last": "Terakhir",
                    "next": "Selanjutnya",
                    "previous": "Sebelumnya"
                }
            }
        });
    });
</script>
<?= $this->endSection() ?>
```

### Penjelasan Bagian Penting

| Bagian Kode | Artinya |
|---|---|
| `target="_blank"` | Tombol Cetak PDF akan membuka tab baru di browser |
| `id="tabelLaporanBuku"` | ID unik untuk tabel ini — dipakai oleh JavaScript DataTables di bawah |
| `DataTable({...})` | Mengaktifkan fitur DataTables: pencarian otomatis, paginasi, sorting per kolom |
| `"language": {...}` | Mengubah teks DataTables dari bahasa Inggris ke bahasa Indonesia |
| Kolom `Aksi` **tidak ada** | Di halaman laporan tidak ada tombol Edit/Hapus — ini hanya untuk melihat data |

**Simpan file.**

---

### Buat File `cetak_buku.php` — Halaman Khusus Cetak PDF

Di dalam folder `app/Views/laporan/`, buat file baru bernama `cetak_buku.php`.

> Halaman ini **tidak menggunakan layout/template utama** karena kita ingin tampilan yang bersih tanpa sidebar dan navbar — sehingga saat dicetak/disimpan sebagai PDF hasilnya rapi.

**Isi dengan kode berikut:**

```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?= $title ?></title>
    <style>
        /* Pengaturan tampilan halaman */
        body {
            font-family: Arial, sans-serif;
            font-size: 12px;
            margin: 20px;
            color: #000;
        }

        /* Header laporan */
        .header-laporan {
            text-align: center;
            margin-bottom: 20px;
            border-bottom: 2px solid #000;
            padding-bottom: 10px;
        }

        .header-laporan h2 {
            margin: 0;
            font-size: 18px;
            text-transform: uppercase;
        }

        .header-laporan p {
            margin: 4px 0 0 0;
            font-size: 12px;
            color: #555;
        }

        /* Tabel data */
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }

        table th {
            background-color: #343a40;
            color: #fff;
            padding: 8px;
            text-align: left;
            border: 1px solid #999;
            font-size: 11px;
        }

        table td {
            padding: 6px 8px;
            border: 1px solid #ccc;
            vertical-align: top;
            font-size: 11px;
        }

        table tr:nth-child(even) {
            background-color: #f2f2f2;
        }

        /* Tombol cetak — hanya tampil di layar, tidak ikut tercetak */
        .tombol-cetak {
            text-align: right;
            margin-bottom: 15px;
        }

        .tombol-cetak button {
            background-color: #dc3545;
            color: white;
            border: none;
            padding: 8px 20px;
            font-size: 13px;
            border-radius: 4px;
            cursor: pointer;
        }

        .tombol-cetak button:hover {
            background-color: #c82333;
        }

        /* Saat dicetak: sembunyikan tombol, pastikan semua terlihat */
        @media print {
            .tombol-cetak {
                display: none;
            }

            body {
                margin: 10px;
            }

            table th {
                background-color: #343a40 !important;
                color: #fff !important;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }
        }
    </style>
</head>
<body>

    <!-- Tombol Cetak (hanya muncul di layar, tidak ikut tercetak) -->
    <div class="tombol-cetak">
        <button onclick="window.print()">
            &#128438; Cetak / Simpan sebagai PDF
        </button>
    </div>

    <!-- Header Laporan -->
    <div class="header-laporan">
        <h2>Laporan Data Buku</h2>
        <p>Library Information System</p>
        <p>Dicetak pada: <?= date('d F Y, H:i') ?> WIB</p>
    </div>

    <!-- Tabel Data Buku -->
    <table>
        <thead>
            <tr>
                <th width="40">No.</th>
                <th>Judul Buku</th>
                <th>Kode</th>
                <th>ISBN</th>
                <th>Penulis</th>
                <th>Penerbit</th>
                <th>Tahun</th>
                <th>Keterangan</th>
            </tr>
        </thead>
        <tbody>
            <?php $no = 0; ?>
            <?php foreach($books as $book): ?>
                <?php $no++; ?>
                <tr>
                    <td><?= $no ?></td>
                    <td><?= $book['title_book'] ?></td>
                    <td><?= $book['code_book'] ?></td>
                    <td><?= $book['isbn_book'] ?></td>
                    <td><?= $book['author_book'] ?></td>
                    <td><?= $book['publisher_book'] ?></td>
                    <td><?= $book['published_year'] ?></td>
                    <td><?= $book['description_book'] ?></td>
                </tr>
            <?php endforeach; ?>
        </tbody>
    </table>

    <script>
        // Dialog cetak muncul otomatis saat halaman dibuka
        window.onload = function () {
            window.print();
        };
    </script>

</body>
</html>
```

### Penjelasan Bagian Penting

**Mengapa tidak pakai `$this->extend('layouts/template')`?**

Karena layout template utama menyertakan sidebar, navbar, header, dll. Untuk cetak PDF, kita butuh halaman yang bersih — hanya konten laporan saja. Jadi kita tulis HTML sendiri dari awal.

| Bagian Kode | Artinya |
|---|---|
| `font-family: Arial` | Font yang aman untuk semua printer |
| `.tombol-cetak` | Div pembungkus tombol cetak |
| `@media print { .tombol-cetak { display: none; } }` | Saat dicetak, tombol "Cetak" disembunyikan otomatis agar tidak ikut muncul di PDF |
| `-webkit-print-color-adjust: exact` | Memaksa browser mencetak warna latar belakang (header tabel hitam) |
| `window.print()` di `onload` | Saat halaman terbuka, **dialog cetak browser langsung muncul otomatis** |
| `date('d F Y, H:i')` | Menampilkan tanggal dan jam saat laporan dicetak |

**Cara menyimpan sebagai PDF saat dialog cetak muncul:**
1. Dialog cetak browser muncul
2. Di kolom **Destination / Printer**, pilih **"Save as PDF"**
3. Klik **Save**
4. PDF tersimpan di komputer

**Simpan file.**

---

## LANGKAH 5 — Uji Coba

### 5a. Pastikan Server Berjalan

Pastikan XAMPP/Laragon aktif (Apache dan MySQL menyala).

### 5b. Buka Halaman Laporan

Buka browser, ketik:

```
http://localhost:8081/laporan/buku
```

**Hasil yang diharapkan:**
- Menu "Laporan" muncul di sidebar kiri
- Halaman menampilkan tabel data buku dengan fitur pencarian dan paginasi (DataTables)
- Tombol merah **"Cetak PDF"** muncul di pojok kanan atas card

### 5c. Klik Menu di Sidebar

Coba klik menu **"Laporan"** di sidebar:
- Sub-menu **"Laporan Data Buku"** akan muncul/tertutup (accordion)
- Klik **"Laporan Data Buku"** untuk masuk ke halaman

### 5d. Uji Tombol Cetak PDF

1. Klik tombol **"Cetak PDF"** (warna merah)
2. Tab baru terbuka di browser
3. Dialog cetak muncul otomatis
4. Untuk simpan PDF: pilih **"Save as PDF"** di kolom Destination, klik **Save**
5. Untuk cetak langsung: pilih printer, klik **Print**

---

## Ringkasan: File Apa yang Kita Buat/Edit

| File | Aksi | Yang Diubah/Ditambahkan |
|---|---|---|
| `sidebar.php` | **Edit** | Tambah blok menu Laporan dengan sub-menu Laporan Data Buku |
| `LaporanController.php` | **Buat baru** | Dua fungsi: `buku()` dan `cetakBuku()` |
| `Routes.php` | **Edit** | Tambah 2 route: `/laporan/buku` dan `/laporan/cetak-buku` |
| `laporan/buku.php` | **Buat baru** | Halaman laporan utama: tabel + DataTables + tombol Cetak PDF |
| `laporan/cetak_buku.php` | **Buat baru** | Halaman cetak bersih: HTML murni tanpa sidebar, otomatis buka dialog print |

---

## Checklist Sebelum Demo

- [ ] Menu "Laporan" sudah muncul di sidebar
- [ ] Klik menu "Laporan" menampilkan sub-menu "Laporan Data Buku"
- [ ] `LaporanController.php` sudah ada di `app/Controllers/`
- [ ] Folder `app/Views/laporan/` sudah ada
- [ ] File `buku.php` sudah ada di `app/Views/laporan/`
- [ ] File `cetak_buku.php` sudah ada di `app/Views/laporan/`
- [ ] Routes laporan sudah ditambahkan di `Routes.php`
- [ ] `http://localhost:8081/laporan/buku` bisa dibuka dan menampilkan data
- [ ] Fitur pencarian dan paginasi DataTables berfungsi
- [ ] Tombol "Cetak PDF" membuka tab baru
- [ ] Dialog cetak muncul otomatis di tab baru
- [ ] Tombol "Cetak" tidak ikut tercetak di PDF

---

## Masalah yang Sering Terjadi

| Pesan Error / Gejala | Kemungkinan Penyebab | Solusi |
|---|---|---|
| `Can't find a route for 'GET: laporan/buku'` | Routes belum ditambahkan | Tambahkan route di `Routes.php` dan simpan |
| `Class "App\Controllers\LaporanController" not found` | Nama class salah atau file belum ada | Pastikan nama class `LaporanController` dan nama file `LaporanController.php` |
| Menu Laporan tidak muncul di sidebar | File `sidebar.php` belum disimpan atau kode salah | Buka kembali sidebar.php, cek kode, simpan ulang |
| Tabel tidak ada fitur pencarian/paginasi | JavaScript DataTables tidak dimuat | Pastikan blok `<?= $this->section('js') ?>` ada dan isinya lengkap |
| Dialog cetak tidak muncul otomatis | Browser memblokir popup | Klik ikon di address bar browser → izinkan popup untuk localhost |
| Warna header tabel hilang saat cetak PDF | Browser memblokir cetak warna latar | Di dialog cetak, centang opsi **"Background graphics"** / **"Print backgrounds"** |
| Tabel data kosong | Data buku belum ada di database | Tambahkan dulu beberapa data buku lewat menu Master Buku |
