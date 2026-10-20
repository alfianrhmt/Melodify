# Melodify (Spotify Clone)

Aplikasi streaming musik mobile berbasis Flutter dengan backend REST API Node.js dan database PostgreSQL. Dibuat sebagai replika fitur inti Spotify untuk tugas pemrograman aplikasi bergerak.

---

## Anggota Kelompok

| No | Nama | NIM | Peran / Pembagian Tugas |
|:--:|------|:---:|-------------------------|
| 1  | Mohammad Alfian Rahmatullah | 24051214153 | Struktur & Fondasi Awal Aplikasi |
| 2  | Muhammad Nashrul Aziz | 24051214159 | Desain Logo, Home Screen, Player Card |
| 3  | Najwa Fatimah Azzahra | 24051214158 | Halaman Search & Playlist |
| 4  | Audi Pratiwi Naura | 24051214145 | Halaman Auth (Login), Profile & Edit Profile |
| 5  | Michal Rivaldo Fresca | 24051214167 | Backend API & Database |

---

## Tech Stack

- **Frontend (Mobile):** Flutter (Dart), Provider (State Management), audioplayers, shared_preferences, http
- **Backend (API):** Node.js, Express.js
- **Database:** PostgreSQL
- **Autentikasi:** JWT (JSON Web Token) & bcryptjs

---

## Fitur Utama

- **Autentikasi & Akun:** Register, login, dan simpan sesi login otomatis via token JWT.
- **Profil User:** Lihat info akun serta edit profil (nama, username, bio, dan URL avatar).
- **Home & Beranda:** Daftar lagu terpopuler, dan album.
- **Pencarian:** Cari lagu berdasarkan judul atau nama artis.
- **Pemutar Musik (Audio Player):**
  - Kontrol play, pause, seekbar durasi, previous, dan next track.
  - Antrean putar lagu otomatis (queue).
  - Mini player persisten di bagian bawah layar saat navigasi menu.
  - Streaming audio lancar via endpoint proxy backend (bebas batasan CORS).
- **Koleksi & Playlist (Library):**
  - Like & unlike lagu favorit (halaman Liked Songs).
  - Buat, edit nama/deskripsi, dan hapus playlist.
  - Tambah dan hapus lagu dari dalam playlist.

---

## Struktur Folder

```text
Melodify/
├── melodify/                  # Aplikasi Flutter
│   └── lib/
│       ├── constants.dart     # Pengaturan URL Backend API
│       ├── main.dart          # Entry point Flutter
│       ├── models/            # Model data (Song, Playlist, User, Artist)
│       ├── providers/         # State management (Auth, Audio, Playlist)
│       ├── screens/           # Halaman UI (Auth, Home, Player, Library, Profile)
│       ├── services/          # Pemanggilan HTTP API ke backend
│       └── widgets/           # Komponen UI reusable (MiniPlayer, SongTile, dll)
│
└── melodify-backend/          # REST API Node.js
    ├── database.sql           # Schema tabel & seed data PostgreSQL
    ├── db.js                  # Koneksi pg pool database
    ├── index.js               # Entry point Express server
    ├── add-song.js            # Skrip CLI untuk tambah lagu cepat
    ├── middleware/            # Middleware verifikasi token JWT
    └── routes/                # Router endpoint (auth, songs, playlists, artists)
```

---

## Cara Menjalankan

### 1. Persiapan Database (PostgreSQL)

1. Pastikan PostgreSQL sudah terpasang dan berjalan di perangkat.
2. Buat database baru bernama `melodify_db`:
   ```sql
   CREATE DATABASE melodify_db;
   ```
3. Eksekusi file `database.sql` untuk membuat tabel dan data awal:
   ```bash
   psql -U postgres -d melodify_db -f melodify-backend/database.sql
   ```
   *(Atau buka dan jalankan isi file `database.sql` menggunakan pgAdmin / DBeaver).*

---

### 2. Menjalankan Backend (Node.js)

1. Masuk ke folder backend:
   ```bash
   cd melodify-backend
   ```
