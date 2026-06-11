# Tutorial: Membuat Fitur Master Member (Cara Copy-Paste dari Master Buku)

> **Untuk siapa tutorial ini?**
> Tutorial ini dibuat untuk mahasiswa yang belum terbiasa dengan pemrograman. Setiap langkah dijelaskan secara detail — mulai dari membuka file, apa yang diubah, hingga mengapa harus diubah. Ikuti urutannya dari atas ke bawah jangan dibolak-balik.

---

## Apa yang Akan Kita Buat?

Kita akan membuat fitur **Master Member** — yaitu halaman untuk mengelola data anggota perpustakaan. Fitur ini akan memiliki fungsi:
- Melihat daftar semua anggota
- Menambah anggota baru
- Mengedit data anggota
- Menghapus anggota

Strateginya: **copy semua file milik Master Buku, lalu edit isinya** agar bekerja untuk data Member.

---

## Gambaran File yang Akan Dibuat

Berikut daftar file yang harus ada setelah tutorial ini selesai:

```
app/
├── Controllers/
│   └── MemberController.php        ← BARU (copy dari BookController.php)
├── Models/
│   └── MemberModel.php             ← BARU (copy dari BookModel.php)
├── Views/
│   └── members/
│       ├── index.php               ← BARU (copy dari books/index.php)
│       ├── create.php              ← BARU (copy dari books/create.php)
│       └── edit.php                ← BARU (copy dari books/edit.php)
├── Database/
│   └── Migrations/
│       └── 2026-06-11-000001_CreateMemberTable.php  ← BARU (copy dari CreateBookTable.php)
└── Config/
    └── Routes.php                  ← EDIT (tambahkan route member)
```

---

## Data Member yang Akan Disimpan

Tabel `members` di database nanti akan punya kolom-kolom berikut:

| Nama Kolom di Database | Keterangan |
|---|---|
| `id_member` | Nomor urut otomatis (tidak perlu diisi manual) |
| `code_member` | Kode anggota, contoh: `MBR-001` |
| `name_member` | Nama lengkap anggota |
| `email_member` | Alamat email anggota |
| `phone_member` | Nomor telepon anggota |
| `address_member` | Alamat lengkap anggota |
| `join_date` | Tanggal bergabung menjadi anggota |

---

## LANGKAH 1 — Buat File Migration (Struktur Tabel Database)

### Apa itu Migration?

Migration adalah file PHP yang bertugas **membuat tabel di database secara otomatis**. Kita tidak perlu buka phpMyAdmin dan klik-klik untuk bikin tabel — cukup tulis di file ini lalu jalankan satu perintah.

### Cara Membuat File Migration

**1a. Buka folder Migration di VS Code**

Navigasi ke folder:
```
app/Database/Migrations/
```

Di sana sudah ada file:
```
2026-05-08-115603_CreateBookTable.php
```

**1b. Copy file tersebut**

- Klik kanan file `2026-05-08-115603_CreateBookTable.php`
- Pilih **Copy**
- Klik kanan di area kosong dalam folder yang sama
- Pilih **Paste**

File hasil copy akan bernama sesuatu seperti `2026-05-08-115603_CreateBookTable copy.php`.

**1c. Rename file hasil copy**

- Klik kanan file copy tersebut → **Rename**
- Ubah namanya menjadi:

```
2026-06-11-000001_CreateMemberTable.php
```

> **Mengapa nama filenya harus seperti itu?**
> Format nama migration CodeIgniter adalah `TAHUN-BULAN-HARI-URUTAN_NamaClass.php`.
> Angka tanggal di depan menentukan urutan eksekusi. Karena migration Buku dibuat 2026-05-08, kita beri tanggal 2026-06-11 agar migration Member dibuat setelahnya.

**1d. Buka file yang baru dibuat dan ganti SELURUH isinya**

