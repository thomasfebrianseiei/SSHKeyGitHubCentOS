
# **Panduan Membuat dan Menggunakan SSH Key untuk GitHub di CentOS**

## **1. Menghasilkan SSH Key Baru**
1. Buka terminal, jalankan perintah berikut:
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   Gantilah `your_email@example.com` dengan email yang terdaftar di akun GitHub Anda.

2. Ketika diminta lokasi penyimpanan file, tekan `Enter` untuk menggunakan lokasi default (`~/.ssh/id_ed25519`).

3. Jika diminta passphrase, masukkan passphrase yang aman atau tekan `Enter` untuk membiarkannya kosong.

---

## **2. Menambahkan SSH Key ke SSH Agent**
1. Mulai SSH Agent:
   ```bash
   eval "$(ssh-agent -s)"
   ```

2. Tambahkan SSH key ke agent:
   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```

---

## **3. Menyalin Public Key**
1. Salin isi public key untuk ditambahkan ke GitHub:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

2. Salin output dari perintah tersebut ke clipboard.

---

## **4. Menambahkan SSH Key ke GitHub**
1. Masuk ke akun GitHub Anda.
2. Pergi ke **Settings** > **SSH and GPG keys**.
3. Klik **New SSH key**.
4. Beri nama deskriptif untuk key Anda, misalnya `CentOS Server`.
5. Tempelkan public key yang telah disalin.
6. Klik **Add SSH key**.

---

## **5. Menguji Koneksi SSH ke GitHub**
Jalankan perintah berikut untuk memastikan SSH key berfungsi:
```bash
ssh -T git@github.com
```

Jika berhasil, Anda akan melihat pesan seperti:
```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## **6. Menggunakan SSH Key dengan `sudo git`**

### **Opsi 1: Menggunakan SSH Key Secara Eksplisit**
Gunakan perintah berikut ketika menjalankan `sudo git`:
```bash
sudo GIT_SSH_COMMAND='ssh -i ~/.ssh/id_ed25519' git <command>
```
Ganti `<command>` dengan operasi Git yang diinginkan, seperti `clone`, `pull`, atau `push`.

### **Opsi 2: Menggunakan SSH Agent Forwarding**
1. Pastikan SSH Agent aktif:
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

2. Jalankan shell dengan SSH Agent forwarding:
   ```bash
   sudo -E ssh-agent bash
   ```

3. Jalankan perintah `git` dengan `sudo`:
   ```bash
   sudo git clone git@github.com:<username>/<repository>.git
   ```

### **Opsi 3: Menyalin SSH Key ke Root User**
1. Masuk sebagai root:
   ```bash
   sudo -i
   ```

2. Buat direktori `.ssh` jika belum ada:
   ```bash
   mkdir -p /root/.ssh
   chmod 700 /root/.ssh
   ```

3. Salin SSH key ke direktori root:
   ```bash
   cp ~/.ssh/id_ed25519 /root/.ssh/
   cp ~/.ssh/id_ed25519.pub /root/.ssh/
   chmod 600 /root/.ssh/id_ed25519
   ```

4. Tambahkan public key ke `authorized_keys` root (opsional untuk akses jarak jauh):
   ```bash
   cat /root/.ssh/id_ed25519.pub >> /root/.ssh/authorized_keys
   chmod 600 /root/.ssh/authorized_keys
   ```

5. Uji perintah `sudo git`:
   ```bash
   sudo git clone git@github.com:<username>/<repository>.git
   ```

---

## **7. Troubleshooting**
1. **Permission Error**:
   Pastikan file private key memiliki izin yang benar:
   ```bash
   chmod 600 ~/.ssh/id_ed25519
   ```

2. **Koneksi Gagal**:
   Pastikan port `22` atau `443` terbuka di firewall.

---

## **Penutup**
SSH key kini dapat digunakan untuk autentikasi dengan GitHub, baik dalam operasi biasa maupun dengan `sudo git`. Pastikan semua langkah telah diuji sebelum digunakan dalam produksi.
