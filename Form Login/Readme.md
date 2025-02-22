# Tutorial 
### how to install on zeistmanga v6 : [Link](https://youtu.be/EcQDbNEB-H4?si=YHWcilWgtvnBJZeY)

# Version 4.0

## Apa yang baru?

- **lock pakai database.**
- **tidak lagi popup.**
- **Profile header.**

## Apa yang dihapus?

- **lock encrypt**
- **Role Staff, Modder**
- **form login popup**
- **encrypt label**

---

### Catatan:

Kode ini direlease agar ada tujuan untuk pengembangan versi premium, sekaligus sebagai test kode premium yang perlu disempurnakan.

---

## Config SDK Firebase

Letakan dibawah tag `<head>`  
Jangan lupa pasang config SDK milik kamu didalam kode ini

```html
<script src='https://www.gstatic.com/firebasejs/8.6.8/firebase-app.js'/>
<script src='https://www.gstatic.com/firebasejs/8.6.8/firebase-auth.js'/>
<script src='https://www.gstatic.com/firebasejs/8.6.8/firebase-firestore.js'/>
<script src='https://www.gstatic.com/firebasejs/8.6.8/firebase-database.js'/>
<script src='https://www.gstatic.com/firebasejs/8.6.8/firebase-storage.js'/>
<script>
  /*<![CDATA[*/
  const config2 = {
     config sdk kamu  disini 
     
  };
  firebase.initializeApp(config2);
  const dbrl = firebase.database();
  const auth = firebase.auth();
  const storagedb = firebase.storage();
  const db = firebase.firestore();
  /*]]>*/
</script>
```

---

# Rule Firebase

# Firestore
```txt
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
# Realtime Database (Update: 17/08/2024 4:00PM)

**Change Log:**
- Add Rule Kontent
- Fixed Rule Series

```txt
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "auth != null && auth.uid === $uid",
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
      ".write": "auth != null"
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
# Storage
```text
service firebase.storage {
  match /b/{bucket}/o {
    match /profile_photos/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```
---

# Profile Header

Letakan di header
 - [File profile header](https://github.com/MagicReincarnation/Form-login_byhirutshuji/tree/main/Form%20Login%2FProfile%20Header)


---

# Login - Register - Profile - Change Password

Keempat kode ini harap letakan di halaman blog kalian, bebas kasih nama apa.

1. Login
   [File Login](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FPages%2FLogin.xml)

2. Register
   [File Register](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FPages%2FRegister.xml)

3. Profile
  [File Profile](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FPages%2FProfile.xml)

4. Change Password
  [File Change Password](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FPages%2FChange%20password.xml)

---

# Lock Kontent

Kode ini buat mengunci kontent kalian, bagian JavaScript harap diletakan di atas `</body>`.

Dan untuk bagian HTML, silahkan letakan dipost lalu ganti `idpost` dan `roleKT` dengan id post dan role kontent yang kamu buat di dashboard.

2. Letakan dipostingan Chapter:
```html
<div idpost="Id post disini" roleKT="Role disini" class="premiumK"></div>
```
1. Letakan di atas `</body>`
[File mainScript](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FMainScript%2FmainScript.min.cdn.xml)

---

# Dasboard Kontent

Letakan juga di halaman blog, tapi ingat jangan sampai ketahuan orang lain

 [File Dasboard Content](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FPages%2Fdasboard%20kontent.xml)

# Update (4.0): Dasboard Admin, letakan di halaman
[File Dasboard Admin](https://github.com/MagicReincarnation/Form-login_byhirutshuji/blob/main/Form%20Login%2FPages%2FDasboard%20Admin.xml)


---

**Selesai**

> Semoga berhasil
Maaf jika kodenya tidak responsif atau style kurang bagus, aku membuat kode ini hanya pakai ponsel, jadi responsif hanya diponsel, tidak di laptop.
Jika kalian bisa membuat stylenya sendiri dan mau membagikan stylenya, silahkan hubungi admin di group. Imbalannya, bisa kuberikan kode tanpa encrypt.

### How To post 
 How To post chapter premium : [Link](https://youtu.be/5wOTJdY3Pio?si=l4thItDiHndDtoFP)