Hapus semua kode yang ada, lalu tulis kode berikut:

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class CreateMemberTable extends Migration
{
    public function up()
    {
        $this->forge->addField([
            'id_member' => [
                'type'           => 'INT',
                'constraint'     => 11,
                'auto_increment' => true,
                'unsigned'       => true,
            ],
            'code_member' => [
                'type'       => 'VARCHAR',
                'constraint' => 20,
            ],
            'name_member' => [
                'type'       => 'VARCHAR',
                'constraint' => 100,
            ],
            'email_member' => [
                'type'       => 'VARCHAR',
                'constraint' => 100,
                'null'       => true,
            ],
            'phone_member' => [
                'type'       => 'VARCHAR',
                'constraint' => 20,
                'null'       => true,
            ],
            'address_member' => [
                'type' => 'TEXT',
                'null' => true,
            ],
            'join_date' => [
                'type' => 'DATE',
                'null' => true,
            ],
            'created_at' => [
                'type' => 'DATETIME',
                'null' => true,
            ],
            'updated_at' => [
                'type' => 'DATETIME',
                'null' => true,
            ],
            'deleted_at' => [
                'type' => 'DATETIME',
                'null' => true,
            ],
        ]);
        $this->forge->addKey('id_member', true);
        $this->forge->createTable('members');
    }

    public function down()
    {
        $this->forge->dropTable('members');
    }
}
```

### Apa Bedanya dengan File Buku?

| File Buku (asli) | File Member (baru) |
|---|---|
| `class CreateBookTable` | `class CreateMemberTable` |
| `'id_book'` | `'id_member'` |
| `'code_book'`, `'title_book'`, dll | `'code_member'`, `'name_member'`, dll |
| `createTable('books')` | `createTable('members')` |
| `dropTable('books')` | `dropTable('members')` |

**Simpan file** (Ctrl+S / Cmd+S).

---

## LANGKAH 2 — Jalankan Migration (Buat Tabel di Database)

### Cara Membuka Terminal di VS Code

- Tekan **Ctrl+` ** (tombol backtick, di sebelah kiri angka 1 pada keyboard)
- Atau klik menu **Terminal → New Terminal**

Pastikan terminal berada di folder project. Jika sudah benar, prompt akan terlihat seperti:
```
.../library_information_system $
```

### Jalankan Perintah

Ketik perintah berikut lalu tekan Enter:

```bash
php spark migrate
```

### Hasil yang Diharapkan

Jika berhasil, terminal akan menampilkan pesan seperti:
```
Running (App) migrations...
2026-06-11-000001_CreateMemberTable
All migrations have been applied successfully.
```

### Verifikasi di phpMyAdmin

Buka `http://localhost/phpmyadmin`, pilih database `library_information_system`, pastikan tabel `members` sudah muncul dengan kolom-kolom yang benar.

> **Jika ada error saat migrate:** Pastikan nama class di dalam file (`class CreateMemberTable`) sama persis dengan bagian nama di nama file (`_CreateMemberTable.php`) — huruf besar kecilnya harus sama.

---

## LANGKAH 3 — Buat File Model

### Apa itu Model?

Model adalah file PHP yang menjadi **jembatan antara aplikasi dan tabel database**. Lewat model inilah kita bisa menyimpan, mengambil, mengupdate, dan menghapus data dari tabel `members`.

### Cara Membuat MemberModel.php

**3a. Buka folder Model**

Navigasi ke:
```
app/Models/
```

Di sana ada file `BookModel.php`.

**3b. Copy file BookModel.php**

- Klik kanan `BookModel.php` → **Copy**
- Klik kanan di area kosong → **Paste**
- Rename hasilnya menjadi `MemberModel.php`

