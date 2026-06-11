# Tutorial: Membuat Fitur Master Member Spark (Cara Spark CLI)

> **Untuk siapa tutorial ini?**
> Tutorial ini dibuat untuk mahasiswa yang belum terbiasa dengan pemrograman. Setiap langkah dijelaskan secara detail. Cara ini menggunakan **Terminal** untuk menjalankan perintah yang akan otomatis membuatkan file, sehingga kita tidak perlu copy-paste manual. Ikuti urutannya dari atas ke bawah jangan dibolak-balik.

---

## Apa Bedanya Cara Spark CLI dengan Cara Copy-Paste?

| | Cara Copy-Paste | Cara Spark CLI |
|---|---|---|
| Cara kerja | Copy file buku → edit sendiri | Ketik perintah di terminal → file dibuat otomatis |
| Nama class & namespace | Harus ditulis manual, rawan salah ketik | Dijamin benar karena dibuat otomatis |
| Kecepatan | Lebih cepat jika tahu persis apa yang diubah | Lebih aman dari kesalahan struktur |
| Butuh terminal | Tidak wajib | **Wajib** |
| Hasil akhir | Sama | Sama |

Keduanya menghasilkan fitur yang **persis sama**. Pilih cara yang lebih nyaman.

---

## Apa yang Akan Kita Buat?

Fitur **Master Member Spark** untuk mengelola data anggota perpustakaan, dengan fungsi:
- Melihat daftar semua anggota
- Menambah anggota baru
- Mengedit data anggota
- Menghapus anggota

> **Mengapa namanya "Member Spark"?**
> Nama ini dipakai untuk membedakan modul yang dibuat dengan **Cara Spark CLI** ini dari modul yang dibuat dengan Cara Copy-Paste. Di database pun tabelnya berbeda: `members_spark`.

---

## Gambaran File yang Akan Dibuat

```
app/
├── Controllers/
│   └── MemberSparkController.php        ← dibuat oleh: php spark make:controller
├── Models/
│   └── MemberSparkModel.php             ← dibuat oleh: php spark make:model
├── Views/
│   └── members_spark/
│       ├── index.php                    ← dibuat MANUAL (spark tidak bisa buat view)
│       ├── create.php                   ← dibuat MANUAL
│       └── edit.php                     ← dibuat MANUAL
├── Database/
│   └── Migrations/
│       └── ..._CreateMemberSparkTable.php  ← dibuat oleh: php spark make:migration
└── Config/
    └── Routes.php                       ← EDIT manual (tambah route member spark)
```

---

## Cara Membuka Terminal di VS Code

Sebelum mulai, pastikan Anda tahu cara membuka terminal:

1. Buka VS Code
2. Tekan **Ctrl + ` ** (tombol backtick/grave accent, di sebelah kiri angka `1` pada keyboard)
   - Atau klik menu **Terminal → New Terminal** di menu bar atas
3. Terminal akan muncul di bagian bawah VS Code

**Pastikan terminal berada di folder project.** Prompt-nya harus terlihat seperti:
```
.../library_information_system $
```

Jika tidak, ketik perintah ini untuk masuk ke folder project (sesuaikan pathnya):
```bash
cd /path/ke/folder/library_information_system
```

---

## Data Member Spark yang Akan Disimpan

Tabel `members_spark` di database nanti akan punya kolom-kolom berikut:

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

## LANGKAH 1 — Generate File Migration dengan Spark

### Apa itu Migration?

Migration adalah file PHP yang bertugas **membuat tabel di database secara otomatis**. Kita tidak perlu buka phpMyAdmin dan klik-klik untuk bikin tabel — cukup tulis di file ini lalu jalankan satu perintah.

### Jalankan Perintah

Di terminal, ketik perintah berikut lalu tekan **Enter**:

```bash
php spark make:migration CreateMemberSparkTable
```

### Hasil yang Diharapkan

Terminal akan menampilkan:
```
Created file: app/Database/Migrations/2026-06-11-xxxxxx_CreateMemberSparkTable.php
```

File baru akan muncul di folder `app/Database/Migrations/` dengan nama seperti:
```
2026-06-11-093308_CreateMemberSparkTable.php
```

> Angka di tengah (093308) adalah timestamp/waktu otomatis dari sistem — tidak perlu diubah.

### Edit File Migration yang Baru Dibuat

Spark membuat file dengan isi kosong. Kita perlu mengisinya.

**Buka file tersebut** (klik di VS Code), lalu **ganti seluruh isinya** dengan kode berikut:

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class CreateMemberSparkTable extends Migration
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
        $this->forge->createTable('members_spark');
    }

    public function down()
    {
        $this->forge->dropTable('members_spark');
    }
}
```

