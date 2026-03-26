# CASE02 - Petstore API Test

Proyek pengujian API untuk [Petstore Swagger](https://petstore.swagger.io/v2) menggunakan **Postman** dan **Newman**. Mencakup 15 skenario uji (8 positif, 7 negatif, dan 3 cleanup) dengan teknik chaining variabel antar request.

---

## Daftar Isi

- [Struktur Project](#struktur-project)
- [Prasyarat](#prasyarat)
- [Instalasi](#instalasi)
- [Cara Menjalankan via Postman](#cara-menjalankan-via-postman)
- [Cara Menjalankan via Newman](#cara-menjalankan-via-newman)
- [Test Cases](#test-cases)
- [Laporan Hasil Test](#laporan-hasil-test)

---

## Struktur Project

```
Petstore_swagger/
├── Petstore_CASE02.postman_collection.json   # Koleksi Postman (file utama)
├── README.md                                  # Dokumentasi project
└── newman/                                    # Folder output laporan HTML
    └── *.html                                 # Laporan hasil eksekusi Newman
```

---

## Prasyarat

Pastikan perangkat lunak berikut sudah terinstal sebelum menjalankan pengujian:

| Perangkat Lunak | Versi Minimum | Keterangan |
|----------------|---------------|------------|
| [Node.js](https://nodejs.org/) | v14+ | Diperlukan untuk Newman |
| [Newman](https://www.npmjs.com/package/newman) | v5+ | CLI runner untuk Postman |
| [Postman](https://www.postman.com/downloads/) | Terbaru | Opsional, untuk jalankan via GUI |
| newman-reporter-htmlextra | v1+ | Opsional, untuk laporan HTML |

Cek versi yang terinstal:

```bash
node -v
npm -v
newman -v
```

---

## Instalasi

### 1. Install Newman (CLI Runner)

```bash
npm install -g newman
```

### 2. Install Reporter HTML (Opsional, untuk laporan visual)

```bash
npm install -g newman-reporter-htmlextra
```

### 3. Clone atau Download Project

```bash
git clone <repository-url>
cd Petstore_swagger
```

---

## Cara Menjalankan via Postman

### Langkah 1 — Import Koleksi

1. Buka aplikasi **Postman**
2. Klik tombol **Import** (pojok kiri atas)
3. Pilih file `Petstore_CASE02.postman_collection.json`
4. Klik **Import**

### Langkah 2 — Jalankan Collection Runner

1. Di sidebar kiri, klik kanan pada koleksi **CASE02 - Petstore API Test**
2. Pilih **Run collection**
3. Pastikan semua request ter-centang
4. Klik tombol **Run CASE02 - Petstore API Test**
5. Lihat hasil di panel **Run Results**

### Langkah 3 — Lihat Hasil

- Centang hijau = test **PASSED**
- Silang merah = test **FAILED**
- Klik setiap request untuk melihat detail assertion

---

## Cara Menjalankan via Newman

### Jalankan Semua Test (Output Terminal)

```bash
newman run Petstore_CASE02.postman_collection.json
```

### Jalankan dengan Laporan HTML

```bash
newman run Petstore_CASE02.postman_collection.json \
  --reporters htmlextra \
  --reporter-htmlextra-export newman/report.html
```

### Jalankan dengan Laporan HTML + Output Terminal Bersamaan

```bash
newman run Petstore_CASE02.postman_collection.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export newman/report.html
```

### Jalankan dengan Laporan JUnit (untuk CI/CD)

```bash
newman run Petstore_CASE02.postman_collection.json \
  --reporters cli,junit \
  --reporter-junit-export newman/report.xml
```

### Jalankan Test Tertentu Saja (Filter Folder)

```bash
# Hanya Positive Test Cases
newman run Petstore_CASE02.postman_collection.json \
  --folder "Positive Test Cases"

# Hanya Negative Test Cases
newman run Petstore_CASE02.postman_collection.json \
  --folder "Negative Test Cases"

# Hanya Cleanup
newman run Petstore_CASE02.postman_collection.json \
  --folder "Cleanup"
```

### Jalankan dengan Timeout Custom (ms)

```bash
newman run Petstore_CASE02.postman_collection.json \
  --timeout-request 10000
```

> Buka file hasil laporan HTML di folder `newman/` menggunakan browser.

---

## Test Cases

### Base URL

```
https://petstore.swagger.io/v2
```

### Positive Test Cases (8 TC)

| ID | Nama | Method | Endpoint | Assertion Utama |
|----|------|--------|----------|-----------------|
| TC-P01 | Create New Pet | POST | `/pet` | Status 200, response memiliki `id` dan `name` |
| TC-P02 | Get Pet by ID | GET | `/pet/{petId}` | Status 200, `id` dan `name` sesuai |
| TC-P03 | Update Pet | PUT | `/pet` | Status 200, nama pet berubah |
| TC-P04 | Find Pet by Status | GET | `/pet/findByStatus?status=available` | Status 200, response berupa array |
| TC-P05 | Create Store Order | POST | `/store/order` | Status 200, response memiliki `id` |
| TC-P06 | Get Store Order by ID | GET | `/store/order/{orderId}` | Status 200, `id` sesuai |
| TC-P07 | Create User | POST | `/user` | Status 200 |
| TC-P08 | Get User by Username | GET | `/user/{username}` | Status 200, `username` sesuai |

### Negative Test Cases (7 TC)

| ID | Nama | Method | Endpoint | Expected Status |
|----|------|--------|----------|-----------------|
| TC-N01 | Get Pet Non-existent ID | GET | `/pet/999999999` | 404 - Pet not found |
| TC-N02 | Get Pet Invalid ID String | GET | `/pet/invalidId` | 404 atau 400 |
| TC-N03 | Find Pet Invalid Status | GET | `/pet/findByStatus?status=xyz` | 200 (array kosong) atau 400 |
| TC-N04 | Create Pet Empty Body | POST | `/pet` | 400 / 405 / 415 / 500 (bukan 200) |
| TC-N05 | Get Order Non-existent ID | GET | `/store/order/99999` | 404 - Order not found |
| TC-N06 | Get Non-existent User | GET | `/user/nonexistentuser123` | 404 - User not found |
| TC-N07 | Delete Pet Non-existent ID | DELETE | `/pet/999999999` | 404 |

### Cleanup (3 TC)

| ID | Nama | Method | Endpoint | Tujuan |
|----|------|--------|----------|--------|
| Clean-1 | Delete Created Pet | DELETE | `/pet/{petId}` | Hapus pet yang dibuat saat TC-P01 |
| Clean-2 | Delete Created Order | DELETE | `/store/order/{orderId}` | Hapus order yang dibuat saat TC-P05 |
| Clean-3 | Delete Created User | DELETE | `/user/{username}` | Hapus user yang dibuat saat TC-P07 |

### Chaining Variabel Antar Request

Koleksi ini menggunakan **Collection Variables** untuk meneruskan data antar request:

| Variabel | Di-set oleh | Digunakan oleh |
|----------|-------------|----------------|
| `petId` | TC-P01 | TC-P02, TC-P03, TC-N01, TC-N07, Clean-1 |
| `orderId` | TC-P05 | TC-P06, TC-N05, Clean-2 |
| `username` | TC-P07 | TC-P08, TC-N06, Clean-3 |

---

## Laporan Hasil Test

Laporan HTML hasil eksekusi tersimpan di folder `newman/`. Buka file `.html` di browser untuk melihat:

- Ringkasan jumlah test passed/failed
- Detail setiap request dan response
- Waktu eksekusi per request
- Pesan error jika ada assertion yang gagal

---

## Informasi Project

| Field | Detail |
|-------|--------|
| **Koleksi** | CASE02 - Petstore API Test |
| **Target API** | https://petstore.swagger.io/v2 |
| **Total TC** | 15 (8 positif + 7 negatif + 3 cleanup) |
| **File Utama** | `Petstore_CASE02.postman_collection.json` |