**3c. Buka MemberModel.php dan ganti SELURUH isinya**

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class MemberModel extends Model
{
    protected $table            = 'members';
    protected $primaryKey       = 'id_member';
    protected $useAutoIncrement = true;
    protected $useSoftDeletes   = true;
    protected $allowedFields    = [
        'code_member',
        'name_member',
        'email_member',
        'phone_member',
        'address_member',
        'join_date',
    ];

    protected $useTimestamps = false;
}
```

### Penjelasan Tiap Baris yang Diubah

| Baris | Isi | Penjelasan |
|---|---|---|
| `class MemberModel` | Nama class | Harus sama dengan nama file |
| `$table = 'members'` | Nama tabel | Harus sama dengan nama tabel yang dibuat di migration |
| `$primaryKey = 'id_member'` | Kolom primary key | Kolom ID utama di tabel members |
| `$useSoftDeletes = true` | Hapus lunak | Data yang "dihapus" tidak benar-benar hilang dari DB, hanya ditandai |
| `$allowedFields` | Kolom yang boleh diisi | Daftar kolom yang boleh diisi lewat form (ID tidak perlu dicantumkan karena auto) |

**Simpan file.**

---

## LANGKAH 4 — Buat File Controller

### Apa itu Controller?

Controller adalah file PHP yang menjadi **otak dari fitur**. Ia menerima permintaan dari browser (misalnya: "tampilkan daftar member"), mengambil data dari Model, lalu mengirim hasilnya ke View untuk ditampilkan.

### Cara Membuat MemberController.php

**4a. Buka folder Controller**

Navigasi ke:
```
app/Controllers/
```

Di sana ada file `BookController.php`.

**4b. Copy file BookController.php**

- Klik kanan `BookController.php` → **Copy**
- Klik kanan di area kosong → **Paste**
- Rename hasilnya menjadi `MemberController.php`

**4c. Buka MemberController.php dan ganti SELURUH isinya**

```php
<?php

namespace App\Controllers;
use App\Models\MemberModel;

class MemberController extends BaseController
{
    public function index(): string
    {
        $memberModel = new MemberModel();
        $data['members'] = $memberModel->findAll();
        $data['title']   = 'Daftar Member';

        return view('members/index', $data);
    }

    public function create()
    {
        $data['title'] = 'Tambah Member';
        return view('members/create', $data);
    }

    public function store()
    {
        $memberModel = new MemberModel();
        $isSaved = $memberModel->save([
            'code_member'    => $this->request->getPost('kode'),
            'name_member'    => $this->request->getPost('nama'),
            'email_member'   => $this->request->getPost('email'),
            'phone_member'   => $this->request->getPost('telepon'),
            'address_member' => $this->request->getPost('alamat'),
            'join_date'      => $this->request->getPost('tanggal_bergabung'),
        ]);

        if ($isSaved) {
            session()->setFlashdata('success', 'Member berhasil disimpan!');
        } else {
            session()->setFlashdata('error', 'Gagal menyimpan member. Silakan coba lagi.');
        }

        return redirect()->to('/list/members');
    }

    public function edit($id)
    {
        $memberModel           = new MemberModel();
        $data['title']         = 'Edit Member';
        $data['detail_member'] = $memberModel->find($id);
        $data['id']            = $id;

        return view('members/edit', $data);
    }

    public function update()
    {
        $id          = $this->request->getPost('id');
        $memberModel = new MemberModel();

        $isUpdated = $memberModel->update($id, [
            'code_member'    => $this->request->getPost('kode'),
            'name_member'    => $this->request->getPost('nama'),
            'email_member'   => $this->request->getPost('email'),
            'phone_member'   => $this->request->getPost('telepon'),
            'address_member' => $this->request->getPost('alamat'),
            'join_date'      => $this->request->getPost('tanggal_bergabung'),
        ]);

        if ($isUpdated) {
            session()->setFlashdata('success', 'Member berhasil diperbarui!');
        } else {
            session()->setFlashdata('error', 'Gagal memperbarui member. Silakan coba lagi.');
        }

        return redirect()->to('/list/members');
    }

