# UAS Keamanan Komputer

## Identitas
- Nama:Mochammad Akbar Kurniawan
- NIM: 221080200074
- Kelas: 6B1
- Repo GitHub: [https://github.com/akbarkurniawn/CI4-JWT]

---

## Bagian A – Bug Fixing JWT REST API

### Bug 1: Tidak ada pembatasan akses endpoint
**Penjelasan:**  
Semua user dapat mengakses endpoint manapun, termasuk yang seharusnya hanya untuk admin atau user tertentu. Contoh: User biasa bisa mengakses /api/users/delete/5 dan menghapus user lain.

**Solusi:**  
- Tambahkan filter AdminOnlyFilter untuk membatasi akses hanya pada role tertentu.
- Terapkan pengecekan role di JWT dan konfigurasikan pada Routes.php.


### Bug 2: User lain bisa mengakses data user lain
**Penjelasan:**  
Siapa pun yang memiliki token valid bisa mengakses dan mengubah data user lain hanya dengan mengubah id di URL atau body request.

**Solusi:**  
- Tambahkan pengecekan bahwa id dari JWT harus sama dengan id pada endpoint.


### Bug 3: Logout tidak menghapus token aktif
**Penjelasan:**  
Setelah logout, token lama masih bisa digunakan karena tidak ada mekanisme pembatalan token. logout hanya terjadi di sisi client, tapi token tetap bisa akses API di server.

**Solusi:**  
- Implementasikan sistem blacklist token saat logout.-
- Simpan token di file/database sementara (blacklist_tokens.json).
- Di filter AuthFilter, cek apakah token termasuk blacklist sebelum decode.

---

## Bagian B – Simulasi Serangan dan Solusi

### Jenis Serangan: Broken Access Control  
**Simulasi Postman:**  
Permintaan yang disalahgunakan

PUT /api/users/update/1

Authorization: Bearer <token_user_biasa>

Content-Type: application/json

{
  "username": "admin_hacked",
  "email": "admin@gmail.com"
}

**Hasil**: Jika endpoint tidak memverifikasi siapa pengaksesnya, maka data admin akan tergantikan.


**Solusi Implementasi:**  
Memastikan bahwa id yang ingin diubah harus sama dengan id dari token JWT.

if ($userData['id'] != $id) {
    return $this->failForbidden('You are not authorized to update this data');
}

---

## Bagian C – Refleksi Teori & Etika

### 1. CIA Triad dalam Keamanan Informasi  
Prinsip dasar keamanan informasi dikenal dengan CIA Triad, yaitu:
- Confidentiality (Kerahasiaan): Memastikan informasi hanya dapat diakses oleh pihak yang berwenang. Contohnya adalah penggunaan enkripsi dan otorisasi.
- Integrity (Integritas): Menjaga keutuhan dan konsistensi data, serta memastikan bahwa data tidak diubah tanpa izin. Misalnya penggunaan checksum.
- Availability (Ketersediaan): Menjamin sistem dan data dapat diakses oleh pengguna yang sah saat dibutuhkan. Misalnya melalui sistem backup.

### 2. UU ITE yang relevan  
- Pasal 30 ayat (1), (2), dan (3)
Melarang akses sistem elektronik milik orang lain tanpa hak.
Contoh pelanggaran: Mengakses database perusahaan tanpa izin.
- Pasal 32 ayat (1)
Melarang tindakan mengubah, menambah, mengurangi, atau menghilangkan informasi elektronik atau dokumen elektronik milik orang lain tanpa izin.
Contoh pelanggaran: Hacker menghapus isi file dokumen di server orang lain.


### 3. Pandangan Al-Qur'an  
- Surah Al-Baqarah: 205  
"Dan apabila ia berpaling (dari kamu), ia berjalan di muka bumi untuk membuat kerusakan di atasnya dan merusak tanam-tanaman dan binatang ternak, dan Allah tidak menyukai kerusakan."
(Al-Baqarah: 205)
- Tafsir dan Kaitannya
Ayat ini melarang segala bentuk perusakan di muka bumi. Dalam konteks dunia digital, ini mencakup tindakan merusak sistem komputer, menyebarkan malware, atau mencuri data. Etika Islam melarang cyber sabotage, hacking tanpa izin, dan pelanggaran privasi.


### 4. Etika Cyber dan Kejujuran  
- Kejujuran: Profesional cybersecurity harus jujur dalam pelaporan kerentanan, tidak menyembunyikan kelemahan sistem, dan tidak menyalahgunakan akses yang dimiliki.
- Amanah: Menjaga kerahasiaan informasi perusahaan dan pengguna merupakan bentuk amanah. Seorang tidak boleh menyalahgunakan hak akses tinggi untuk keuntungan pribadi.
- Dalam dunia modern, kejujuran dan amanah menjadi fondasi penting karena keamanan digital sangat bergantung pada integritas profesional yang mengelola sistem.