### Penjelasan Kode Migration

| Bagian Kode | Artinya |
|---|---|
| `class CreateMemberSparkTable` | Nama class — harus sama dengan bagian nama di nama file |
| `addField([...])` | Mendaftarkan semua kolom yang akan dibuat di tabel |
| `'type' => 'INT'` | Tipe data angka bulat |
| `'type' => 'VARCHAR'` | Tipe data teks pendek |
| `'type' => 'TEXT'` | Tipe data teks panjang (untuk alamat) |
| `'type' => 'DATE'` | Tipe data tanggal (format: YYYY-MM-DD) |
| `'null' => true` | Kolom ini boleh dikosongkan |
| `'auto_increment' => true` | Nomor otomatis bertambah (1, 2, 3, ...) |
| `addKey('id_member', true)` | Menjadikan `id_member` sebagai Primary Key |
| `createTable('members_spark')` | Buat tabel dengan nama `members_spark` di database |
| `dropTable('members_spark')` di `down()` | Hapus tabel jika migration di-rollback |

**Simpan file** (Ctrl+S / Cmd+S).

---

## LANGKAH 2 — Jalankan Migration

### Jalankan Perintah

Kembali ke terminal, ketik:

```bash
php spark migrate
```

Tekan **Enter**.

### Hasil yang Diharapkan

```
Running (App) migrations...
2026-06-11-xxxxxx_CreateMemberSparkTable
All migrations have been applied successfully.
```

### Verifikasi di phpMyAdmin

1. Buka browser, akses `http://localhost/phpmyadmin`
2. Pilih database `library_information_system`
3. Pastikan tabel `members_spark` sudah muncul dengan kolom-kolom yang benar

> **Jika ada error:** Pastikan nama class di dalam file (`class CreateMemberSparkTable`) sama persis dengan bagian nama di nama file (`_CreateMemberSparkTable.php`) — huruf besar kecilnya harus sama.

---

## LANGKAH 3 — Generate File Model dengan Spark

### Apa itu Model?

Model adalah file PHP yang menjadi **jembatan antara aplikasi dan tabel database**. Lewat model inilah kita bisa menyimpan, mengambil, mengupdate, dan menghapus data dari tabel `members_spark`.

### Jalankan Perintah

Di terminal, ketik:

```bash
php spark make:model MemberSparkModel
```

Tekan **Enter**.

Spark akan menanyakan beberapa pertanyaan. Jawab dengan menekan **Enter** saja (pilih default) untuk semua pertanyaan, atau ketik `n` lalu Enter jika diminta konfirmasi yang tidak diperlukan.

### Hasil yang Diharapkan

```
Created file: app/Models/MemberSparkModel.php
```

### Edit File MemberSparkModel.php

