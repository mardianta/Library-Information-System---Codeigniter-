# Tutorial: Cetak Label / Detail Buku untuk Ditempel ke Buku

> **Untuk siapa tutorial ini?**
> Tutorial ini adalah **lanjutan** dari tutorial Laporan Data Buku. Pastikan fitur Laporan sudah berjalan sebelum mengikuti tutorial ini. Dibuat untuk mahasiswa non-IT — setiap langkah dijelaskan secara detail.

---

## Apa yang Akan Kita Buat?

**Label buku** adalah stiker kecil yang ditempel di punggung atau sampul buku, berisi informasi identitas buku. Fitur ini akan membuat dua jenis cetak:

1. **Cetak Semua Label** — mencetak label untuk semua buku sekaligus dalam format grid (banyak label per halaman)
2. **Cetak Label Satu Buku** — mencetak label untuk satu buku saja (misalnya saat buku baru datang)

**Contoh tampilan label buku yang akan dicetak:**

```
┌──────────────────────────────┐
│  📚 PERPUSTAKAAN             │
│  Library Information System  │
├──────────────────────────────┤
│  Kode   : BK-001             │
│  ───────────────────────     │
│  Judul  : Clean Code         │
│  Penulis: Robert C. Martin   │
│  Penerbit: Prentice Hall     │
│  Tahun  : 2008               │
└──────────────────────────────┘
```

Setiap halaman A4 memuat **2 kolom × 5 baris = 10 label** per halaman.

---

## Gambaran File yang Akan Dibuat / Diedit

```
app/
├── Controllers/
│   └── LaporanController.php        ← EDIT (tambah 3 fungsi baru)
├── Views/
│   └── laporan/
│       ├── label_buku.php           ← BARU (halaman daftar + tombol cetak)
│       ├── cetak_label_buku.php     ← BARU (cetak semua label, clean print)
│       └── cetak_label_satu.php     ← BARU (cetak label 1 buku, clean print)
├── Config/
│   └── Routes.php                   ← EDIT (tambah 3 route baru)
└── Views/layouts/partials/
    └── sidebar.php                  ← EDIT (tambah sub-menu Cetak Label Buku)
```

---

## Alur Fitur

```
Klik "Cetak Label Buku" di sidebar
         ↓
Halaman daftar buku + dua tombol:
  [Cetak Semua Label]   ← tombol atas
  [Cetak Label] (per baris)  ← tombol di tiap buku
         ↓                              ↓
Tab baru: semua label         Tab baru: label 1 buku
dalam format grid              besar di tengah halaman
         ↓                              ↓
Dialog print muncul           Dialog print muncul
→ Save as PDF / Print         → Save as PDF / Print
```

---

## LANGKAH 1 — Tambah Sub-Menu di Sidebar

### Buka File Sidebar

Buka file:
```
app/Views/layouts/partials/sidebar.php
```

### Cari Bagian Menu Laporan

Di dalam file, cari blok ini:

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
                    </ul>
                </li>
```

### Tambahkan Sub-Menu Baru

Tambahkan satu `<li>` baru di dalam `<ul class="nav nav-treeview">` — **setelah** baris Laporan Data Buku:

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
                        <li class="nav-item">
                            <a href="<?= base_url('/laporan/label-buku') ?>" class="nav-link">
                                <i class="far fa-circle nav-icon"></i>
                                <p>Cetak Label Buku</p>
                            </a>
                        </li>
                    </ul>
                </li>
```

**Yang ditambahkan** hanyalah blok `<li>` baru ini (letakkan setelah sub-menu Laporan Data Buku):

```php
                        <li class="nav-item">
                            <a href="<?= base_url('/laporan/label-buku') ?>" class="nav-link">
                                <i class="far fa-circle nav-icon"></i>
                                <p>Cetak Label Buku</p>
                            </a>
                        </li>
```

**Simpan file.**

---

## LANGKAH 2 — Tambah Fungsi di Controller

### Buka File Controller

Buka file:
```
app/Controllers/LaporanController.php
```

### Isi File Saat Ini

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

### Ganti Seluruh Isinya dengan Kode Berikut

