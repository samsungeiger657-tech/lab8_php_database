# Lab8Web - Praktikum 8: PHP dan Database MySQL

| | |
|---|---|
| **Nama** | Faizal Abdul Mu'id |
| **NIM** | 312510024 |
| **Kelas** | I251A |
| **Mata Kuliah** | Pemrograman Web OBE |
| **Program Studi** | Teknik Informatika, Universitas Pelita Bangsa |

## Tujuan Praktikum

1. Memahami konsep dasar database.
2. Memahami konsep dasar CRUD menggunakan PHP.
3. Membuat program CRUD sederhana menggunakan PHP dan MySQL.

## Struktur Folder

```
lab8_php_database/
├── gambar/          -> tempat menyimpan foto barang
├── koneksi.php      -> koneksi ke database
├── index.php        -> menampilkan data (Read)
├── tambah.php       -> menambah data (Create)
├── ubah.php         -> mengubah data (Update)
├── hapus.php        -> menghapus data (Delete)
└── screenshots/     -> screenshot untuk laporan ini
```

---

## 1. Persiapan

### 1.1 Menjalankan MySQL Server

Buka **XAMPP Control Panel**, lalu klik tombol **Start** pada modul **Apache** dan **MySQL**. Kedua modul harus berwarna hijau, artinya web server dan database server sudah berjalan.

![XAMPP Control Panel](screenshots/01-xampp.png)

### 1.2 Mengakses phpMyAdmin

Buka browser dan akses `http://localhost/phpmyadmin/`. Di sini database dibuat dan dikelola lewat tampilan web.

---

## 2. Membuat Database dan Tabel

Studi kasus yang dipakai adalah **Data Barang**.

### 2.1 Membuat Database

Pada tab **SQL** di phpMyAdmin, jalankan perintah berikut untuk membuat database bernama `latihan1`.

```sql
CREATE DATABASE latihan1;
```

![Membuat database latihan1](screenshots/02-buat-database.png)

### 2.2 Membuat Tabel

Pilih database `latihan1`, lalu jalankan perintah berikut untuk membuat tabel `data_barang`.

```sql
CREATE TABLE data_barang (
  id_barang int(10) auto_increment Primary Key,
  kategori varchar(30),
  nama varchar(30),
  gambar varchar(100),
  harga_beli decimal(10,0),
  harga_jual decimal(10,0),
  stok int(4)
);
```

Penjelasan field:

| Field | Tipe Data | Keterangan |
|---|---|---|
| id_barang | INT (10) | Primary key dan auto increment, jadi id terisi otomatis |
| kategori | VARCHAR (30) | Kategori barang |
| nama | VARCHAR (30) | Nama barang |
| gambar | VARCHAR (100) | Nama atau lokasi file gambar |
| harga_beli | DECIMAL (10,0) | Harga beli barang |
| harga_jual | DECIMAL (10,0) | Harga jual barang |
| stok | INT (4) | Jumlah stok |

![Tampilan phpMyAdmin setelah tabel dibuat](screenshots/03-buat-tabel.png)

### 2.3 Menambahkan Data Awal

Tiga data contoh dimasukkan dengan perintah `INSERT INTO`.

```sql
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```

Setelah dijalankan, isi tabel bisa dicek lewat menu **Browse/Telusuri** pada tabel `data_barang`.

![Data barang di tabel data_barang](screenshots/04-data-barang.png)

---

## 3. Membuat Program CRUD

### 3.1 Menyiapkan Folder Project

Folder `lab8_php_database` dibuat di dalam `htdocs` (root directory web server XAMPP). Di dalamnya juga dibuat folder `gambar` untuk menyimpan foto barang. Folder ini bisa diakses lewat `http://localhost/lab8_php_database/`.

![Folder lab8_php_database di htdocs](screenshots/05-folder-lab8.png)

### 3.2 File Koneksi Database (`koneksi.php`)

File ini berisi pengaturan untuk menghubungkan PHP dengan MySQL:

- `$host`, `$user`, `$pass`, dan `$db` berisi alamat server, username, password, dan nama database (`latihan1`).
- `mysqli_connect()` dipakai untuk membuat koneksi ke database.
- Jika koneksi gagal, program menampilkan pesan "Koneksi ke server gagal." lalu berhenti dengan `die()`.
- File ini dipanggil oleh file lain memakai `include` atau `include_once`, jadi pengaturan koneksi cukup ditulis di satu tempat.

