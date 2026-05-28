# 🔥 Panduan Setup Firebase Backend

## Gambaran Arsitektur

```
Firebase
├── Authentication     → Login, Register, Reset Password
├── Cloud Firestore    → Database (users, materi, soal, leaderboard, dll)
└── Firebase Storage   → (opsional) foto profil
```

---

## Langkah 1 — Buat Project Firebase

1. Buka [console.firebase.google.com](https://console.firebase.google.com)
2. Klik **Add project** → beri nama (misal: `pjbl-backend`)
3. Aktifkan **Google Analytics** jika mau, lalu **Create project**

---

## Langkah 2 — Daftarkan Aplikasi Flutter (Android & iOS)

### Android
1. Di Firebase Console → **Project Settings** → **Add app** → pilih Android
2. Isi **Android package name**: `com.example.pjbl_backend`
3. Download `google-services.json`
4. Letakkan di `android/app/google-services.json`

### iOS
1. Tambah app iOS → isi **Bundle ID** dari `ios/Runner/Info.plist`
2. Download `GoogleService-Info.plist`
3. Letakkan di `ios/Runner/GoogleService-Info.plist`

---

## Langkah 3 — Install FlutterFire CLI & Konfigurasi

```bash
# Install FlutterFire CLI
dart pub global activate flutterfire_cli

# Konfigurasi otomatis (jalankan di root project Flutter)
flutterfire configure --project=<your-firebase-project-id>
```

Ini akan membuat file `lib/firebase_options.dart` secara otomatis.

---

## Langkah 4 — Tambah Dependensi ke pubspec.yaml

Salin isi `pubspec.yaml` yang sudah disediakan (sudah include semua dependency Firebase).

```bash
flutter pub get
```

---

## Langkah 5 — Aktifkan Firebase Services

### Authentication
1. Firebase Console → **Authentication** → **Get started**
2. Aktifkan **Email/Password**

### Cloud Firestore
1. Firebase Console → **Firestore Database** → **Create database**
2. Pilih mode **Production** (rules akan kita set manual)
3. Pilih region terdekat (misal: `asia-southeast2` untuk Jakarta)

---

## Langkah 6 — Deploy Security Rules & Indexes

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Init project (pilih Firestore)
firebase init firestore

# Deploy rules
firebase deploy --only firestore:rules

# Deploy indexes
firebase deploy --only firestore:indexes
```

Atau copy-paste isi `firestore.rules` dan `firestore.indexes.json` langsung dari Firebase Console.

---

## Langkah 7 — Seed Data Awal (Materi & Soal)

```bash
# Di folder firebase_backend
npm install firebase-admin

# Download serviceAccountKey.json dari:
# Firebase Console → Project Settings → Service accounts → Generate new private key
# Letakkan di folder yang sama dengan seed_firestore.js

node seed_firestore.js
```

---

## Langkah 8 — Update main.dart

Ganti `lib/main.dart` dengan versi yang sudah disediakan. File ini sudah mengandung inisialisasi Firebase dan injeksi services.

---

## Struktur Firestore

```
firestore/
├── users/{uid}
│   ├── name, email, photoUrl
│   ├── joinDate, totalScore
│   ├── totalGames, totalAchievements
│
├── kuis_results/{autoId}
│   ├── uid, userName, bahasa, islandName
│   ├── score, totalQuestions, correctAnswers
│   └── createdAt
│
├── history_materi/{uid_bahasa}
│   ├── uid, bahasa, name, description
│   ├── image, lastReadAt
│
├── leaderboard/{uid}
│   ├── uid, name, score, updatedAt
│
├── materi/{bahasa}
│   ├── bahasa, name, description, image, pulau
│   └── konten[] → { tipe, judul, teks/items }
│
├── soal/{autoId}
│   ├── bahasa, phrase
│   ├── options[], correctAnswer
│
└── tersimpan/{uid_bahasa}
    ├── uid, bahasa, name
    ├── description, image, savedAt
```

---

## Ringkasan File yang Diubah

| File | Perubahan |
|------|-----------|
| `pubspec.yaml` | Tambah Firebase & dependency |
| `lib/main.dart` | Init Firebase + inject services |
| `lib/app/services/auth_service.dart` | **BARU** - Firebase Auth wrapper |
| `lib/app/services/firestore_service.dart` | **BARU** - Semua operasi Firestore |
| `lib/app/services/firebase_service.dart` | **BARU** - Inisialisasi Firebase |
| `lib/app/models/*.dart` | **BARU** - UserModel, KuisResultModel, dll |
| `lib/app/modules/login/controllers/` | Firebase Auth login |
| `lib/app/modules/register/controllers/` | Firebase Auth register |
| `lib/app/modules/profile/controllers/` | Ambil profil dari Firestore |
| `lib/app/modules/isi_kuis/controllers/` | Soal dari Firestore, simpan hasil |
| `lib/app/modules/leaderboard/controllers/` | Real-time stream Firestore |
| `lib/app/modules/history_kuis/controllers/` | Riwayat kuis dari Firestore |
| `lib/app/modules/history_materi/controllers/` | Riwayat materi dari Firestore |
| `lib/app/modules/materi/controllers/` | Filter materi |
| `lib/app/modules/isi_materi/controllers/` | Tracking baca + simpan/unsimpan |
| `lib/app/modules/tersimpan/controllers/` | Materi tersimpan dari Firestore |
| `lib/app/modules/splashscreen/controllers/` | Cek status login |
| `lib/app/modules/setelah_kuis/controllers/` | Grade & navigasi |
| `firestore.rules` | **BARU** - Security rules |
| `firestore.indexes.json` | **BARU** - Composite indexes |
| `seed_firestore.js` | **BARU** - Script isi data awal |