    public function delete($id)
    {
        $memberModel = new MemberModel();
        $isDeleted   = $memberModel->delete($id);

        if ($isDeleted) {
            session()->setFlashdata('success', 'Member berhasil dihapus!');
        } else {
            session()->setFlashdata('error', 'Gagal menghapus member. Silakan coba lagi.');
        }

        return redirect()->to('/list/members');
    }
}
```

### Penjelasan Fungsi-Fungsi di Controller

| Nama Fungsi | Tugasnya |
|---|---|
| `index()` | Mengambil semua data member dari database lalu tampilkan di halaman daftar |
| `create()` | Hanya membuka halaman form tambah member (belum menyimpan) |
| `store()` | Menerima data dari form tambah, lalu simpan ke database |
| `edit($id)` | Mengambil data satu member berdasarkan ID, lalu buka form edit yang sudah terisi |
| `update()` | Menerima data dari form edit, lalu perbarui data di database |
| `delete($id)` | Menghapus data satu member berdasarkan ID |

### Kenapa `getPost('kode')` Bukan `getPost('code_member')`?

`getPost('kode')` → mengambil data dari **form HTML** (nama field di form)
`'code_member'` → nama **kolom di database**

Keduanya boleh berbeda. Yang penting konsisten antara nama `name` di form dan nama di `getPost(...)`.

**Simpan file.**

---

## LANGKAH 5 — Buat File View (Tampilan Halaman)

### Apa itu View?

View adalah file PHP yang berisi **tampilan HTML** yang dilihat pengguna di browser. Ada tiga halaman yang perlu dibuat: daftar member, form tambah, dan form edit.

### Buat Folder `members` di dalam Views

**5a. Navigasi ke folder:**
```
app/Views/
```

Di sana sudah ada folder `books/`. Buat folder baru:
- Klik kanan di dalam folder `Views/` → **New Folder**
- Beri nama: `members`

---

### 5b. Buat File `index.php` (Halaman Daftar Member)

Di dalam folder `app/Views/members/`, buat file baru bernama `index.php`.

> **Cara buat file baru di VS Code:** Klik kanan folder `members` → **New File** → ketik `index.php`

**Isi file `app/Views/members/index.php` dengan kode berikut:**

```php
<?= $this->extend('layouts/template') ?>

<?= $this->section('header') ?>
<div class="row mb-2">
    <div class="col-sm-6">
        <h1><?php echo $title; ?></h1>
    </div>
</div>
<?= $this->endSection() ?>

<?= $this->section('content') ?>
<div class="card">
    <div class="card-header">
        <h3 class="card-title">Data Member</h3>

        <?php if(session()->getFlashdata('success')): ?>
            <div class="alert alert-success">
                <?= session()->getFlashdata('success') ?>
            </div>
        <?php endif; ?>

        <?php if(session()->getFlashdata('error')): ?>
            <div class="alert alert-danger">
                <?= session()->getFlashdata('error') ?>
            </div>
        <?php endif; ?>

        <div class="card-tools">
            <a href="<?= base_url('create/member') ?>" class="btn btn-primary btn-sm">
                <i class="fas fa-plus"></i> Tambah Member
            </a>
        </div>
    </div>

    <div class="card-body table-responsive">
        <table class="table table-bordered table-striped">
            <thead>
                <tr>
                    <th width="50">No.</th>
                    <th>Kode</th>
                    <th>Nama Lengkap</th>
                    <th>Email</th>
                    <th>Telepon</th>
                    <th>Alamat</th>
                    <th>Tanggal Bergabung</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                <?php $no = 0; ?>
                <?php foreach($members as $member): ?>
                    <?php $no++; ?>
                    <tr>
                        <td><?php echo $no ?></td>
                        <td><?php echo $member['code_member'] ?></td>
                        <td><?php echo $member['name_member'] ?></td>
                        <td><?php echo $member['email_member'] ?></td>
                        <td><?php echo $member['phone_member'] ?></td>
                        <td><?php echo $member['address_member'] ?></td>
                        <td><?php echo $member['join_date'] ?></td>
                        <td>
                            <a href="<?= base_url('edit/member/' . $member['id_member']) ?>" class="btn btn-info btn-sm">Edit</a>
                            <form action="<?= base_url('delete/member/' . $member['id_member']) ?>" method="post" style="display: inline;">
                                <?= csrf_field() ?>
                                <button type="submit" class="btn btn-danger btn-sm" onclick="return confirm('Apakah Anda yakin ingin menghapus member ini?')">Hapus</button>
                            </form>
                        </td>
                    </tr>
                <?php endforeach; ?>
            </tbody>
        </table>
    </div>