Untuk menguji koneksi, pesan `echo "Koneksi berhasil"` di-uncomment sementara, lalu file dibuka lewat browser.

![Tes koneksi berhasil](screenshots/06-tes-koneksi.png)

### 3.3 Menampilkan Data / Read (`index.php`)

File `index.php` bertugas menampilkan semua data barang dalam bentuk tabel.

Cara kerjanya:

1. `include("koneksi.php")` untuk menghubungkan ke database.
2. Query `SELECT * FROM data_barang` mengambil semua data dari tabel, hasilnya disimpan di `$result`.
3. `while($row = mysqli_fetch_array($result))` mengulang pengambilan data baris demi baris.
4. Setiap kolom ditampilkan dengan `<?= $row['nama']; ?>` dan seterusnya.
5. Jika tidak ada data, tabel menampilkan tulisan "Belum ada data".

![Tampilan index.php](screenshots/07-index.png)

### 3.4 Menambah Data / Create (`tambah.php`)

File `tambah.php` berisi form untuk menambah barang baru.

Cara kerjanya:

1. Form memakai `method="post"` dan `enctype="multipart/form-data"` supaya bisa mengirim file gambar.
2. Saat tombol **Simpan** diklik, `isset($_POST['submit'])` bernilai benar, lalu semua isian form diambil dari `$_POST`, dan file gambar diambil dari `$_FILES`.
3. Jika upload gambar tidak error, nama file dirapikan (spasi diganti `_`) dan dipindahkan ke folder `gambar` memakai `move_uploaded_file()`.
4. Data dimasukkan ke database dengan query `INSERT INTO data_barang`.
5. Setelah itu halaman diarahkan kembali ke `index.php` memakai `header('location: index.php')`.

Tampilan form tambah barang:

![Form tambah barang](screenshots/08-form-tambah.png)

Hasil setelah data baru disimpan:

![Hasil tambah data](screenshots/09-hasil-tambah.png)

### 3.5 Mengubah Data / Update (`ubah.php`)

File `ubah.php` dipakai untuk mengedit data barang yang sudah ada.

Cara kerjanya:

1. Halaman dibuka dengan membawa id barang lewat URL, contohnya `ubah.php?id=1`. Id diambil dengan `$_GET['id']`.
2. Data barang dengan id tersebut diambil dengan `SELECT ... WHERE id_barang`, lalu ditampilkan di dalam form sebagai nilai awal.
3. Id barang disimpan di input `hidden` supaya ikut terkirim saat form disubmit.
4. Saat tombol **Simpan** diklik, data diperbarui dengan query `UPDATE data_barang SET ... WHERE id_barang`.
5. Kolom `gambar` hanya diperbarui kalau pengguna memilih file gambar baru.
6. Setelah selesai, halaman kembali ke `index.php`.

Tampilan form ubah barang:

![Form ubah barang](screenshots/10-form-ubah.png)

Hasil setelah data diubah:

![Hasil ubah data](screenshots/11-hasil-ubah.png)

### 3.6 Menghapus Data / Delete (`hapus.php`)

File `hapus.php` menghapus data barang berdasarkan id yang dikirim lewat URL, contohnya `hapus.php?id=3`.

Cara kerjanya:

1. Id diambil dengan `$_GET['id']`.
2. Query `DELETE FROM data_barang WHERE id_barang = ...` dijalankan.
3. Halaman otomatis kembali ke `index.php`.

![Hasil hapus data](screenshots/12-hasil-hapus.png)

---

## Kesimpulan

Dari praktikum ini saya belajar cara membuat database dan tabel MySQL lewat phpMyAdmin, menghubungkannya dengan PHP memakai `mysqli_connect()`, dan membuat aplikasi CRUD sederhana (Create, Read, Update, Delete) untuk mengelola data barang.

## Cara Menjalankan

1. Salin folder project ke `htdocs` XAMPP.
2. Jalankan **Apache** dan **MySQL** dari XAMPP Control Panel.
3. Buat database `latihan1` dan tabel `data_barang` lewat phpMyAdmin (query ada di bagian 2).
4. Buka `http://localhost/lab8_php_database/` di browser.