2. Buat file `.env` di dalam folder `melodify-backend` dan sesuaikan kredensial database kamu:
   ```env
   PORT=3000
   JWT_SECRET=melodify_jwt_super_secret_key_2026

   DB_HOST=localhost
   DB_PORT=5432
   DB_NAME=melodify_db
   DB_USER=postgres
   DB_PASSWORD=password_postgres_kamu
   ```
3. Install package dan jalankan server:
   ```bash
   npm install
   npm start
   ```
   Server default berjalan di `http://localhost:3000`.

> **Catatan:** Kamu bisa menambahkan lagu baru langsung lewat terminal menggunakan perintah:
> ```bash
> node add-song.js "Judul Lagu" "Nama Artis" "https://link-audio.mp3" "https://link-cover.jpg" 210
> ```

---

### 3. Menjalankan Frontend (Flutter)

1. Masuk ke folder Flutter:
   ```bash
   cd melodify
   ```
2. Atur URL API di `lib/constants.dart` sesuai target perangkat yang dipakai:
   - **Emulator Android:** `http://10.0.2.2:3000/api`
   - **HP Fisik (USB Debugging / Wi-Fi):** `http://IP_LAPTOP:3000/api` *(contoh: `http://192.168.1.10:3000/api`)*
   - **Chrome / Web / Windows:** `http://localhost:3000/api`
3. Unduh dependencies dan jalankan aplikasi:
   ```bash
   flutter pub get
   flutter run
   ```

---

## Daftar Endpoint API

### Autentikasi & Akun (`/api/auth`)
| Method | Endpoint | Deskripsi | Auth |
|:---:|---|---|:---:|
| POST | `/api/auth/register` | Pendaftaran akun user baru | Tidak |
| POST | `/api/auth/login` | Login dan menerima token JWT | Tidak |
| GET | `/api/auth/me` | Ambil data profil user yang sedang login | Ya |
| PUT | `/api/auth/profile` | Update profil (nama, bio, foto avatar) | Ya |

### Lagu (`/api/songs`)
| Method | Endpoint | Deskripsi | Auth |
|:---:|---|---|:---:|
| GET | `/api/songs` | Ambil seluruh daftar lagu | Tidak |
| POST | `/api/songs` | Tambah lagu baru | Tidak |
| GET | `/api/songs/search?q=` | Cari lagu / artis berdasarkan kata kunci | Tidak |
| GET | `/api/songs/liked/all` | Ambil semua lagu yang di-like user | Ya |
| GET | `/api/songs/:id` | Ambil detail satu lagu | Tidak |
| GET | `/api/songs/:id/stream` | Stream audio via proxy chunk backend | Tidak |
| POST | `/api/songs/:id/play` | Tambah total pemutaran lagu | Tidak |
| POST | `/api/songs/:id/like` | Like atau unlike lagu | Ya |

### Playlist (`/api/playlists`)
| Method | Endpoint | Deskripsi | Auth |
|:---:|---|---|:---:|
| GET | `/api/playlists` | Ambil daftar playlist milik user | Ya |
| POST | `/api/playlists` | Buat playlist baru | Ya |
| GET | `/api/playlists/:id` | Ambil detail playlist beserta daftar lagunya | Ya |
| PUT | `/api/playlists/:id` | Ubah nama atau deskripsi playlist | Ya |
| DELETE | `/api/playlists/:id` | Hapus playlist | Ya |
| POST | `/api/playlists/:id/songs` | Masukkan lagu ke playlist | Ya |
| DELETE | `/api/playlists/:id/songs/:songId` | Hapus lagu dari playlist | Ya |

### Artis & Album (`/api/artists`)
| Method | Endpoint | Deskripsi | Auth |
|:---:|---|---|:---:|
| GET | `/api/artists` | Ambil daftar seluruh artis | Tidak |
| GET | `/api/artists/albums/all` | Ambil seluruh daftar album | Tidak |
| GET | `/api/artists/:id` | Detail artis beserta daftar lagu karyanya | Tidak |
