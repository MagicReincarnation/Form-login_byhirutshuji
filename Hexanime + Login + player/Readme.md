# Hexanime

[Hexanime](https://emissionhex.blogspot.com/2024/08/hexanime-v1-free.html?m=1#c2757858608377440120) adalah template streaming anime untuk platform Blogger. Versi ini menyediakan sistem login yang terintegrasi dengan Firebase dan opsi pemutar video khusus (pemutar defaultnya masih ada cukup pakai label `dayat`).

## Fitur

### Login
Sistem login yang terintegrasi dengan Hexanime. Pengguna perlu mendaftar dan masuk untuk mengakses fitur premium.

### StreamMiko
StreamMiko adalah pemutar video khusus untuk sistem login, yang mendukung berbagai jenis pemutaran video:

1. **Plyr** - Pemutar video yang modern dan responsif (rekomendasi).
2. **Iframe YouTube** - Memungkinkan pemutaran video dari YouTube.
3. **Iframe Normal** - Pemutaran video dari sumber lain dengan menggunakan iframe.
4. **HTML5** - Mendukung pemutaran video menggunakan elemen HTML5.

### Akses Server Premium
Hanya pengguna yang sudah login yang dapat mengakses server premium dan fitur download. Ini memberikan pengalaman streaming yang lebih baik dan eksklusif.

## Instalasi

### 1. Buat Akun Firebase
- Kunjungi [Firebase Console](https://console.firebase.google.com/).
- Klik **"Go to console"** dan masuk dengan akun Google Kamu.
- Klik **"Add project"** dan ikuti langkah-langkah untuk membuat proyek baru.
- Setelah proyek dibuat, buka menu **"Authentication"** dan pilih **"Get Started"**.
- Aktifkan **Email/Password** sebagai metode login dengan mengklik **"Sign-in method"**, lalu aktifkan toggle untuk **Email/Password**.

#### Atur Firestore, Realtime Database, dan Storage
- **Firestore Rules**:
  1. Buka **Firestore Database** di konsol Firebase.
  2. Pilih tab **"Rules"** dan atur aturan berikut:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Izinkan pencarian email
    match /users/{userId} {
      allow read: if request.auth == null || resource.data.email == request.auth.token.email;
      allow create, update, delete: if request.auth != null && request.auth.uid == userId;
      
      // Izinkan akses ke semua field dalam dokumen users setelah login
      match /users/{userId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }

  match /profiles_publik/{document=**} {
      allow read: if true;
      allow write: if request.auth != null && (get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role in ['User', 'Member', 'Staff', 'Modder']);
    }


    // Pengecualian untuk pengguna dengan role "Admin" agar mendapat akses ke semua data
    match /{document=**} {
      allow read, write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Admin';
    }
  }
}
```

- **Realtime Database Rules**:
  1. Buka **Realtime Database** di konsol Firebase.
  2. Pilih tab **"Rules"** dan atur aturan berikut:

 ```
  {
  "rules": {
    "users": {
      "$uid": {
        ".read": "true",
        ".write": "auth != null && auth.uid === $uid"
      }
    },    
   "riwayat_baca": {
      "$userId": {
        ".read": "$userId === auth.uid",
        ".write": "$userId === auth.uid",
        "$postId": {
          ".validate": "newData.hasChildren(['imgcover', 'title', 'url', 'initialtime','latesttimeread','count_read'])"
        },
        ".indexOn": ["initialtime"]
      }
    },
  "kontent": {
        ".read": "auth != null",
        ".write": "auth != null",
    },
  "series": {
        ".read": "auth != null || !data.child('linksPremium').exists()",
        ".write": "auth != null",
        "episodes": {
          "$episodeId": {
            ".read": "auth != null || !data.child('linksPremium').exists()",
            ".write": "auth != null"
          }
      }
    },    
    "$other": {
      ".read": "auth != null",
      ".write": "root.child('users/' + auth.uid + '/role').val() == 'Admin'"
    }
  }
}
  ```

- **Storage Rules**:
  1. Buka **Storage** di konsol Firebase.
  2. Pilih tab **"Rules"** dan atur aturan berikut:

```
service firebase.storage {
  match /b/{bucket}/o {
    match /profile_photos/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

### 2. Pasang Template di Blogger
- Masuk ke akun Blogger Kamu.
- Buka **"Tema"** di menu sebelah kiri.
- Klik pada **"Cadangkan/Pulihkan"** dan unggah file tema Hexanime yang telah Kamu unduh.
- **Atau cara manual**
Copy File tema dan pergi ke **Tema** dan pergi ke **Edit HTML**
hapus dulu semua isinya dan paste file tema yang kamu copy sebelumnya.
- Setelah itu, pergi ke folder
 - **"[Halaman Login](https://github.com/MagicReincarnation/Form-login_byhirutshuji/tree/main/Form%20Login/Pages)"**
 - **"[Halaman StreamMiko](https://github.com/MagicReincarnation/Form-login_byhirutshuji/tree/main/StreamMiko/pages)"**
dan buat halaman baru dengan kode yang ada difolder tersebut untuk masing-masing kodenya.


### 3. pasang Config Sdk Firebase
masuk kebagian layout blogger kamu dan cari bagian **Config Sdk** ganti isi config dengan punyamu.

## Penggunaan

- Masuk ke akun Kamu untuk mengakses konten premium.
- Pilih salah satu player yang tersedia untuk menikmati video anime.

## Kontribusi 
 1. **[Wernayasa](https://emissionhex.blogspot.com/2024/08/hexanime-v1-free.html?m=1#c2757858608377440120)** (Developer tema hexanime)
 2. **Hirutshuji** (Login & player StreamMiko)
 3. **Roka** (player StreamMiko)
