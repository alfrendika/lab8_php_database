# Lab8Web – Praktikum 8: PHP dan Database MySQL

**Mata Kuliah:** Pemrograman Web OBE – Teknik Informatika
**Rahsya Alfrendika / 312510339 / Kelas: I253C**

Aplikasi CRUD sederhana (Create, Read, Update, Delete) untuk data barang menggunakan PHP dan MySQL (XAMPP).

## Tujuan
1. Memahami konsep dasar database.
2. Memahami konsep dasar CRUD menggunakan PHP.
3. Membuat program CRUD sederhana menggunakan PHP.

## Struktur Folder
```
lab8_php_database/
├── database.sql     # perintah SQL (database, tabel, data awal)
├── koneksi.php      # koneksi ke MySQL
├── index.php        # Read   : menampilkan data
├── tambah.php       # Create : menambah data
├── ubah.php         # Update : mengubah data
├── hapus.php        # Delete : menghapus data
├── style.css        # tampilan
└── gambar/          # tempat upload gambar barang
```

## Langkah 1 – Persiapan: jalankan Apache & MySQL
Buka **XAMPP Control Panel**, klik **Start** pada modul **Apache** dan **MySQL** sampai statusnya hijau (MySQL port 3306).

## Langkah 2 – Buka phpMyAdmin
Buka `http://localhost/phpmyadmin/` di browser. phpMyAdmin dipakai sebagai MySQL client untuk membuat database dan tabel.

## Langkah 3 – Membuat database dan tabel
Pada tab **SQL**, jalankan:

```sql
CREATE DATABASE latihan1;

CREATE TABLE data_barang (
  id_barang  int(10) auto_increment PRIMARY KEY,
  kategori   varchar(30),
  nama       varchar(50),
  gambar     varchar(100),
  harga_beli decimal(10,0),
  harga_jual decimal(10,0),
  stok       int(4)
);
```
- `id_barang` = primary key yang bertambah otomatis (`auto_increment`).
- `decimal(10,0)` dipakai untuk harga (angka bulat hingga 10 digit).

## Langkah 4 – Menambahkan data awal
```sql
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
       ('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
       ('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```
Cek dengan `SELECT * FROM data_barang;`

## Langkah 5 – Membuat folder project
Buat folder `lab8_php_database` di `C:\xampp\htdocs\` (atau `D:\xampp\htdocs\`) lalu buka lewat `http://localhost/lab8_php_database/`. Buat juga subfolder `gambar`.

## Langkah 6 – `koneksi.php`
Menghubungkan PHP ke MySQL memakai `mysqli_connect($host, $user, $pass, $db)`. Jika gagal, tampil pesan "Koneksi ke server gagal." dan program berhenti (`die()`). Untuk tes, hilangkan komentar pada `echo "Koneksi berhasil"`.

## Langkah 7 – `index.php` (Read)
Menjalankan `SELECT * FROM data_barang`, lalu setiap baris ditampilkan ke tabel HTML memakai `while(mysqli_fetch_array(...))`. Kolom **Aksi** berisi link **Ubah** dan **Hapus**, serta ada link **Tambah Barang**.

## Langkah 8 – `tambah.php` (Create)
Form (method `post`, `enctype="multipart/form-data"` karena ada upload file). Saat tombol **Simpan** ditekan: data dibaca dari `$_POST`, gambar dipindahkan ke folder `gambar/` dengan `move_uploaded_file()`, lalu di-`INSERT` ke database, dan halaman diarahkan kembali ke `index.php`.

## Langkah 9 – `ubah.php` (Update)
Data lama diambil berdasarkan `id` dari URL (`$_GET['id']`) dan ditampilkan di form. Saat disimpan, dijalankan `UPDATE ... WHERE id_barang = ...`. Gambar hanya diganti jika ada file baru yang diunggah. `id` dikirim lewat `input type="hidden"`.

## Langkah 10 – `hapus.php` (Delete)
Menjalankan `DELETE FROM data_barang WHERE id_barang = ...` berdasarkan `id` dari URL, lalu kembali ke `index.php`.

## Catatan Perbaikan dari Modul
| Masalah di modul | Perbaikan |
|---|---|
| Kepala kolom "Harga Jual" dan "Harga Beli" terbalik dengan isi data | Urutan header disamakan dengan isi (Beli lalu Jual) |
| Path gambar ganda (`gambar/gambar/...`) | Hanya nama file yang disimpan di DB |
| `enctype="multipart/formdata"` (typo) di `ubah.php` | Dibetulkan menjadi `multipart/form-data` |
| `is_select()` selalu membandingkan dengan `'Komputer'` | Dibandingkan dengan nilai option masing-masing |
| Query digabung string (rawan SQL Injection) | Memakai prepared statement (`mysqli_prepare`) |
| Teks tidak di-escape | Memakai `htmlspecialchars()` untuk mencegah XSS |