</div>
<?= $this->endSection() ?>
```

**Yang berbeda dari `books/index.php`:**

| Di books/index.php | Di members/index.php |
|---|---|
| `Data Buku` | `Data Member` |
| `foreach($books as $book)` | `foreach($members as $member)` |
| `$book['title_book']`, `$book['code_book']`, dll | `$member['name_member']`, `$member['code_member']`, dll |
| `base_url('create/book')` | `base_url('create/member')` |
| `base_url('edit/book/' . $book['id_book'])` | `base_url('edit/member/' . $member['id_member'])` |
| `base_url('delete/book/' . $book['id_book'])` | `base_url('delete/member/' . $member['id_member'])` |

**Simpan file.**

---

### 5c. Buat File `create.php` (Halaman Form Tambah Member)

Di dalam folder `app/Views/members/`, buat file baru bernama `create.php`.

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
<div class="card card-primary">
    <div class="card-header">
        <h3 class="card-title">Form Tambah Member</h3>
    </div>

    <?php if(session()->getFlashdata('error')): ?>
        <div class="alert alert-danger">
            <?= session()->getFlashdata('error') ?>
        </div>
    <?php endif; ?>

    <form action="<?= base_url('create/member') ?>" method="post">
        <?= csrf_field() ?>
        <div class="card-body">

            <div class="form-group">
                <label>Kode Member</label>
                <input type="text" name="kode" class="form-control" placeholder="Contoh: MBR-001">
            </div>
            <div class="form-group">
                <label>Nama Lengkap</label>
                <input type="text" name="nama" class="form-control" placeholder="Masukkan nama lengkap">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="email" name="email" class="form-control" placeholder="Masukkan alamat email">
            </div>
            <div class="form-group">
                <label>No. Telepon</label>
                <input type="text" name="telepon" class="form-control" placeholder="Contoh: 08123456789">
            </div>
            <div class="form-group">
                <label>Alamat</label>
                <textarea name="alamat" class="form-control" rows="3" placeholder="Masukkan alamat lengkap"></textarea>
            </div>
            <div class="form-group">
                <label>Tanggal Bergabung</label>
                <input type="date" name="tanggal_bergabung" class="form-control">
            </div>

            <div class="card-footer">
                <button type="submit" class="btn btn-primary">Simpan</button>
                <a href="<?= base_url('list/members') ?>" class="btn btn-secondary">Batal</a>
            </div>
        </div>
    </form>
</div>

<?= $this->endSection() ?>

<?= $this->section('js') ?>
<?= $this->endSection() ?>
```

**Yang berbeda dari `books/create.php`:**

| Di books/create.php | Di members/create.php |
|---|---|
| `Form Tambah Buku` | `Form Tambah Member` |
| `action="...create/book"` | `action="...create/member"` |
| Field: judul, kode, isbn, penulis, penerbit, tahun, keterangan | Field: kode, nama, email, telepon, alamat, tanggal bergabung |
| `name="judul"`, `name="kode"`, dll | `name="kode"`, `name="nama"`, dll |

