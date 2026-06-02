# Cara Mengatur Pengguna di Microsoft Copilot Studio

Catatan ini berisi langkah praktis untuk mengatur pengguna atau user di **Microsoft Copilot Studio**. Pengaturan user biasanya dilakukan melalui **role** dan **access control** pada environment di **Power Platform**.

---

## 1. Masuk ke Copilot Studio

1. Buka halaman Copilot Studio: <https://copilotstudio.microsoft.com>
2. Login menggunakan akun **Microsoft 365**.
3. Pilih **Environment** yang digunakan.

> **Catatan:** User diatur berdasarkan environment, jadi pastikan environment yang dipilih sudah sesuai.

---

## 2. Buka Pengaturan Akses

Untuk mengatur akses user:

1. Klik **Settings**.
2. Pilih **Users + permissions**.
3. Masuk ke salah satu menu berikut:
   - **Users**
   - **Security roles**

---

## 3. Tambahkan Pengguna

Langkah untuk menambahkan user baru:

1. Masuk ke menu **Users**.
2. Klik **+ Add user**.
3. Pilih user dari **Azure AD / Microsoft Entra ID**.
4. Assign user tersebut ke environment yang sesuai.

---

## 4. Assign Role ke Pengguna

Di Copilot Studio, akses user diatur melalui role Power Platform.

### Role Umum

| Role | Fungsi |
|---|---|
| **Environment Maker** | Bisa membuat dan mengedit Copilot atau bot |
| **System Administrator** | Memiliki akses penuh, termasuk pengaturan security |
| **Basic User** | Memiliki akses terbatas |
| **Copilot Author (preview)** | Fokus untuk membuat dan mengedit Copilot |

### Cara Assign Role

1. Masuk ke menu **Users**.
2. Klik nama user yang ingin diatur.
3. Klik **Manage roles**.
4. Checklist role yang ingin diberikan.
5. Klik **Save**.

---

## 5. Sharing Copilot

Selain assign role, Copilot juga perlu di-share agar user bisa mengakses Copilot yang dibuat.

Langkahnya:

1. Masuk ke Copilot yang sudah dibuat.
2. Klik **Share**.
3. Tambahkan akses untuk:
   - User tertentu
   - Security Group
4. Tentukan jenis akses:
   - **View**
   - **Edit**

> **Penting:** Role environment saja belum tentu cukup. Copilot juga harus di-share ke user atau grup yang sesuai.

---

## 6. Publish dan Channel Access

Kalau user perlu mengakses Copilot yang sudah live, pastikan beberapa hal berikut:

- Copilot sudah di-**Publish**.
- Channel yang digunakan sudah aktif, misalnya:
  - Microsoft Teams
  - Web

### Khusus Microsoft Teams

Jika Copilot digunakan melalui Microsoft Teams, assign app ke user melalui **Teams Admin Center**.

---

## 7. Best Practice

Agar pengaturan akses lebih rapi dan mudah dikelola:

- Gunakan **Security Group** di Azure AD / Microsoft Entra ID.
- Hindari assign akses satu per satu secara manual jika user banyak.
- Pisahkan environment berdasarkan kebutuhan:
  - **Dev**
  - **Test**
  - **Production**
- Batasi role **System Administrator** hanya untuk IT atau pihak yang benar-benar membutuhkan akses penuh.

---

## Contoh Setup untuk Tim

Contoh pembagian role untuk kebutuhan tim:

| Role | Siapa |
|---|---|
| **Environment Maker** | CAM + Solution Architect |
| **Basic User** | Telesales |
| **System Admin** | IT / Manager |

---

## Summary Singkat

User di Copilot Studio diatur melalui **Power Platform environment**.

Langkah utama:

1. **Add user** ke environment.
2. **Assign role** sesuai kebutuhan.
3. **Share Copilot** ke user atau security group.
4. Pastikan Copilot sudah **Publish** jika akan digunakan secara live.
5. Aktifkan channel seperti **Teams** atau **Web** sesuai kebutuhan.

Role yang paling umum digunakan untuk membuat dan mengedit Copilot adalah **Environment Maker**.

---

## Checklist Cepat

- [ ] Sudah login ke Copilot Studio
- [ ] Environment sudah sesuai
- [ ] User sudah ditambahkan
- [ ] Role sudah di-assign
- [ ] Copilot sudah di-share
- [ ] Copilot sudah di-publish
- [ ] Channel akses sudah aktif
- [ ] Security Group sudah digunakan jika user banyak
