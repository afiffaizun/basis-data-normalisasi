# Nomer 1 :  Normalisasi Database Transaksi

---

## Daftar Isi

- [1NF – First Normal Form](#1nf--first-normal-form)
- [2NF – Second Normal Form](#2nf--second-normal-form)
- [3NF – Third Normal Form](#3nf--third-normal-form)
- [Relasi Antar Tabel](#relasi-antar-tabel)
- [Penjelasan Relasi](#penjelasan-relasi)

---

## 1NF – First Normal Form

Pada tahap ini semua atribut bersifat atomik (tidak ada nilai yang berulang atau multi-nilai). Semua data digabung dalam satu tabel dengan composite primary key.

**Tabel: Transaksi**

| Kolom | Keterangan |
|---|---|
| `no_faktur` | PK (bagian dari composite PK) |
| `kode_pelanggan` | PK (bagian dari composite PK) |
| `kode_barang` | PK (bagian dari composite PK) |
| `tanggal` | |
| `nama_pelanggan` | |
| `alamat` | |
| `nama_barang` | |
| `jumlah` | |
| `harga_jual` | |

> **Composite PK:** (`no_faktur`, `kode_pelanggan`, `kode_barang`)

**Masalah di 1NF:** Terdapat *partial dependency* — `nama_pelanggan` dan `alamat` hanya bergantung pada `kode_pelanggan` (bukan seluruh composite PK), begitu pula `nama_barang` dan `harga_jual` hanya bergantung pada `kode_barang`.

---

## 2NF – Second Normal Form

Partial dependency dihilangkan dengan memisahkan atribut yang hanya bergantung pada sebagian PK ke tabel tersendiri.

**Tabel: Pelanggan**

| Kolom | Keterangan |
|---|---|
| `kode_pelanggan` | PK |
| `nama_pelanggan` | |
| `alamat` | |

**Tabel: Barang**

| Kolom | Keterangan |
|---|---|
| `kode_barang` | PK |
| `nama_barang` | |
| `harga_jual` | |

**Tabel: Transaksi**

| Kolom | Keterangan |
|---|---|
| `no_faktur` | PK (bagian dari composite PK) |
| `kode_barang` | PK (bagian dari composite PK) |
| `tanggal` | |
| `kode_pelanggan` | FK → Pelanggan |
| `jumlah` | |

> **Composite PK:** (`no_faktur`, `kode_barang`)

**Masalah di 2NF:** Terdapat *transitive dependency* — `tanggal` dan `kode_pelanggan` hanya bergantung pada `no_faktur`, bukan pada keseluruhan composite PK (`no_faktur`, `kode_barang`).

---

## 3NF – Third Normal Form

Transitive dependency dihilangkan dengan memisahkan header transaksi dan detail barang ke tabel yang berbeda.

**Tabel: Pelanggan**

| Kolom | Keterangan |
|---|---|
| `kode_pelanggan` | PK |
| `nama_pelanggan` | |
| `alamat` | |

**Tabel: Barang**

| Kolom | Keterangan |
|---|---|
| `kode_barang` | PK |
| `nama_barang` | |
| `harga_jual` | |

**Tabel: Transaksi**

| Kolom | Keterangan |
|---|---|
| `no_faktur` | PK |
| `tanggal` | |
| `kode_pelanggan` | FK → Pelanggan |

**Tabel: Detail Transaksi**

| Kolom | Keterangan |
|---|---|
| `no_faktur` | PK, FK → Transaksi |
| `kode_barang` | PK, FK → Barang |
| `jumlah` | |

> **Composite PK:** (`no_faktur`, `kode_barang`)

---

## Relasi Antar Tabel

```
Pelanggan ──────────< Transaksi ──────────< Detail Transaksi >────────── Barang
(kode_pelanggan PK)   (no_faktur PK)        (no_faktur PK,FK)            (kode_barang PK)
                      (kode_pelanggan FK)    (kode_barang PK,FK)
```

| Relasi | Tipe | Keterangan |
|---|---|---|
| Pelanggan → Transaksi | One-to-Many (1:N) | Satu pelanggan bisa melakukan banyak transaksi |
| Transaksi → Detail Transaksi | One-to-Many (1:N) | Satu transaksi bisa memiliki banyak baris detail |
| Barang → Detail Transaksi | One-to-Many (1:N) | Satu barang bisa muncul di banyak detail transaksi |

---

## Penjelasan Relasi

### Pelanggan → Transaksi (1:N)
Satu pelanggan dapat melakukan banyak transaksi, namun setiap transaksi hanya dimiliki oleh satu pelanggan. Kolom `kode_pelanggan` di tabel **Transaksi** bertindak sebagai *foreign key* yang mereferensikan tabel **Pelanggan**.

### Transaksi → Detail Transaksi (1:N)
Satu faktur/transaksi dapat memiliki banyak baris detail barang. Kolom `no_faktur` di tabel **Detail Transaksi** bertindak sebagai *foreign key* yang mereferensikan tabel **Transaksi**.

### Barang → Detail Transaksi (1:N)
Satu barang dapat muncul di banyak detail transaksi yang berbeda. Kolom `kode_barang` di tabel **Detail Transaksi** bertindak sebagai *foreign key* yang mereferensikan tabel **Barang**.

### Catatan: PK, FK pada Detail Transaksi
Kolom `no_faktur` dan `kode_barang` di tabel **Detail Transaksi** memiliki **dua peran sekaligus**:
- **PK** – keduanya membentuk *composite primary key* yang menjadi identitas unik setiap baris.
- **FK** – keduanya juga mereferensikan tabel lain (Transaksi dan Barang) untuk menjaga integritas data.

Tabel **Detail Transaksi** berfungsi sebagai *junction table* yang menyelesaikan relasi many-to-many antara Transaksi dan Barang.

---

# Nomer 4 📚 Normalisasi Database — Studi Kasus Sistem Perpustakaan

## 📋 Daftar Isi

- [Gambaran Umum](#-gambaran-umum)
- [Tahap 1 — 1NF](#-tahap-1--bentuk-normal-pertama-1nf)
- [Tahap 2 — 2NF](#-tahap-2--bentuk-normal-kedua-2nf)
- [Tahap 3 — 3NF](#-tahap-3--bentuk-normal-ketiga-3nf)
- [Relasi Antar Tabel](#-relasi-antar-tabel)
- [Koreksi Diagram Asal](#-koreksi-diagram-asal)
- [Hasil Akhir](#-hasil-akhir)

---

## 🗂 Gambaran Umum

Normalisasi adalah proses menyusun struktur tabel database secara sistematis untuk:

- Mengurangi **redundansi data**
- Mencegah **anomali** saat insert, update, dan delete
- Memastikan setiap atribut bergantung pada Primary Key yang tepat

**Entitas utama dalam studi kasus ini:**

| Entitas | Deskripsi |
|---|---|
| Anggota | Data mahasiswa/anggota perpustakaan |
| Buku | Koleksi buku yang tersedia |
| Jenis Buku | Kategori/jenis buku |
| Peminjaman | Transaksi peminjaman buku |

---

## ① Tahap 1 — Bentuk Normal Pertama (1NF)

### Syarat 1NF
- Setiap sel hanya berisi **satu nilai (atomic)**
- Tidak ada grup berulang
- Harus memiliki **Primary Key**

### Kondisi Awal — Satu Tabel Flat

Semua atribut berada dalam satu tabel dengan **Composite Primary Key**:

```
PK: (no_anggota, no_buku, tanggal_pinjam)
```

| Atribut | Keterangan |
|---|---|
| `no_anggota` **(PK)** | Identitas anggota |
| `nim` | Nomor induk mahasiswa |
| `nama` | Nama anggota |
| `alamat` | Alamat anggota |
| `kota` | Kota anggota |
| `no_telepon` | Nomor telepon |
| `tanggal_lahir` | Tanggal lahir |
| `jurusan` | Jurusan anggota |
| `no_buku` **(PK)** | Identitas buku |
| `judul` | Judul buku |
| `pengarang` | Pengarang buku |
| `penerbit` | Penerbit buku |
| `tahun_terbit` | Tahun terbit |
| `jenis_buku` | Jenis/kategori buku |
| `status_buku` | Status ketersediaan buku |
| `tanggal_pinjam` **(PK)** | Tanggal peminjaman |
| `tgl_kembali` | Tanggal pengembalian |
| `status_pengembalian` | Status pengembalian |

### ⚠️ Masalah yang Ditemukan

> Banyak atribut hanya bergantung pada **sebagian** dari Composite PK, bukan pada keseluruhan PK. Ini disebut **Partial Dependency** dan harus dihilangkan di tahap 2NF.

---

## ② Tahap 2 — Bentuk Normal Kedua (2NF)

### Syarat 2NF
- Sudah memenuhi **1NF**
- Setiap atribut non-key harus bergantung **penuh** pada seluruh Primary Key (**Full Functional Dependency**)

### Identifikasi Partial Dependency

```
no_anggota  →  nim, nama, alamat, kota, no_telepon, tanggal_lahir, jurusan
no_buku     →  judul, pengarang, penerbit, tahun_terbit, jenis_buku, status_buku
(no_anggota, no_buku, tanggal_pinjam)  →  tgl_kembali, status_pengembalian
```

### Hasil Pemisahan — 3 Tabel

#### Tabel `Anggota`

| Kolom | Tipe | Keterangan |
|---|---|---|
| `no_anggota` | VARCHAR | **Primary Key** |
| `nim` | VARCHAR | Nomor induk mahasiswa |
| `nama` | VARCHAR | Nama lengkap |
| `alamat` | TEXT | Alamat |
| `kota` | VARCHAR | Kota |
| `no_telepon` | VARCHAR | Nomor telepon |
| `tanggal_lahir` | DATE | Tanggal lahir |
| `jurusan` | VARCHAR | Jurusan |

#### Tabel `Buku`

| Kolom | Tipe | Keterangan |
|---|---|---|
| `no_buku` | VARCHAR | **Primary Key** |
| `judul` | VARCHAR | Judul buku |
| `pengarang` | VARCHAR | Nama pengarang |
| `penerbit` | VARCHAR | Nama penerbit |
| `tahun_terbit` | YEAR | Tahun terbit |
| `jenis_buku` | VARCHAR | Jenis/kategori buku *(masih di sini di 2NF)* |
| `status_buku` | VARCHAR | Status ketersediaan |

#### Tabel `Peminjaman`

| Kolom | Tipe | Keterangan |
|---|---|---|
| `no_pinjam` | VARCHAR | **Primary Key** |
| `tgl_pinjam` | DATE | Tanggal pinjam |
| `tgl_kembali` | DATE | Tanggal kembali |
| `status_pengembalian` | VARCHAR | Status pengembalian |
| `no_anggota` | VARCHAR | **Foreign Key** → Anggota |
| `no_buku` | VARCHAR | **Foreign Key** → Buku |

---

## ③ Tahap 3 — Bentuk Normal Ketiga (3NF)

### Syarat 3NF
- Sudah memenuhi **2NF**
- Tidak ada atribut non-key yang bergantung pada atribut non-key lain (**Transitive Dependency**)

### Transitive Dependency yang Ditemukan

```
no_buku → kodejenis_buku → jenis_buku
```

Atribut `jenis_buku` tidak langsung bergantung pada `no_buku`, melainkan **melalui** `kodejenis_buku`. Ini adalah **transitive dependency** yang harus dihilangkan.

**Solusi:** Pisahkan `jenis_buku` ke tabel baru `Jenis_Buku`.

### Hasil Akhir — 4 Tabel

#### Tabel `Anggota` *(tidak berubah)*

| Kolom | Tipe | Keterangan |
|---|---|---|
| `no_anggota` | VARCHAR | **Primary Key** |
| `nim` | VARCHAR | Nomor induk mahasiswa |
| `nama` | VARCHAR | Nama lengkap |
| `alamat` | TEXT | Alamat |
| `kota` | VARCHAR | Kota |
| `no_telepon` | VARCHAR | Nomor telepon |
| `tanggal_lahir` | DATE | Tanggal lahir |
| `jurusan` | VARCHAR | Jurusan |

#### Tabel `Buku` *(diperbarui)*

| Kolom | Tipe | Keterangan |
|---|---|---|
| `no_buku` | VARCHAR | **Primary Key** |
| `judul` | VARCHAR | Judul buku |
| `pengarang` | VARCHAR | Nama pengarang |
| `penerbit` | VARCHAR | Nama penerbit |
| `tahun_terbit` | YEAR | Tahun terbit |
| `status_buku` | VARCHAR | Status ketersediaan |
| `kodejenis_buku` | VARCHAR | **Foreign Key** → Jenis_Buku |

#### Tabel `Jenis_Buku` *(tabel baru)*

| Kolom | Tipe | Keterangan |
|---|---|---|
| `kodejenis_buku` | VARCHAR | **Primary Key** |
| `jenis_buku` | VARCHAR | Nama jenis/kategori buku |

#### Tabel `Peminjaman` *(tidak berubah)*

| Kolom | Tipe | Keterangan |
|---|---|---|
| `no_pinjam` | VARCHAR | **Primary Key** |
| `tgl_pinjam` | DATE | Tanggal pinjam |
| `tgl_kembali` | DATE | Tanggal kembali |
| `status_pengembalian` | VARCHAR | Status pengembalian |
| `no_anggota` | VARCHAR | **Foreign Key** → Anggota |
| `no_buku` | VARCHAR | **Foreign Key** → Buku |

---

## 🔗 Relasi Antar Tabel

```
Anggota       ──── 1 : N ────▶  Peminjaman
Buku          ──── 1 : N ────▶  Peminjaman
Jenis_Buku    ──── 1 : N ────▶  Buku
```

| Relasi | Tipe | Foreign Key | Keterangan |
|---|---|---|---|
| Anggota → Peminjaman | `1 : N` | `no_anggota` | 1 anggota bisa memiliki banyak peminjaman |
| Buku → Peminjaman | `1 : N` | `no_buku` | 1 buku bisa dipinjam berkali-kali |
| Jenis_Buku → Buku | `1 : N` | `kodejenis_buku` | 1 jenis mencakup banyak buku |

> **Catatan:** Tabel `Peminjaman` berfungsi sebagai **junction table** yang memecah relasi Many-to-Many antara Anggota dan Buku menjadi dua relasi One-to-Many yang valid.

### Diagram Relasi (ERD Sederhana)

```
┌─────────────┐         ┌──────────────────┐         ┌──────────────┐
│   Anggota   │         │   Peminjaman     │         │     Buku     │
│─────────────│         │──────────────────│         │──────────────│
│ no_anggota  │◄───1:N──│ no_pinjam   (PK) │──N:1───►│ no_buku      │
│ nim         │   (PK)  │ tgl_pinjam       │   (PK)  │ judul        │
│ nama        │         │ tgl_kembali      │         │ pengarang    │
│ alamat      │         │ status_pngemb.   │         │ penerbit     │
│ kota        │         │ no_anggota  (FK) │         │ tahun_terbit │
│ no_telepon  │         │ no_buku     (FK) │         │ status_buku  │
│ tgl_lahir   │         └──────────────────┘         │ kodejenis(FK)│
│ jurusan     │                                       └──────┬───────┘
└─────────────┘                                              │ N:1
                                                             ▼
                                                    ┌──────────────────┐
                                                    │   Jenis_Buku     │
                                                    │──────────────────│
                                                    │ kodejenis_buku   │
                                                    │ jenis_buku       │
                                                    └──────────────────┘
```

---

## ✅ Hasil Akhir

| Tahap | Jumlah Tabel | Dependency Dihilangkan |
|---|---|---|
| **1NF** | 1 | — |
| **2NF** | 3 | Partial Dependency |
| **3NF** | 4 | Transitive Dependency |

**Tabel final dalam database:**

```
📁 database_perpustakaan/
├── 🟦 Anggota          (no_anggota PK)
├── 🟩 Buku             (no_buku PK, kodejenis_buku FK)
├── 🟨 Jenis_Buku       (kodejenis_buku PK)
└── 🟪 Peminjaman       (no_pinjam PK, no_anggota FK, no_buku FK)
```

---

## 📌 Referensi Konsep

| Istilah | Definisi |
|---|---|
| **Primary Key (PK)** | Atribut unik yang mengidentifikasi setiap baris dalam tabel |
| **Foreign Key (FK)** | Atribut yang merujuk ke Primary Key tabel lain |
| **Partial Dependency** | Atribut non-key bergantung hanya pada sebagian Composite PK |
| **Transitive Dependency** | Atribut non-key bergantung pada atribut non-key lain |
| **Full Functional Dependency** | Atribut non-key bergantung pada **seluruh** Primary Key |
| **Junction Table** | Tabel penghubung untuk memecah relasi Many-to-Many |

---

*Studi Kasus: Sistem Peminjaman Buku Perpustakaan · Normalisasi Database 1NF → 2NF → 3NF*