> **Penting:** Nilai `name="..."` pada tiap `<input>` harus sama persis dengan yang ada di `getPost(...)` di Controller. Contoh: `name="kode"` di sini → `getPost('kode')` di Controller.

**Simpan file.**

---

### 5d. Buat File `edit.php` (Halaman Form Edit Member)

Di dalam folder `app/Views/members/`, buat file baru bernama `edit.php`.

**Isi dengan kode berikut:**

```php
<?= $this->extend('layouts/template') ?>

<?= $this->section('header') ?>
<div class="row mb-2">
    <div class="col-sm-6">
        <h1><?php echo $title; ?></h1>
    </div>
</div>
<?= $this->endSection() ?>

<?= $this->section('content') ?>
<div class="card card-primary">
    <div class="card-header">
        <h3 class="card-title">Form Edit Member</h3>
    </div>

    <?php if(session()->getFlashdata('success')): ?>
        <div class="alert alert-success">
            <?= session()->getFlashdata('success') ?>
        </div>
    <?php endif; ?>

    <?php if(session()->getFlashdata('error')): ?>
        <div class="alert alert-danger">
            <?= session()->getFlashdata('error') ?>
        </div>
    <?php endif; ?>

    <form action="<?= base_url('update/member') ?>" method="post">
        <?= csrf_field() ?>
        <div class="card-body">

            <input type="hidden" name="id" value="<?php echo $id; ?>">

            <div class="form-group">
                <label>Kode Member</label>
                <input type="text" name="kode" class="form-control" placeholder="Contoh: MBR-001" value="<?php echo $detail_member['code_member']; ?>">
            </div>
            <div class="form-group">
                <label>Nama Lengkap</label>
                <input type="text" name="nama" class="form-control" placeholder="Masukkan nama lengkap" value="<?php echo $detail_member['name_member']; ?>">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="email" name="email" class="form-control" placeholder="Masukkan alamat email" value="<?php echo $detail_member['email_member']; ?>">
            </div>
            <div class="form-group">
                <label>No. Telepon</label>
                <input type="text" name="telepon" class="form-control" placeholder="Contoh: 08123456789" value="<?php echo $detail_member['phone_member']; ?>">
            </div>
            <div class="form-group">
                <label>Alamat</label>
                <textarea name="alamat" class="form-control" rows="3"><?php echo $detail_member['address_member']; ?></textarea>
            </div>
            <div class="form-group">
                <label>Tanggal Bergabung</label>
                <input type="date" name="tanggal_bergabung" class="form-control" value="<?php echo $detail_member['join_date']; ?>">
            </div>

            <div class="card-footer">
                <button type="submit" class="btn btn-primary" onclick="return confirm('Apakah anda yakin akan mengupdate data ini?')">Update</button>
                <a href="<?= base_url('list/members') ?>" class="btn btn-secondary">Batal</a>
            </div>
        </div>
    </form>
</div>
<?= $this->endSection() ?>
```

**Yang berbeda dari `books/edit.php`:**

| Di books/edit.php | Di members/edit.php |
|---|---|
| `Form Edit Buku` | `Form Edit Member` |
| `action="...update/book"` | `action="...update/member"` |
| `$detail_buku['title_book']`, dll | `$detail_member['name_member']`, dll |
| Field buku: judul, kode, isbn, dll | Field member: kode, nama, email, dll |

> **Perhatian:** Baris `<input type="hidden" name="id" value="...">` sangat penting! Baris ini menyimpan ID member secara tersembunyi agar Controller tahu data mana yang harus di-update. Jangan dihapus.

**Simpan file.**

---

## LANGKAH 6 — Tambahkan Routes

### Apa itu Routes?

Routes adalah **peta alamat URL** aplikasi. Setiap kali browser mengakses URL tertentu, Routes menentukan fungsi Controller mana yang harus dipanggil. Tanpa routes, browser tidak tahu harus memanggil apa.

### Cara Menambah Routes

**6a. Buka file:**
```
app/Config/Routes.php
```

