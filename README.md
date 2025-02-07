# Soal Test

Gunakan **Laravel 11** dengan **PHP 8.2** sebagai framework, dengan ketentuan sebagai berikut:

## Persyaratan

- Menggunakan webserver bawaan Laravel:
  ```sh
  php artisan serve
  ```
- Validasi input harus menggunakan **Form Request**.
- Transaksi basis data menggunakan **Eloquent** dengan **MySQL/MariaDB**.
- Dokumentasi **REST API** harus dibuat menggunakan **Swagger**.

---

## Tugas 1: Membuat Migration untuk Tabel Database

Gunakan **Schema Builder** di Laravel untuk membuat migration dengan struktur tabel berikut:

### Customers

| Nama Kolom  | Tipe Data                    | Keterangan       | Aturan NULL |
| ----------- | ---------------------------- | ---------------- | ----------- |
| id_customer | Integer (PK, Auto Increment) | Primary Key      | NOT NULL    |
| name        | String (100)                 | Nama Pelanggan   | NOT NULL    |
| email       | String (100)                 | Email Pelanggan  | NOT NULL    |
| phone       | String (13)                  | Nomor Pelanggan  | NULL        |
| address     | Text                         | Alamat Pelanggan | NOT NULL    |

### Products

| Nama Kolom  | Tipe Data                    | Keterangan       | Aturan NULL           |
| ----------- | ---------------------------- | ---------------- | --------------------- |
| id_product  | Integer (PK, Auto Increment) | Primary Key      | NOT NULL              |
| name        | String (100)                 | Nama Produk      | NOT NULL              |
| description | Text                         | Deskripsi Produk | NULL                  |
| price       | BigInteger (Default 0)       | Harga            | NOT NULL (Default: 0) |
| stock       | Integer (4) (Default 0)      | Stok Produk      | NOT NULL (Default: 0) |

### Orders

| Nama Kolom  | Tipe Data                                  | Keterangan                       | Aturan NULL                 |
| ----------- | ------------------------------------------ | -------------------------------- | --------------------------- |
| id_orders   | Integer (PK, Auto Increment)               | Primary Key                      | NOT NULL                    |
| id_customer | Integer (Foreign Key ke customers)         | Pelanggan yang melakukan pesanan | NOT NULL                    |
| date        | Date                                       | Tanggal Pesanan                  | NOT NULL                    |
| status      | Enum (‘pending’, ‘completed’, ‘cancelled’) | Status Pesanan                   | NOT NULL (Default: pending) |

### Order_items

| Nama Kolom    | Tipe Data                         | Keterangan                   | Aturan NULL           |
| ------------- | --------------------------------- | ---------------------------- | --------------------- |
| id_order_item | Integer (PK, Auto Increment)      | Primary Key                  | NOT NULL              |
| id_order      | Integer (Foreign Key ke orders)   | Pesanan yang terkait         | NOT NULL              |
| id_product    | Integer (Foreign Key ke products) | Produk dalam pesanan         | NOT NULL              |
| quantity      | Integer (4) (Default: 0)          | Jumlah produk dalam pesanan  | NOT NULL (Default: 0) |
| price         | BigInteger (Default 0)            | Harga per item dalam pesanan | NOT NULL (Default: 0) |

---

## Tugas 2: Implementasi Relasi dengan Schema Builder

- **id_customer** di tabel **orders** harus memiliki **Foreign Key** yang merujuk ke tabel **customers**.
- **id_order** di tabel **order_items** harus memiliki **Foreign Key** yang merujuk ke tabel **orders**.
- **id_product** di tabel **order_items** harus memiliki **Foreign Key** yang merujuk ke tabel **products**.
- Tambahkan **Soft Delete** untuk tabel **customers**, **products**, dan **orders**.
- Pastikan semua kolom yang **NOT NULL** harus memiliki nilai saat melakukan insert data.

---

## Tugas 3: Buatlah CRUD REST API untuk Tabel Berikut

