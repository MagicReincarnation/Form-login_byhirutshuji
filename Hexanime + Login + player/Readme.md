# Hexanime

[Hexanime](https://emissionhex.blogspot.com/2024/08/hexanime-v1-free.html?m=1#c2757858608377440120) adalah template streaming anime untuk platform Blogger. Versi ini menyediakan sistem login yang terintegrasi dengan Firebase dan Player video khusus.

## Fitur

### Login
Sistem login yang terintegrasi dengan Hexanime. users perlu mendaftar dan masuk untuk mengakses episode premium.

### Akses Episode Premium
Hanya users yang sudah login yang dapat mengakses episode premium. Ini memberikan pengalaman streaming yang lebih eksklusif.

## Cara Setup

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
- Klik pada **"Cadangkan/Pulihkan"** dan unggah file tema Hexanime yang telah diunduh.
- **Atau cara manual**
Copy File tema dan pergi ke **Tema** dan pergi ke **Edit HTML**
hapus dulu semua isinya dan paste file tema yang sudah dicopy sebelumnya.
- Setelah itu, pergi ke folder `pages`
   dan buat halaman baru dengan kode yang ada difolder tersebut untuk masing-masing kodenya.

### 3. pasang Config Sdk Firebase
masuk kebagian layout blogger dan cari bagian **Config Sdk** ganti dengan config dengan punyamu.

## Cara pakai playerClone
  pasang role pada episode yang mau dikunci.
  contoh: 
  ```json
  {
    'title': 'Skill sampah ternyata berguna',
    'episode': '1',
    'role': "Member",
    'files': [{
      0: 'Youtube',
      1: 'https://www.youtube.com/embed/0xNH5gMaa0U',
      2: 'sub'
    }, {
      0: 'Youtube',
      1: 'https://www.youtube.com/embed/N-QwYL6JWic',
      2: 'dub'
    }]
  }, 
  ```
  silahkan cek codepost agar lebih jelas.
  **Tersedia 3 role**: `Admin`, `Member`, `User`.
  
## Kontribusi 
 1. **[Wernayasa](https://emissionhex.blogspot.com/2024/08/hexanime-v1-free.html?m=1#c2757858608377440120)** (Developer tema hexanime)
 2. **Hirutshuji** (Login & playerClone hexanime)
 3. **Roka** (player Clone)
