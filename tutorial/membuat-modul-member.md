# Tutorial: Membuat Modul Master Member

Panduan ini menjelaskan cara membuat modul **Master Member** pada project Library Information System berbasis CodeIgniter 4. Modul member akan memiliki fitur CRUD (Create, Read, Update, Delete) lengkap, mirip seperti modul Buku yang sudah ada.

Ada **dua cara** yang akan dijelaskan:

- [Cara 1 — Copy & Edit File Buku](#cara-1--copy--edit-file-buku)
- [Cara 2 — Generate dengan Spark (CLI)](#cara-2--generate-dengan-spark-cli)

---

## Struktur File yang Akan Dibuat

| File | Lokasi |
|---|---|
| Migration | `app/Database/Migrations/..._CreateMemberTable.php` |
| Model | `app/Models/MemberModel.php` |
| Controller | `app/Controllers/MemberController.php` |
| View (index) | `app/Views/members/index.php` |
| View (create) | `app/Views/members/create.php` |
| View (edit) | `app/Views/members/edit.php` |
| Routes | `app/Config/Routes.php` |

---

## Data Member yang Akan Disimpan

| Kolom DB | Nama Field Form | Keterangan |
|---|---|---|
| `id_member` | *(auto)* | Primary key |
| `code_member` | `kode` | Kode anggota |
| `name_member` | `nama` | Nama lengkap |
| `email_member` | `email` | Email anggota |
| `phone_member` | `telepon` | No. telepon |
| `address_member` | `alamat` | Alamat |
| `join_date` | `tanggal_bergabung` | Tanggal daftar |

---

## Cara 1 — Copy & Edit File Buku

Cara ini cocok jika Anda lebih nyaman bekerja di file manager atau VS Code. Kita menduplikasi semua file modul buku, lalu mengganti kontennya untuk kebutuhan member.

### Langkah 1 — Buat Migration

Buat file baru di `app/Database/Migrations/`, beri nama mengikuti format tanggal CodeIgniter:

```
app/Database/Migrations/2026-06-11-000001_CreateMemberTable.php
```

**Referensi:** Buka `app/Database/Migrations/2026-05-08-115603_CreateBookTable.php` sebagai acuan, lalu buat file baru dengan isi berikut:

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

### Langkah 2 — Jalankan Migration

Buka terminal di folder project, jalankan:

```bash
php spark migrate
```

Tabel `members` akan terbuat di database.

### Langkah 3 — Buat Model

Buka `app/Models/BookModel.php`, **copy** isinya, lalu buat file baru `app/Models/MemberModel.php`.

Edit isinya menjadi:

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

> **Yang diubah dari BookModel:**
> - `$table` → `'members'`
> - `$primaryKey` → `'id_member'`
> - `$allowedFields` → field-field member

### Langkah 4 — Buat Controller

Buka `app/Controllers/BookController.php`, **copy** isinya, lalu buat file baru `app/Controllers/MemberController.php`.

Edit isinya menjadi:

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
        $memberModel = new MemberModel();
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

### Langkah 5 — Buat Folder dan View

Buat folder baru `app/Views/members/`, lalu buat tiga file view di dalamnya.

#### 5a. File `app/Views/members/index.php`

Copy dari `app/Views/books/index.php`, lalu edit:

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
        <h3 class="card-title">Data Member</h3>

        <?php if (session()->getFlashdata('success')): ?>
            <div class="alert alert-success"><?= session()->getFlashdata('success') ?></div>
        <?php endif; ?>

        <?php if (session()->getFlashdata('error')): ?>
            <div class="alert alert-danger"><?= session()->getFlashdata('error') ?></div>
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
                    <th>Nama</th>
                    <th>Email</th>
                    <th>Telepon</th>
                    <th>Alamat</th>
                    <th>Tanggal Bergabung</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                <?php $no = 0; ?>
                <?php foreach ($members as $member): ?>
                    <?php $no++; ?>
                    <tr>
                        <td><?= $no ?></td>
                        <td><?= $member['code_member'] ?></td>
                        <td><?= $member['name_member'] ?></td>
                        <td><?= $member['email_member'] ?></td>
                        <td><?= $member['phone_member'] ?></td>
                        <td><?= $member['address_member'] ?></td>
                        <td><?= $member['join_date'] ?></td>
                        <td>
                            <a href="<?= base_url('edit/member/' . $member['id_member']) ?>" class="btn btn-info btn-sm">Edit</a>
                            <form action="<?= base_url('delete/member/' . $member['id_member']) ?>" method="post" style="display:inline;">
                                <?= csrf_field() ?>
                                <button type="submit" class="btn btn-danger btn-sm"
                                    onclick="return confirm('Yakin ingin menghapus member ini?')">Hapus</button>
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

#### 5b. File `app/Views/members/create.php`

Copy dari `app/Views/books/create.php`, lalu edit:

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

    <?php if (session()->getFlashdata('error')): ?>
        <div class="alert alert-danger"><?= session()->getFlashdata('error') ?></div>
    <?php endif; ?>

    <form action="<?= base_url('create/member') ?>" method="post">
        <?= csrf_field() ?>
        <div class="card-body">
            <div class="form-group">
                <label>Kode Member</label>
                <input type="text" name="kode" class="form-control" placeholder="Masukkan kode member">
            </div>
            <div class="form-group">
                <label>Nama Lengkap</label>
                <input type="text" name="nama" class="form-control" placeholder="Masukkan nama lengkap">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="email" name="email" class="form-control" placeholder="Masukkan email">
            </div>
            <div class="form-group">
                <label>No. Telepon</label>
                <input type="text" name="telepon" class="form-control" placeholder="Masukkan no. telepon">
            </div>
            <div class="form-group">
                <label>Alamat</label>
                <textarea name="alamat" class="form-control" placeholder="Masukkan alamat"></textarea>
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
```

#### 5c. File `app/Views/members/edit.php`

Copy dari `app/Views/books/edit.php`, lalu edit:

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
        <h3 class="card-title">Form Edit Member</h3>
    </div>

    <?php if (session()->getFlashdata('success')): ?>
        <div class="alert alert-success"><?= session()->getFlashdata('success') ?></div>
    <?php endif; ?>

    <?php if (session()->getFlashdata('error')): ?>
        <div class="alert alert-danger"><?= session()->getFlashdata('error') ?></div>
    <?php endif; ?>

    <form action="<?= base_url('update/member') ?>" method="post">
        <?= csrf_field() ?>
        <div class="card-body">
            <input type="hidden" name="id" value="<?= $id ?>">

            <div class="form-group">
                <label>Kode Member</label>
                <input type="text" name="kode" class="form-control" value="<?= $detail_member['code_member'] ?>">
            </div>
            <div class="form-group">
                <label>Nama Lengkap</label>
                <input type="text" name="nama" class="form-control" value="<?= $detail_member['name_member'] ?>">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="email" name="email" class="form-control" value="<?= $detail_member['email_member'] ?>">
            </div>
            <div class="form-group">
                <label>No. Telepon</label>
                <input type="text" name="telepon" class="form-control" value="<?= $detail_member['phone_member'] ?>">
            </div>
            <div class="form-group">
                <label>Alamat</label>
                <textarea name="alamat" class="form-control"><?= $detail_member['address_member'] ?></textarea>
            </div>
            <div class="form-group">
                <label>Tanggal Bergabung</label>
                <input type="date" name="tanggal_bergabung" class="form-control" value="<?= $detail_member['join_date'] ?>">
            </div>

            <div class="card-footer">
                <button type="submit" class="btn btn-primary"
                    onclick="return confirm('Yakin akan mengupdate data ini?')">Update</button>
                <a href="<?= base_url('list/members') ?>" class="btn btn-secondary">Batal</a>
            </div>
        </div>
    </form>
</div>
<?= $this->endSection() ?>
```

### Langkah 6 — Tambah Routes

Buka `app/Config/Routes.php`, tambahkan baris berikut setelah routes buku:

```php
// Member Routes
$routes->get('list/members', 'MemberController::index');
$routes->get('/create/member', 'MemberController::create');
$routes->post('/create/member', 'MemberController::store');
$routes->get('/edit/member/(:num)', 'MemberController::edit/$1');
$routes->post('/update/member', 'MemberController::update');
$routes->post('/delete/member/(:num)', 'MemberController::delete/$1');
```

### Langkah 7 — Uji Coba

Buka browser, akses: `http://localhost:8081/list/members`

---

## Cara 2 — Generate dengan Spark (CLI)

Cara ini lebih cepat karena CodeIgniter menyediakan generator otomatis lewat perintah `php spark`. Cocok untuk menghindari salah ketik nama class atau namespace.

> **Syarat:** Terminal harus berada di folder root project (tempat `spark` berada).

### Langkah 1 — Generate Migration

```bash
php spark make:migration CreateMemberTable
```

File baru akan muncul di `app/Database/Migrations/`. Buka file tersebut dan isi method `up()` dan `down()` dengan kode yang sama seperti di **Cara 1 Langkah 1**.

Setelah diisi, jalankan:

```bash
php spark migrate
```

### Langkah 2 — Generate Model

```bash
php spark make:model MemberModel
```

File `app/Models/MemberModel.php` akan dibuat otomatis. Spark akan menanyakan beberapa pilihan — jawab atau langsung edit file yang dihasilkan dengan isi yang sama seperti **Cara 1 Langkah 3**.

### Langkah 3 — Generate Controller

```bash
php spark make:controller MemberController
```

File `app/Controllers/MemberController.php` akan dibuat. Edit isinya dengan kode yang sama seperti **Cara 1 Langkah 4**.

### Langkah 4 — Buat View (Manual)

Spark **tidak** men-generate view secara otomatis (kecuali menggunakan `--restful` yang formatnya berbeda). Buat folder dan ketiga file view secara manual sama seperti **Cara 1 Langkah 5**.

```bash
# Buat folder views/members
mkdir app/Views/members
```

Kemudian buat tiga file: `index.php`, `create.php`, `edit.php` (isinya sama dengan Cara 1).

### Langkah 5 — Tambah Routes

Sama seperti **Cara 1 Langkah 6**, tambahkan routes member di `app/Config/Routes.php`.

### Langkah 6 — Uji Coba

```bash
# Opsional: jalankan server development
php spark serve --port 8081
```

Akses `http://localhost:8081/list/members`.

---

## Perbandingan Dua Cara

| Aspek | Cara 1 (Copy & Edit) | Cara 2 (Spark) |
|---|---|---|
| Kecepatan awal | Lebih cepat (tinggal ubah) | Perlu isi ulang tiap file |
| Risiko typo | Bisa mewarisi typo dari file asal | Namespace & struktur dijamin benar |
| Cocok untuk | Pemula, struktur mirip file yang ada | Yang sudah familiar CLI |
| Butuh terminal | Tidak wajib | Wajib |
| View otomatis | Manual | Manual juga |

---

## Checklist Akhir

- [ ] File migration dibuat dan `php spark migrate` berhasil
- [ ] Tabel `members` muncul di database
- [ ] `MemberModel.php` ada di `app/Models/`
- [ ] `MemberController.php` ada di `app/Controllers/`
- [ ] Folder `app/Views/members/` berisi `index.php`, `create.php`, `edit.php`
- [ ] Routes member sudah ditambahkan di `Routes.php`
- [ ] `http://localhost:8081/list/members` bisa diakses
- [ ] Form tambah member berhasil menyimpan data
- [ ] Tombol Edit dan Hapus berfungsi