### Customers
| Nama Kolom  | Tipe Data         | Aturan Validasi |
|------------|-----------------|----------------|
| id_customer | Integer (PK, Auto Increment) | Auto Increment |
| name       | String (100)     | Wajib, minimal 3 karakter, maksimal 100 karakter |
| email      | String (100)     | Wajib, format email, unik dalam database |
| phone      | String (13)      | Opsional, jika diisi harus angka dan minimal 10 karakter |
| address    | Text             | Wajib, minimal 10 karakter |

### Products
| Nama Kolom  | Tipe Data         | Aturan Validasi |
|------------|-----------------|----------------|
| id_product | Integer (PK, Auto Increment) | Auto Increment |
| name       | String (100)     | Wajib, minimal 3 karakter, maksimal 100 karakter |
| description | Text            | Opsional |
| price      | BigInteger (Default 0) | Wajib, harus angka positif |
| stock      | Integer (4) (Default 0) | Wajib, minimal 0 |

### Orders
Gunakan format JSON berikut untuk menambah pesanan baru:
```json
{
  "id_customer": 1,
  "date": "2025-02-07",
  "status": "pending",
  "order_items": [
    {
      "id_product": 1,
      "quantity": 2,
      "price": 20000
    },
    {
      "id_product": 2,
      "quantity": 1,
      "price": 50000
    }
  ]
}
```

| Nama Kolom  | Tipe Data         | Aturan Validasi |
|------------|-----------------|----------------|
| id_customer | Integer (PK, Auto Increment) | Harus diisi dan merupakan ID pelanggan yang valid di tabel customers. |
| date       | Date            | Harus diisi dengan format tanggal |
| status     | String (Enum)   | Harus diisi dan hanya boleh bernilai "pending", "completed", atau "canceled". |
| order_items | Array           | Harus diisi dengan minimal satu item pesanan. |
| order_items.*.id_product | Integer | Harus diisi dan merupakan ID produk yang valid di tabel products. |
| order_items.*.quantity | Integer | Harus diisi, berupa bilangan bulat, dan minimal 1 unit. |
| order_items.*.price | Integer | Harus diisi, berupa bilangan bulat, dan tidak boleh negatif. |

---

## Tugas 4: Membuat REST API dengan Validasi

| Endpoint | Method | Deskripsi |
|----------|--------|-----------|
| `/api/customers` | GET | Mendapatkan semua pelanggan |
| `/api/customers` | POST | Menambahkan pelanggan baru |
| `/api/customers/{id}` | GET | Mendapatkan detail pelanggan berdasarkan ID |
| `/api/customers/{id}` | PUT | Mengupdate data pelanggan |
| `/api/customers/{id}` | DELETE | Menghapus pelanggan |
| `/api/products` | GET | Mendapatkan semua produk |
| `/api/products` | POST | Menambahkan produk baru |
| `/api/products/{id}` | GET | Mendapatkan detail produk berdasarkan ID |
| `/api/products/{id}` | PUT | Mengupdate data produk |
| `/api/products/{id}` | DELETE | Menghapus produk |
| `/api/orders` | GET | Mendapatkan semua orders |
| `/api/orders` | POST | Menambahkan pesanan baru |
| `/api/orders/{id}` | GET | Mendapatkan detail pesanan berdasarkan ID |
| `/api/orders/{id}` | DELETE | Menghapus pesanan |

### Format JSON Response
Setiap response API harus memiliki struktur berikut:
```json
{
  "status": "success", // atau "error"
  "message": "Informasi tentang hasil request",
  "data": { /* Data hasil request atau null jika tidak ada */ }
}
```

---

## Tugas 5: Dokumentasi API dengan Swagger
Gunakan **Swagger** (**L5 Swagger "darkaonline/l5-swagger"**) untuk mendokumentasikan semua endpoint API.

Dokumentasi harus mencakup:
- Contoh request dan response untuk setiap endpoint.
- Validasi input di dalam dokumentasi.

Pastikan dokumentasi lengkap dan dapat digunakan untuk testing API secara interaktif.