**6b. Isi file saat ini terlihat seperti ini:**

```php
<?php

use CodeIgniter\Router\RouteCollection;

/**
 * @var RouteCollection $routes
 */
$routes->get('/', 'Home::index');

$routes->get('/dashboard', 'DashboardController::index');
$routes->get('list/books', 'BookController::index');
$routes->get('list/users', 'UserController::index');

$routes->get('/create/book', 'BookController::create');
$routes->post('/create/book', 'BookController::store');

$routes->get('/edit/book/(:num)', 'BookController::edit/$1');
$routes->post('/update/book', 'BookController::update');

$routes->post('/delete/book/(:num)', 'BookController::delete/$1');
```

**6c. Tambahkan baris berikut di bagian PALING BAWAH file:**

```php
// Member Routes
$routes->get('list/members', 'MemberController::index');

$routes->get('/create/member', 'MemberController::create');
$routes->post('/create/member', 'MemberController::store');

$routes->get('/edit/member/(:num)', 'MemberController::edit/$1');
$routes->post('/update/member', 'MemberController::update');

$routes->post('/delete/member/(:num)', 'MemberController::delete/$1');
```

**6d. Hasil akhir file Routes.php akan terlihat seperti ini:**

```php
<?php

use CodeIgniter\Router\RouteCollection;

/**
 * @var RouteCollection $routes
 */
$routes->get('/', 'Home::index');

$routes->get('/dashboard', 'DashboardController::index');
$routes->get('list/books', 'BookController::index');
$routes->get('list/users', 'UserController::index');

$routes->get('/create/book', 'BookController::create');
$routes->post('/create/book', 'BookController::store');

$routes->get('/edit/book/(:num)', 'BookController::edit/$1');
$routes->post('/update/book', 'BookController::update');

$routes->post('/delete/book/(:num)', 'BookController::delete/$1');

// Member Routes
$routes->get('list/members', 'MemberController::index');

$routes->get('/create/member', 'MemberController::create');
$routes->post('/create/member', 'MemberController::store');

$routes->get('/edit/member/(:num)', 'MemberController::edit/$1');
$routes->post('/update/member', 'MemberController::update');

$routes->post('/delete/member/(:num)', 'MemberController::delete/$1');
```

### Penjelasan Tiap Baris Routes

| Baris Routes | Artinya |
|---|---|
| `$routes->get('list/members', ...)` | Saat browser membuka `/list/members`, panggil fungsi `index()` |
| `$routes->get('/create/member', ...)` | Saat buka `/create/member`, tampilkan form tambah (panggil `create()`) |
| `$routes->post('/create/member', ...)` | Saat form dikirim ke `/create/member`, simpan data (panggil `store()`) |
| `$routes->get('/edit/member/(:num)', ...)` | Saat buka `/edit/member/5`, buka form edit untuk ID 5 (panggil `edit(5)`) |
| `$routes->post('/update/member', ...)` | Saat form edit dikirim, perbarui data (panggil `update()`) |
| `$routes->post('/delete/member/(:num)', ...)` | Saat tombol hapus ditekan untuk ID tertentu, hapus data |

> **`get` vs `post`:** `get` dipakai untuk membuka halaman biasa. `post` dipakai untuk mengirim data dari form (ketika menekan tombol Simpan/Update/Hapus).

**Simpan file.**

---

## LANGKAH 7 — Uji Coba di Browser

### 7a. Pastikan Server Berjalan

Pastikan XAMPP/Laragon sudah aktif (Apache dan MySQL menyala).

### 7b. Buka Halaman Daftar Member

Buka browser, ketik di address bar:

```
http://localhost:8081/list/members
```

Jika berhasil, akan muncul halaman dengan tabel kosong (karena belum ada data) dan tombol **Tambah Member**.

### 7c. Coba Tambah Member Baru