File yang dihasilkan spark berisi template kosong. Buka `app/Models/MemberSparkModel.php` di VS Code, lalu **ganti seluruh isinya** dengan kode berikut:

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class MemberSparkModel extends Model
{
    protected $table            = 'members_spark';
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

### Penjelasan Tiap Baris

| Baris | Isi | Penjelasan |
|---|---|---|
| `class MemberSparkModel` | Nama class | Harus sama dengan nama file |
| `$table = 'members_spark'` | Nama tabel | Harus sama dengan nama tabel yang dibuat di migration |
| `$primaryKey = 'id_member'` | Kolom primary key | Kolom ID utama di tabel members_spark |
| `$useSoftDeletes = true` | Hapus lunak | Data "dihapus" tidak hilang dari DB, hanya ditandai di kolom `deleted_at` |
| `$allowedFields` | Kolom yang boleh diisi | Daftar kolom yang boleh diisi lewat form — **ID tidak perlu** karena auto increment |
| `$useTimestamps = false` | Nonaktifkan timestamp otomatis | Kita kelola sendiri atau tidak pakai |

**Simpan file.**

---

## LANGKAH 4 — Generate File Controller dengan Spark

### Apa itu Controller?

Controller adalah file PHP yang menjadi **otak dari fitur**. Ia menerima permintaan dari browser, mengambil data dari Model, lalu mengirim hasilnya ke View untuk ditampilkan.

### Jalankan Perintah

Di terminal, ketik:

```bash
php spark make:controller MemberSparkController
```

Tekan **Enter**.

### Hasil yang Diharapkan

```
Created file: app/Controllers/MemberSparkController.php
```

### Edit File MemberSparkController.php

File yang dihasilkan spark hanya berisi template dasar kosong. Buka `app/Controllers/MemberSparkController.php` di VS Code, lalu **ganti seluruh isinya** dengan kode berikut:

```php
<?php

namespace App\Controllers;
use App\Models\MemberSparkModel;

class MemberSparkController extends BaseController
{
    public function index(): string
    {
        $memberSparkModel       = new MemberSparkModel();
        $data['members_spark']  = $memberSparkModel->findAll();
        $data['title']          = 'Daftar Member Spark';

        return view('members_spark/index', $data);
    }

    public function create()
    {
        $data['title'] = 'Tambah Member Spark';
        return view('members_spark/create', $data);
    }

    public function store()
    {
        $memberSparkModel = new MemberSparkModel();
        $isSaved = $memberSparkModel->save([
            'code_member'    => $this->request->getPost('kode'),
            'name_member'    => $this->request->getPost('nama'),
            'email_member'   => $this->request->getPost('email'),
            'phone_member'   => $this->request->getPost('telepon'),
            'address_member' => $this->request->getPost('alamat'),
            'join_date'      => $this->request->getPost('tanggal_bergabung'),
        ]);

        if ($isSaved) {
            session()->setFlashdata('success', 'Member Spark berhasil disimpan!');
        } else {
            session()->setFlashdata('error', 'Gagal menyimpan Member Spark. Silakan coba lagi.');
        }

        return redirect()->to('/list/members-spark');
    }

    public function edit($id)
    {
        $memberSparkModel             = new MemberSparkModel();
        $data['title']                = 'Edit Member Spark';
        $data['detail_member_spark']  = $memberSparkModel->find($id);
        $data['id']                   = $id;

        return view('members_spark/edit', $data);
    }

    public function update()
    {
        $id               = $this->request->getPost('id');
        $memberSparkModel = new MemberSparkModel();

        $isUpdated = $memberSparkModel->update($id, [
            'code_member'    => $this->request->getPost('kode'),
            'name_member'    => $this->request->getPost('nama'),
            'email_member'   => $this->request->getPost('email'),
            'phone_member'   => $this->request->getPost('telepon'),
            'address_member' => $this->request->getPost('alamat'),
            'join_date'      => $this->request->getPost('tanggal_bergabung'),
        ]);

        if ($isUpdated) {
            session()->setFlashdata('success', 'Member Spark berhasil diperbarui!');
        } else {
            session()->setFlashdata('error', 'Gagal memperbarui Member Spark. Silakan coba lagi.');
        }

        return redirect()->to('/list/members-spark');
    }

    public function delete($id)
    {
        $memberSparkModel = new MemberSparkModel();
        $isDeleted        = $memberSparkModel->delete($id);

        if ($isDeleted) {
            session()->setFlashdata('success', 'Member Spark berhasil dihapus!');
        } else {
            session()->setFlashdata('error', 'Gagal menghapus Member Spark. Silakan coba lagi.');
        }

        return redirect()->to('/list/members-spark');
    }
}
```

### Penjelasan Fungsi-Fungsi di Controller

| Nama Fungsi | Tugasnya |
|---|---|
| `index()` | Mengambil semua data member spark dari database lalu tampilkan di halaman daftar |
| `create()` | Hanya membuka halaman form tambah (belum menyimpan apa-apa) |
| `store()` | Menerima data dari form tambah, lalu simpan ke database |
| `edit($id)` | Mengambil data satu member spark berdasarkan ID, lalu buka form edit yang sudah terisi |
| `update()` | Menerima data dari form edit, lalu perbarui data di database |
| `delete($id)` | Menghapus data satu member spark berdasarkan ID |

### Kenapa `getPost('kode')` Bukan `getPost('code_member')`?

`getPost('kode')` → mengambil data dari **form HTML** (nilai `name` pada elemen `<input>`)
`'code_member'` → nama **kolom di database**

Keduanya boleh berbeda namanya. Yang penting:
- Nama `name="kode"` di file View **harus sama** dengan `getPost('kode')` di Controller
- Nama `'code_member'` di Controller **harus sama** dengan nama kolom di database

**Simpan file.**

---

## LANGKAH 5 — Buat File View (Tampilan Halaman)

### Mengapa Spark Tidak Bisa Membuat View?

Spark memiliki perintah `make:view` tapi hasilnya hanya file kosong — tidak ada struktur HTML sama sekali. Lebih cepat dan lebih baik kita buat sendiri menggunakan template yang sudah ada di project ini.

### Buat Folder `members_spark`

**Cara 1 — Lewat VS Code:**
- Klik kanan folder `app/Views/` di panel kiri VS Code
- Pilih **New Folder**
- Ketik: `members_spark`
- Tekan Enter

**Cara 2 — Lewat Terminal:**
```bash
mkdir app/Views/members_spark
```

---

### 5a. Buat File `index.php` (Halaman Daftar Member Spark)

Di dalam folder `app/Views/members_spark/`, buat file baru bernama `index.php`.

> **Cara buat file baru:** Klik kanan folder `members_spark` → **New File** → ketik `index.php` → Enter

**Isi file `app/Views/members_spark/index.php` dengan kode berikut:**

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
        <h3 class="card-title">Data Member Spark</h3>

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
            <a href="<?= base_url('create/member-spark') ?>" class="btn btn-primary btn-sm">
                <i class="fas fa-plus"></i> Tambah Member Spark
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
                <?php foreach($members_spark as $member): ?>
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
                            <a href="<?= base_url('edit/member-spark/' . $member['id_member']) ?>" class="btn btn-info btn-sm">Edit</a>
                            <form action="<?= base_url('delete/member-spark/' . $member['id_member']) ?>" method="post" style="display: inline;">
                                <?= csrf_field() ?>
                                <button type="submit" class="btn btn-danger btn-sm" onclick="return confirm('Apakah Anda yakin ingin menghapus Member Spark ini?')">Hapus</button>
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

**Penjelasan bagian penting:**

| Kode | Artinya |
|---|---|
| `$this->extend('layouts/template')` | Halaman ini menggunakan template/layout utama yang sudah ada |
| `$this->section('content')` ... `endSection()` | Isi konten yang akan ditampilkan di dalam template |
| `foreach($members_spark as $member)` | Perulangan — tampilkan satu baris tabel untuk setiap data member spark |
| `$member['name_member']` | Ambil nilai kolom `name_member` dari data yang sedang diproses |
| `csrf_field()` | Token keamanan wajib untuk setiap form POST |
| `session()->getFlashdata('success')` | Menampilkan notifikasi sukses/gagal dari operasi sebelumnya |

**Simpan file.**

---

### 5b. Buat File `create.php` (Halaman Form Tambah Member Spark)

Di dalam folder `app/Views/members_spark/`, buat file baru bernama `create.php`.

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
        <h3 class="card-title">Form Tambah Member Spark</h3>
    </div>

    <?php if(session()->getFlashdata('error')): ?>
        <div class="alert alert-danger">
            <?= session()->getFlashdata('error') ?>
        </div>
    <?php endif; ?>

    <form action="<?= base_url('create/member-spark') ?>" method="post">
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
                <a href="<?= base_url('list/members-spark') ?>" class="btn btn-secondary">Batal</a>
            </div>
        </div>
    </form>
</div>

<?= $this->endSection() ?>

<?= $this->section('js') ?>
<?= $this->endSection() ?>
```

**Penjelasan bagian penting:**

| Kode | Artinya |
|---|---|
| `<form action="...create/member-spark" method="post">` | Form akan dikirim ke URL `/create/member-spark` menggunakan method POST |
| `name="kode"` | Nama field ini — **harus sama** dengan `getPost('kode')` di Controller |
| `name="nama"` | Nama field ini — **harus sama** dengan `getPost('nama')` di Controller |
| `type="email"` | Browser akan validasi format email secara otomatis |
| `type="date"` | Browser akan tampilkan date picker (kalender) secara otomatis |
| `<textarea>` | Digunakan untuk input teks panjang (seperti alamat) |
| Tombol `Batal` | Link kembali ke halaman daftar tanpa menyimpan |

> **Aturan penting:** Nilai `name="..."` pada tiap input **harus persis sama** dengan yang ada di `getPost(...)` di Controller. Contoh: `name="kode"` di sini → `getPost('kode')` di Controller.

**Simpan file.**

---

### 5c. Buat File `edit.php` (Halaman Form Edit Member Spark)

Di dalam folder `app/Views/members_spark/`, buat file baru bernama `edit.php`.

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
        <h3 class="card-title">Form Edit Member Spark</h3>
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

    <form action="<?= base_url('update/member-spark') ?>" method="post">
        <?= csrf_field() ?>
        <div class="card-body">

            <input type="hidden" name="id" value="<?php echo $id; ?>">

            <div class="form-group">
                <label>Kode Member</label>
                <input type="text" name="kode" class="form-control" placeholder="Contoh: MBR-001" value="<?php echo $detail_member_spark['code_member']; ?>">
            </div>
            <div class="form-group">
                <label>Nama Lengkap</label>
                <input type="text" name="nama" class="form-control" placeholder="Masukkan nama lengkap" value="<?php echo $detail_member_spark['name_member']; ?>">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="email" name="email" class="form-control" placeholder="Masukkan alamat email" value="<?php echo $detail_member_spark['email_member']; ?>">
            </div>
            <div class="form-group">
                <label>No. Telepon</label>
                <input type="text" name="telepon" class="form-control" placeholder="Contoh: 08123456789" value="<?php echo $detail_member_spark['phone_member']; ?>">
            </div>
            <div class="form-group">
                <label>Alamat</label>
                <textarea name="alamat" class="form-control" rows="3"><?php echo $detail_member_spark['address_member']; ?></textarea>
            </div>
            <div class="form-group">
                <label>Tanggal Bergabung</label>
                <input type="date" name="tanggal_bergabung" class="form-control" value="<?php echo $detail_member_spark['join_date']; ?>">
            </div>

            <div class="card-footer">
                <button type="submit" class="btn btn-primary" onclick="return confirm('Apakah anda yakin akan mengupdate data ini?')">Update</button>
                <a href="<?= base_url('list/members-spark') ?>" class="btn btn-secondary">Batal</a>
            </div>
        </div>
    </form>
</div>
<?= $this->endSection() ?>
```

**Penjelasan bagian penting:**

| Kode | Artinya |
|---|---|
| `<input type="hidden" name="id" value="...">` | Menyimpan ID member secara tersembunyi — wajib ada agar Controller tahu data mana yang diupdate |
| `$detail_member_spark['code_member']` | Mengisi field dengan data lama dari database agar bisa diedit |
| `<form action="...update/member-spark" method="post">` | Form dikirim ke route update |
| Baris `<textarea>` untuk alamat | Teks yang ada di database ditampilkan di antara tag `<textarea>` |

> **Perhatian:** Baris `<input type="hidden" name="id">` sangat penting. Jangan dihapus. Tanpa ini, sistem tidak tahu data member spark mana yang harus diperbarui.

**Simpan file.**

---

## LANGKAH 6 — Tambahkan Routes dan Uji Coba

### Apa itu Routes?

Routes adalah **peta alamat URL** aplikasi. Setiap kali browser mengakses URL tertentu, Routes menentukan fungsi Controller mana yang harus dipanggil. Tanpa routes, browser tidak tahu harus memanggil apa dan akan menampilkan error 404.

### Buka File Routes.php

Buka file:
```
app/Config/Routes.php
```

### Isi File Saat Ini (sebagai referensi):

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

### Tambahkan Baris Berikut di Bagian PALING BAWAH File:

```php
// Member Spark Routes
$routes->get('list/members-spark', 'MemberSparkController::index');

$routes->get('/create/member-spark', 'MemberSparkController::create');
$routes->post('/create/member-spark', 'MemberSparkController::store');

$routes->get('/edit/member-spark/(:num)', 'MemberSparkController::edit/$1');
$routes->post('/update/member-spark', 'MemberSparkController::update');

$routes->post('/delete/member-spark/(:num)', 'MemberSparkController::delete/$1');
```

### Penjelasan Tiap Baris Routes

| Baris Routes | Artinya |
|---|---|
| `$routes->get('list/members-spark', ...)` | Saat browser buka `/list/members-spark`, panggil fungsi `index()` |
| `$routes->get('/create/member-spark', ...)` | Saat buka `/create/member-spark`, tampilkan form tambah |
| `$routes->post('/create/member-spark', ...)` | Saat form tambah dikirim, simpan data |
| `$routes->get('/edit/member-spark/(:num)', ...)` | Saat buka `/edit/member-spark/5`, buka form edit ID 5 |
| `$routes->post('/update/member-spark', ...)` | Saat form edit dikirim, perbarui data |
| `$routes->post('/delete/member-spark/(:num)', ...)` | Saat tombol hapus ditekan, hapus data |

> **`get` vs `post`:**
> - `get` = membuka halaman biasa lewat address bar browser
> - `post` = mengirim data dari form (setelah menekan tombol Simpan / Update / Hapus)

**Simpan file.**

---

## Uji Coba di Browser

### Pastikan Server Berjalan

Pastikan XAMPP/Laragon sudah aktif (Apache dan MySQL menyala).

Opsional — jalankan server development CodeIgniter lewat terminal:
```bash
php spark serve --port 8081
```

### Buka Halaman Daftar Member Spark

Buka browser, ketik di address bar:

```
http://localhost:8081/list/members-spark
```

Jika berhasil, akan muncul halaman dengan tabel kosong dan tombol **Tambah Member Spark**.

### Coba Tambah Member Spark Baru

1. Klik tombol **Tambah Member Spark**
2. Isi semua kolom pada form
3. Klik **Simpan**
4. Halaman akan kembali ke daftar, data baru muncul di tabel
5. Notifikasi hijau muncul: *"Member Spark berhasil disimpan!"*

### Coba Edit Member Spark

1. Klik tombol **Edit** pada salah satu data
2. Form terbuka dengan data yang sudah terisi
3. Ubah salah satu data
4. Klik **Update** → konfirmasi **OK**
5. Data di tabel sudah berubah

### Coba Hapus Member Spark

1. Klik tombol **Hapus** pada salah satu data
2. Konfirmasi muncul → klik **OK**
3. Data hilang dari tabel

---

## Rekap Perintah Spark yang Digunakan

| Perintah | File yang Dihasilkan | Perlu Diedit? |
|---|---|---|
| `php spark make:migration CreateMemberSparkTable` | `app/Database/Migrations/..._CreateMemberSparkTable.php` | Ya — isi kolom tabel |
| `php spark migrate` | *(tidak membuat file, menjalankan migration ke DB)* | Tidak |
| `php spark make:model MemberSparkModel` | `app/Models/MemberSparkModel.php` | Ya — isi konfigurasi model |
| `php spark make:controller MemberSparkController` | `app/Controllers/MemberSparkController.php` | Ya — isi semua fungsi CRUD |

> **Catatan:** Spark hanya membuat **kerangka file** (file kosong atau setengah terisi). Kita tetap harus mengisi kode logikanya sendiri.

---

## Checklist Sebelum Demo

Centang satu per satu sebelum melakukan demo:

- [ ] Perintah `php spark make:migration CreateMemberSparkTable` sudah dijalankan
- [ ] File migration sudah diisi dengan definisi kolom dan sudah disimpan
- [ ] Perintah `php spark migrate` sudah dijalankan dan berhasil
- [ ] Tabel `members_spark` sudah muncul di phpMyAdmin
- [ ] Perintah `php spark make:model MemberSparkModel` sudah dijalankan
- [ ] File `MemberSparkModel.php` sudah diisi dan disimpan
- [ ] Perintah `php spark make:controller MemberSparkController` sudah dijalankan
- [ ] File `MemberSparkController.php` sudah diisi dengan semua fungsi CRUD dan disimpan
- [ ] Folder `app/Views/members_spark/` sudah dibuat
- [ ] File `index.php`, `create.php`, `edit.php` sudah dibuat dan diisi di dalam folder `members_spark/`
- [ ] Routes member spark sudah ditambahkan di bagian bawah `Routes.php` dan disimpan
- [ ] `http://localhost:8081/list/members-spark` bisa dibuka tanpa error
- [ ] Form tambah Member Spark bisa menyimpan data baru
- [ ] Tombol Edit menampilkan form yang sudah terisi data
- [ ] Tombol Hapus menghapus data dari tabel

---

## Masalah yang Sering Terjadi dan Cara Mengatasinya

| Pesan Error | Kemungkinan Penyebab | Solusi |
|---|---|---|
| `Class "App\Controllers\MemberSparkController" not found` | File belum dibuat atau nama class di dalam file salah | Pastikan `class MemberSparkController` di dalam file, dan nama file `MemberSparkController.php` |
| `Can't find a route for 'GET: list/members-spark'` | Routes belum ditambahkan | Tambahkan routes di `Routes.php` dan simpan file |
| `Class "App\Models\MemberSparkModel" not found` | File MemberSparkModel.php tidak ada atau nama class salah | Cek file di `app/Models/` — nama class harus `MemberSparkModel` |
| Tabel `members_spark` tidak ada | `php spark migrate` belum dijalankan | Jalankan `php spark migrate` di terminal |
| `SQLSTATE[HY000]` saat simpan | Nama kolom di `allowedFields` tidak cocok dengan kolom tabel | Samakan nama kolom di `MemberSparkModel.php` dengan nama di migration |
| Data tidak tersimpan, tidak ada error | Nama `name` di form berbeda dengan `getPost()` di Controller | Samakan `name="kode"` di HTML dengan `getPost('kode')` di Controller |
| `php spark` tidak dikenal | Terminal tidak berada di folder project | Pastikan terminal berada di folder yang berisi file `spark` |