Kita menambahkan **3 fungsi baru** di bawah fungsi yang sudah ada:

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

    public function labelBuku()
    {
        $bookModel = new BookModel();
        $data['books'] = $bookModel->findAll();
        $data['title'] = 'Cetak Label Buku';

        return view('laporan/label_buku', $data);
    }

    public function cetakLabelBuku()
    {
        $bookModel = new BookModel();
        $data['books'] = $bookModel->findAll();
        $data['title'] = 'Cetak Label Buku';

        return view('laporan/cetak_label_buku', $data);
    }

    public function cetakLabelSatu($id)
    {
        $bookModel = new BookModel();
        $data['book'] = $bookModel->find($id);
        $data['title'] = 'Cetak Label Buku';

        return view('laporan/cetak_label_satu', $data);
    }
}
```

### Penjelasan 3 Fungsi Baru

| Fungsi | Tugasnya | View yang Dipanggil |
|---|---|---|
| `labelBuku()` | Menampilkan daftar buku dengan tombol-tombol cetak | `laporan/label_buku.php` |
| `cetakLabelBuku()` | Menampilkan halaman cetak bersih untuk **semua** label sekaligus | `laporan/cetak_label_buku.php` |
| `cetakLabelSatu($id)` | Menampilkan halaman cetak bersih untuk **satu** buku berdasarkan ID | `laporan/cetak_label_satu.php` |

**Simpan file.**

---

## LANGKAH 3 — Tambah Routes

### Buka File Routes

Buka file:
```
app/Config/Routes.php
```

### Tambahkan di Bagian Bawah (setelah route laporan yang sudah ada)

Cari baris paling bawah yang sudah ada:
```php
$routes->get('/laporan/cetak-buku', 'LaporanController::cetakBuku');
```

Tambahkan **3 baris baru** tepat di bawahnya:

```php
// Label Buku Routes
$routes->get('/laporan/label-buku', 'LaporanController::labelBuku');
$routes->get('/laporan/cetak-label-buku', 'LaporanController::cetakLabelBuku');
$routes->get('/laporan/cetak-label-buku/(:num)', 'LaporanController::cetakLabelSatu/$1');
```

### Penjelasan

| Route | Artinya |
|---|---|
| `GET /laporan/label-buku` | Halaman utama daftar buku + tombol cetak label |
| `GET /laporan/cetak-label-buku` | Halaman cetak bersih — semua label sekaligus |
| `GET /laporan/cetak-label-buku/5` | Halaman cetak bersih — label buku dengan ID = 5 |

> **`(:num)`** artinya bagian URL itu harus berupa angka. Angka tersebut dikirim sebagai parameter `$id` ke fungsi `cetakLabelSatu($id)`.

**Simpan file.**

---

## LANGKAH 4 — Buat File View

### Buka Folder View

Folder `app/Views/laporan/` sudah ada dari tutorial sebelumnya. Kita tinggal buat 3 file baru di dalamnya.

---

### 4a. Buat File `label_buku.php` — Halaman Daftar + Tombol Cetak

Buat file baru bernama `label_buku.php` di dalam `app/Views/laporan/`.

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
        <h3 class="card-title">Daftar Buku — Pilih untuk Cetak Label</h3>
        <div class="card-tools">
            <a href="<?= base_url('/laporan/cetak-label-buku') ?>" target="_blank" class="btn btn-danger btn-sm">
                <i class="fas fa-print"></i> Cetak Semua Label
            </a>
        </div>
    </div>

    <div class="card-body table-responsive">
        <table id="tabelLabelBuku" class="table table-bordered table-striped">
            <thead>
                <tr>
                    <th width="50">No.</th>
                    <th>Kode Buku</th>
                    <th>Judul Buku</th>
                    <th>Penulis</th>
                    <th>Penerbit</th>
                    <th>Tahun</th>
                    <th width="130">Aksi</th>
                </tr>
            </thead>
            <tbody>
                <?php $no = 0; ?>
                <?php foreach($books as $book): ?>
                    <?php $no++; ?>
                    <tr>
                        <td><?= $no ?></td>
                        <td><?= $book['code_book'] ?></td>
                        <td><?= $book['title_book'] ?></td>
                        <td><?= $book['author_book'] ?></td>
                        <td><?= $book['publisher_book'] ?></td>
                        <td><?= $book['published_year'] ?></td>
                        <td>
                            <a href="<?= base_url('/laporan/cetak-label-buku/' . $book['id_book']) ?>"
                               target="_blank"
                               class="btn btn-warning btn-sm">
                                <i class="fas fa-tag"></i> Cetak Label
                            </a>
                        </td>
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
        $('#tabelLabelBuku').DataTable({
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
| Tombol **"Cetak Semua Label"** (merah) | Membuka tab baru dengan semua label sekaligus |
| Tombol **"Cetak Label"** per baris (kuning) | Membuka tab baru hanya untuk label buku di baris itu |
| `target="_blank"` | Link dibuka di tab baru, sehingga halaman daftar tidak tertutup |
| `base_url('/laporan/cetak-label-buku/' . $book['id_book'])` | URL berisi ID buku, contoh: `/laporan/cetak-label-buku/3` |

**Simpan file.**

---

### 4b. Buat File `cetak_label_buku.php` — Cetak Semua Label (Grid)

Buat file baru bernama `cetak_label_buku.php` di dalam `app/Views/laporan/`.

> Halaman ini **tidak menggunakan layout template** karena harus bersih tanpa sidebar dan navbar untuk hasil cetak yang rapi.

**Isi dengan kode berikut:**

```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cetak Label Buku</title>
    <style>
        /* Reset dan pengaturan dasar */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            font-size: 11px;
            background: #f0f0f0;
            padding: 15px;
        }

        /* Tombol cetak — hanya tampil di layar */
        .tombol-cetak {
            text-align: center;
            margin-bottom: 15px;
        }

        .tombol-cetak button {
            background-color: #dc3545;
            color: white;
            border: none;
            padding: 10px 30px;
            font-size: 14px;
            border-radius: 4px;
            cursor: pointer;
            margin: 0 5px;
        }

        .tombol-cetak button.btn-kembali {
            background-color: #6c757d;
        }

        .tombol-cetak button:hover {
            opacity: 0.85;
        }

        /* Judul halaman */
        .judul-halaman {
            text-align: center;
            margin-bottom: 15px;
            font-size: 14px;
            font-weight: bold;
        }

        /* Kontainer semua label — layout grid */
        .grid-label {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            max-width: 19cm;
            margin: 0 auto;
        }

        /* Satu label buku */
        .label-buku {
            background: white;
            border: 2px solid #333;
            border-radius: 4px;
            padding: 10px 12px;
            min-height: 5cm;
            page-break-inside: avoid;
        }

        /* Header label — nama perpustakaan */
        .label-header {
            background-color: #1a3a5c;
            color: white;
            text-align: center;
            padding: 5px 4px;
            margin: -10px -12px 8px -12px;
            border-radius: 2px 2px 0 0;
            font-size: 10px;
            font-weight: bold;
            letter-spacing: 0.5px;
        }

        /* Kode buku — ditampilkan besar dan mencolok */
        .label-kode {
            font-size: 20px;
            font-weight: bold;
            text-align: center;
            border: 2px solid #333;
            padding: 4px;
            margin-bottom: 8px;
            letter-spacing: 2px;
            background-color: #fffde7;
        }

        /* Garis pemisah */
        .label-divider {
            border: none;
            border-top: 1px solid #999;
            margin: 6px 0;
        }

        /* Baris informasi buku */
        .label-row {
            display: flex;
            margin-bottom: 3px;
            line-height: 1.4;
        }

        .label-key {
            font-weight: bold;
            min-width: 60px;
            color: #333;
            flex-shrink: 0;
        }

        .label-sep {
            margin: 0 4px;
            flex-shrink: 0;
        }

        .label-val {
            color: #111;
            word-break: break-word;
        }

        /* Saat dicetak */
        @media print {
            body {
                background: white;
                padding: 5mm;
            }

            .tombol-cetak {
                display: none;
            }

            .judul-halaman {
                display: none;
            }

            .grid-label {
                gap: 6mm;
                max-width: 100%;
            }

            .label-buku {
                border: 1.5pt solid #000;
                break-inside: avoid;
            }

            .label-header {
                background-color: #1a3a5c !important;
                color: white !important;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .label-kode {
                background-color: #fffde7 !important;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }
        }
    </style>
</head>
<body>

    <!-- Tombol aksi (tidak ikut tercetak) -->
    <div class="tombol-cetak">
        <button onclick="window.print()">&#128438; Cetak / Simpan sebagai PDF</button>
        <button class="btn-kembali" onclick="window.close()">&#10006; Tutup</button>
    </div>

    <div class="judul-halaman">
        Label Buku &mdash; Dicetak: <?= date('d F Y, H:i') ?> WIB
        &mdash; Total: <?= count($books) ?> buku
    </div>

    <!-- Grid semua label -->
    <div class="grid-label">
        <?php foreach($books as $book): ?>
        <div class="label-buku">

            <div class="label-header">
                &#128218; PERPUSTAKAAN &mdash; LIBRARY INFORMATION SYSTEM
            </div>

            <div class="label-kode">
                <?= strtoupper($book['code_book']) ?>
            </div>

            <hr class="label-divider">

            <div class="label-row">
                <span class="label-key">Judul</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['title_book'] ?></span>
            </div>
            <div class="label-row">
                <span class="label-key">Penulis</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['author_book'] ?></span>
            </div>
            <div class="label-row">
                <span class="label-key">Penerbit</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['publisher_book'] ?></span>
            </div>
            <div class="label-row">
                <span class="label-key">Tahun</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['published_year'] ?></span>
            </div>
            <?php if(!empty($book['isbn_book'])): ?>
            <div class="label-row">
                <span class="label-key">ISBN</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['isbn_book'] ?></span>
            </div>
            <?php endif; ?>

        </div>
        <?php endforeach; ?>
    </div>

    <script>
        window.onload = function () {
            window.print();
        };
    </script>

</body>
</html>
```

### Penjelasan Bagian Penting

| Bagian Kode | Artinya |
|---|---|
| `display: grid; grid-template-columns: repeat(2, 1fr)` | Layout 2 kolom otomatis, lebar sama |
| `page-break-inside: avoid` | Label tidak akan terpotong di tengah saat pindah halaman |
| `.label-header` dengan warna biru gelap | Bagian atas label berisi nama perpustakaan |
| `.label-kode` dengan font besar | Kode buku ditampilkan mencolok — mudah dibaca dari jarak jauh |
| `strtoupper($book['code_book'])` | Kode buku selalu ditampilkan huruf kapital |
| `if(!empty($book['isbn_book']))` | ISBN hanya ditampilkan jika ada isinya |
| `count($books)` | Menampilkan jumlah total buku yang dicetak |
| `window.close()` | Tombol "Tutup" menutup tab ini |
| `@media print { .tombol-cetak { display: none } }` | Tombol tidak ikut tercetak |
| `-webkit-print-color-adjust: exact` | Paksa browser cetak warna latar belakang |

**Simpan file.**

---

### 4c. Buat File `cetak_label_satu.php` — Cetak Label 1 Buku

Buat file baru bernama `cetak_label_satu.php` di dalam `app/Views/laporan/`.

**Isi dengan kode berikut:**

```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cetak Label — <?= $book['title_book'] ?></title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            font-size: 12px;
            background: #f0f0f0;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        /* Tombol aksi */
        .tombol-cetak {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .tombol-cetak button {
            padding: 10px 25px;
            font-size: 13px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            color: white;
        }

        .btn-print  { background-color: #dc3545; }
        .btn-close  { background-color: #6c757d; }
        .btn-print:hover { background-color: #c82333; }
        .btn-close:hover { background-color: #5a6268; }

        /* Keterangan kecil */
        .info-cetak {
            font-size: 11px;
            color: #666;
            margin-bottom: 20px;
            text-align: center;
        }

        /* Label tunggal — lebih besar */
        .label-buku {
            background: white;
            border: 2px solid #333;
            border-radius: 6px;
            width: 10cm;
            min-height: 7cm;
            padding: 0;
            box-shadow: 0 2px 8px rgba(0,0,0,0.15);
        }

        .label-header {
            background-color: #1a3a5c;
            color: white;
            text-align: center;
            padding: 8px;
            border-radius: 4px 4px 0 0;
            font-size: 11px;
            font-weight: bold;
            letter-spacing: 1px;
        }

        .label-body {
            padding: 14px 16px;
        }

        .label-kode {
            font-size: 28px;
            font-weight: bold;
            text-align: center;
            border: 2px solid #333;
            padding: 6px;
            margin-bottom: 12px;
            letter-spacing: 3px;
            background-color: #fffde7;
        }

        .label-divider {
            border: none;
            border-top: 1px solid #ccc;
            margin: 8px 0;
        }

        .label-row {
            display: flex;
            margin-bottom: 5px;
            font-size: 12px;
            line-height: 1.5;
        }

        .label-key {
            font-weight: bold;
            min-width: 70px;
            color: #444;
            flex-shrink: 0;
        }

        .label-sep {
            margin: 0 6px;
            flex-shrink: 0;
        }

        .label-val {
            color: #111;
        }

        .label-footer {
            background-color: #f8f8f8;
            border-top: 1px solid #ddd;
            text-align: center;
            padding: 6px;
            font-size: 10px;
            color: #888;
            border-radius: 0 0 4px 4px;
        }

        /* Saat dicetak */
        @media print {
            body {
                background: white;
                padding: 10mm;
                justify-content: flex-start;
            }

            .tombol-cetak,
            .info-cetak {
                display: none;
            }

            .label-buku {
                box-shadow: none;
                width: 9cm;
                border: 2pt solid #000;
            }

            .label-header {
                background-color: #1a3a5c !important;
                color: white !important;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .label-kode {
                background-color: #fffde7 !important;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }
        }
    </style>
</head>
<body>

    <!-- Tombol aksi -->
    <div class="tombol-cetak">
        <button class="btn-print" onclick="window.print()">&#128438; Cetak / Simpan sebagai PDF</button>
        <button class="btn-close" onclick="window.close()">&#10006; Tutup</button>
    </div>

    <div class="info-cetak">
        Label untuk 1 buku &mdash; Dicetak: <?= date('d F Y, H:i') ?> WIB
    </div>

    <!-- Label tunggal -->
    <div class="label-buku">

        <div class="label-header">
            &#128218; PERPUSTAKAAN &mdash; LIBRARY INFORMATION SYSTEM
        </div>

        <div class="label-body">

            <div class="label-kode">
                <?= strtoupper($book['code_book']) ?>
            </div>

            <hr class="label-divider">

            <div class="label-row">
                <span class="label-key">Judul</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['title_book'] ?></span>
            </div>
            <div class="label-row">
                <span class="label-key">Penulis</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['author_book'] ?></span>
            </div>
            <div class="label-row">
                <span class="label-key">Penerbit</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['publisher_book'] ?></span>
            </div>
            <div class="label-row">
                <span class="label-key">Tahun</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['published_year'] ?></span>
            </div>
            <?php if(!empty($book['isbn_book'])): ?>
            <div class="label-row">
                <span class="label-key">ISBN</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['isbn_book'] ?></span>
            </div>
            <?php endif; ?>
            <?php if(!empty($book['description_book'])): ?>
            <div class="label-row">
                <span class="label-key">Ket.</span>
                <span class="label-sep">:</span>
                <span class="label-val"><?= $book['description_book'] ?></span>
            </div>
            <?php endif; ?>

        </div>

        <div class="label-footer">
            ID Buku: <?= $book['id_book'] ?>
        </div>

    </div>

    <script>
        window.onload = function () {
            window.print();
        };
    </script>

</body>
</html>
```

### Perbedaan `cetak_label_satu.php` vs `cetak_label_buku.php`

| | `cetak_label_buku.php` (semua) | `cetak_label_satu.php` (satu) |
|---|---|---|
| Data yang diterima | `$books` (array banyak buku) | `$book` (satu buku) |
| Ukuran label | Lebih kecil, masuk grid 2 kolom | Lebih besar, satu label ditengah |
| Kode buku | Font 20px | Font 28px |
| Loop | `foreach($books as $book)` | Tidak ada loop |
| Footer | Tidak ada | Ada "ID Buku" |
| Keterangan | Tidak ditampilkan | Ditampilkan jika ada |

**Simpan file.**

---

## LANGKAH 5 — Uji Coba

### Pastikan Server Berjalan

Pastikan XAMPP/Laragon aktif (Apache dan MySQL menyala).

### Buka Halaman Cetak Label

Buka browser, akses:
```
http://localhost:8081/laporan/label-buku
```

**Yang diharapkan:**
- Menu "Laporan" di sidebar menampilkan sub-menu baru **"Cetak Label Buku"**
- Halaman menampilkan tabel buku dengan kolom **Aksi** berisi tombol kuning **"Cetak Label"**
- Di pojok kanan atas ada tombol merah **"Cetak Semua Label"**

### Uji Cetak Semua Label

1. Klik tombol **"Cetak Semua Label"** (merah)
2. Tab baru terbuka
3. Dialog cetak muncul otomatis
4. Terlihat label-label buku dalam format 2 kolom
5. Untuk simpan PDF: pilih **"Save as PDF"** → klik **Save**

### Uji Cetak Label Satu Buku

1. Klik tombol **"Cetak Label"** (kuning) pada salah satu baris buku
2. Tab baru terbuka
3. Dialog cetak muncul otomatis
4. Terlihat satu label besar di tengah halaman
5. Untuk simpan PDF: pilih **"Save as PDF"** → klik **Save**

### Cara Simpan sebagai PDF di Browser

| Browser | Cara |
|---|---|
| Chrome / Edge | Di dialog print → **Destination** → pilih **"Save as PDF"** → klik **Save** |
| Firefox | Di dialog print → **Printer** → pilih **"Microsoft Print to PDF"** atau **"Save to PDF"** → klik **Print** |

---

## Ringkasan File yang Dibuat/Diedit

| File | Aksi | Isi Perubahan |
|---|---|---|
| `sidebar.php` | Edit | Tambah 1 sub-menu "Cetak Label Buku" di dalam menu Laporan |
| `LaporanController.php` | Edit | Tambah 3 fungsi: `labelBuku()`, `cetakLabelBuku()`, `cetakLabelSatu($id)` |
| `Routes.php` | Edit | Tambah 3 route baru untuk label buku |
| `laporan/label_buku.php` | Buat baru | Halaman daftar buku + tombol Cetak Semua Label + tombol Cetak Label per baris |
| `laporan/cetak_label_buku.php` | Buat baru | Cetak semua label dalam grid 2 kolom, dialog print otomatis |
| `laporan/cetak_label_satu.php` | Buat baru | Cetak label satu buku ukuran besar di tengah, dialog print otomatis |

---

## Checklist Sebelum Demo

- [ ] Sub-menu "Cetak Label Buku" sudah muncul di bawah menu Laporan di sidebar
- [ ] 3 fungsi baru sudah ditambahkan di `LaporanController.php` dan disimpan
- [ ] 3 route baru sudah ditambahkan di `Routes.php` dan disimpan
- [ ] File `label_buku.php` sudah ada di `app/Views/laporan/`
- [ ] File `cetak_label_buku.php` sudah ada di `app/Views/laporan/`
- [ ] File `cetak_label_satu.php` sudah ada di `app/Views/laporan/`
- [ ] `http://localhost:8081/laporan/label-buku` bisa dibuka tanpa error
- [ ] Tombol "Cetak Semua Label" membuka tab baru dengan grid label
- [ ] Tombol "Cetak Label" per baris membuka tab baru dengan label satu buku
- [ ] Dialog cetak muncul otomatis di tab baru
- [ ] Tombol cetak tidak ikut tercetak di PDF
- [ ] Warna header label biru tetap muncul saat dicetak

---

## Masalah yang Sering Terjadi

| Gejala / Error | Kemungkinan Penyebab | Solusi |
|---|---|---|
| `Can't find a route for 'GET: laporan/label-buku'` | Route belum ditambahkan | Tambahkan 3 route baru di `Routes.php` |
| Sub-menu "Cetak Label Buku" tidak muncul di sidebar | `sidebar.php` belum diedit atau belum disimpan | Buka `sidebar.php`, cek kode, simpan ulang |
| Tab baru terbuka tapi halaman error | Nama fungsi di Controller salah atau View belum ada | Cek nama fungsi dan pastikan file View sudah dibuat |
| Dialog cetak tidak muncul otomatis | Browser memblokir popup | Klik ikon di address bar → izinkan popup dari localhost |
| Warna biru header label hilang saat cetak | Browser tidak cetak warna latar | Di dialog print, centang **"Background graphics"** |
| Label terpotong di tengah saat pindah halaman | CSS `break-inside` tidak didukung browser lama | Pastikan menggunakan Chrome/Edge versi terbaru |
| Tombol "Tutup" tidak berfungsi | Tab tidak dibuka sebagai popup | Tombol `window.close()` hanya berfungsi jika tab dibuka via `target="_blank"` — sudah benar di kode kita |