1. Klik tombol **Tambah Member**
2. Isi semua kolom pada form
3. Klik **Simpan**
4. Halaman akan kembali ke daftar member, dan data yang baru saja diisi akan muncul di tabel
5. Muncul notifikasi hijau: *"Member berhasil disimpan!"*

### 7d. Coba Edit Member

1. Klik tombol **Edit** pada salah satu data member
2. Data yang sudah ada akan muncul terisi di form
3. Ubah salah satu data
4. Klik **Update**
5. Halaman kembali ke daftar, data sudah berubah

### 7e. Coba Hapus Member

1. Klik tombol **Hapus** pada salah satu data
2. Akan muncul konfirmasi: *"Apakah Anda yakin ingin menghapus member ini?"*
3. Klik **OK**
4. Data hilang dari daftar

---

## Ringkasan: File Apa yang Kita Buat dan Apa Isinya?

| File | Disalin dari | Yang Diubah |
|---|---|---|
| `CreateMemberTable.php` | `CreateBookTable.php` | Nama class, nama kolom, nama tabel |
| `MemberModel.php` | `BookModel.php` | Nama class, nama tabel, primary key, allowedFields |
| `MemberController.php` | `BookController.php` | Nama class, nama model, nama variabel, nama field form, redirect URL |
| `members/index.php` | `books/index.php` | Label kolom, nama variabel loop, URL tombol aksi |
| `members/create.php` | `books/create.php` | Judul form, URL action, nama field input |
| `members/edit.php` | `books/edit.php` | Judul form, URL action, nama field input, nama variabel data |
| `Routes.php` | *(ditambahkan saja)* | Tambah 6 baris routes member di bagian bawah |

---

## Checklist Sebelum Demo

Centang satu per satu sebelum melakukan demo:

- [ ] File `2026-06-11-000001_CreateMemberTable.php` sudah ada di `app/Database/Migrations/`
- [ ] Perintah `php spark migrate` sudah dijalankan dan berhasil
- [ ] Tabel `members` sudah muncul di phpMyAdmin
- [ ] File `MemberModel.php` sudah ada di `app/Models/`
- [ ] File `MemberController.php` sudah ada di `app/Controllers/`
- [ ] Folder `app/Views/members/` sudah ada
- [ ] File `index.php`, `create.php`, `edit.php` sudah ada di dalam folder `members/`
- [ ] Routes member sudah ditambahkan di bagian bawah `Routes.php`
- [ ] `http://localhost:8081/list/members` bisa dibuka tanpa error
- [ ] Form tambah member bisa menyimpan data baru
- [ ] Tombol Edit menampilkan form yang sudah terisi data
- [ ] Tombol Hapus menghapus data dari daftar

---

## Masalah yang Sering Terjadi dan Cara Mengatasinya

| Pesan Error | Kemungkinan Penyebab | Solusi |
|---|---|---|
| `Class "App\Controllers\MemberController" not found` | Nama class di dalam file tidak sama dengan nama file | Pastikan `class MemberController` di dalam file, dan nama filenya `MemberController.php` |
| `Can't find a route for 'GET: list/members'` | Routes belum ditambahkan | Tambahkan routes di `Routes.php` dan simpan file |
| `Class "App\Models\MemberModel" not found` | File MemberModel.php tidak ada atau nama class salah | Cek apakah file ada di `app/Models/` dan nama class-nya `MemberModel` |
| Tabel `members` tidak ada | Migration belum dijalankan | Jalankan `php spark migrate` di terminal |
| `SQLSTATE[HY000]: General error` saat simpan | Nama kolom di `allowedFields` tidak cocok dengan kolom di tabel | Samakan nama kolom di `MemberModel.php` dengan nama kolom di migration |
| Data tidak tersimpan tapi tidak ada error | Nama `name` di form tidak sama dengan nama di `getPost()` | Samakan `name="kode"` di HTML dengan `getPost('kode')` di Controller |
