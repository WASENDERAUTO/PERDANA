# PERDANA
Membangun proyek backend otentikasi berbasis token dengan Node.js adalah langkah yang umum dilakukan. Di bawah ini adalah panduan langkah demi langkah untuk mengimplementasikan otentikasi berbasis token dalam proyek backend Node.js.

## Langkah 1: Menyiapkan Lingkungan

1. Pastikan Anda telah menginstal Node.js dan npm (Node Package Manager). Anda dapat mengunduhnya dari situs web resmi: https://nodejs.org/

2. Buat direktori baru untuk proyek Anda dan masuk ke dalamnya melalui terminal:
```sh
bash
Copy code
mkdir node-authentication-backend
cd node-authentication-backend
```
3. Inisialisasi proyek Node.js baru:
```sh
bash
Copy code
npm init
```
Ikuti petunjuk untuk membuat file package.json.

## Langkah 2: Instalasi Dependensi yang Dibutuhkan

Anda akan membutuhkan beberapa package untuk mengimplementasikan otentikasi berbasis token. Berikut beberapa package umum yang diperlukan:
```sh
bash
Copy code
npm install express mongoose bcrypt jsonwebtoken
```
express: Framework aplikasi web untuk membangun API.
mongoose: Library Object Data Modeling (ODM) untuk MongoDB.
bcrypt: Library untuk meng-hash dan menyalt password.
jsonwebtoken: Library untuk membuat dan memverifikasi JSON Web Tokens (JWT).

## Langkah 3: Mengatur Basis Data MongoDB

Anda memerlukan basis data MongoDB untuk menyimpan informasi pengguna. Anda dapat membuat akun MongoDB Atlas gratis dan mengatur basis data untuk proyek Anda.

## Langkah 4: Membuat Aplikasi Express

Buat aplikasi Express dasar dalam sebuah file bernama app.js:
```sh
javascript
Copy code
// app.js
const express = require('express');
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const app = express();

// Terhubung ke MongoDB (perbarui string koneksi)
mongoose.connect('mongodb://localhost:27017/nama-database-anda', { useNewUrlParser: true, useUnifiedTopology: true });

// Tentukan skema dan model Pengguna (menggunakan Mongoose)
const User = mongoose.model('User', {
  username: String,
  password: String,
});

// Middleware untuk parsing JSON
app.use(express.json());

// Endpoint untuk registrasi pengguna
app.post('/register', async (req, res) => {
  const { username, password } = req.body;

  // Hash password sebelum menyimpannya ke basis data
  const hashedPassword = await bcrypt.hash(password, 10);

  const user = new User({ username, password: hashedPassword });

  try {
    await user.save();
    res.status(201).send('Pengguna telah dibuat');
  } catch (error) {
    res.status(500).send('Gagal mendaftarkan pengguna');
  }
});

// Endpoint untuk masuk
app.post('/login', async (req, res) => {
  const { username, password } = req.body;

  // Temukan pengguna berdasarkan username
  const user = await User.findOne({ username });

  if (!user) {
    return res.status(400).send('Username atau password salah');
  }

  // Periksa apakah password yang diberikan cocok dengan password yang di-hash dalam basis data
  if (await bcrypt.compare(password, user.password)) {
    // Buat JSON Web Token (JWT) untuk otentikasi
    const token = jwt.sign({ username: user.username }, 'kunci-rahasia-anda');
    res.json({ token });
  } else {
    res.status(400).send('Username atau password salah');
  }
});

// Mulai server
app.listen(3000, () => {
  console.log('Server berjalan di port 3000');
});
```
## Langkah 5: Memulai Server

Untuk memulai server Node.js Anda, jalankan:
```sh
bash
Copy code
node app.js
```
Server Anda sekarang akan berjalan di port 3000.

## Langkah 6: Pengujian

Anda dapat menggunakan alat seperti Postman atau curl untuk menguji endpoint /register dan /login. Setelah berhasil login, Anda akan menerima JSON Web Token (JWT) yang dapat Anda sertakan dalam header untuk rute yang dilindungi guna mengotentikasi pengguna.

Ini adalah implementasi dasar otentikasi berbasis token dalam proyek backend Node.js. Di lingkungan produksi, Anda harus mempertimbangkan peningkatan keamanan, manajemen pengguna, dan penggunaan variabel lingkungan (environment variables) untuk menyimpan informasi sensitif seperti kunci rahasia JWT.
