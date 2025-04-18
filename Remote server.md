# Remote server menggunakan SSH ( Secure Shell )
SSH (Secure Shell) adalah protokol jaringan yang memungkinkan kita untuk mengakses dan mengelola komputer/server lain secara aman melalui jaringan yang tidak aman, seperti internet. SSH memberikan akses ke shell (command line) dari komputer remote, seolah-olah kita sedang duduk langsung di depannya.
SSH dirancang dengan keamanan sebagai prioritas utama. Dibanding protokol lama seperti Telnet atau FTP, SSH mengenkripsi seluruh koneksi antara client dan server. Ini mencegah penyadapan (sniffing) atau manipulasi data oleh pihak ketiga.
## Install SSH
```bash
apt update
apt install openssh-server -y
```
cek status:
```bash
systemctl status ssh
```
Pastikan statusnya active (running). Jika belum aktifkan lebih dulu.
```bash
systemctl enable --now ssh
```
## IP Address Server
Gunakan perintah berikut di server untuk melihat IP address:
```bash
ip a
```
## Remote server dari komputer client
Di terminal komputer client, gunakan perintah:
```bash
ssh username@ipaddress

ssh dapit@192.168.1.14
```
Jika pertama kali, akan muncul prompt verifikasi fingerprint. Ketik yes dan lanjutkan. Lalu masukkan password user.
# Hardening SSH
## study case
Seorang sysadmin ingin mengamankan akses SSH ke server Ubuntu-nya. Ia mengubah port SSH menjadi 2025 agar tidak mudah dipindai, dan menonaktifkan login root untuk mencegah serangan langsung ke akun administrator. Seluruh user diwajibkan login menggunakan SSH key (key-based authentication) demi keamanan. Namun, karena ada seorang developer baru (user2) yang belum sempat mengatur SSH key, sysadmin membuat pengecualian: hanya untuk user2, login menggunakan password tetap diizinkan sementara. Selain itu, hanya dua user (user1 dan user2) yang diizinkan mengakses server melalui SSH, sehingga akses lebih terkontrol dan aman.
## Buat user baru
```bash
adduser user1
adduser user2
```
## edit file konfigurasi
```bash
nano /etc/ssh/sshd_config
```
## Pengaturan
```bash
Port 2025
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
```
Tambahkan dibaris paling bawah untuk mengijinkan user:
```bash
AllowUsers user1 user2
```
Mengecualikan pengaturan untuk user tertentu:
```bash
Match User user2
          PasswordAuthentication yes
```
## Ijinkan port 2025 untuk firewall
```bash
ufw allow 2025/tcp
ufw status
ufw enable
```
## Buat SSH Key
SSH key authentication adalah metode login yang lebih aman dibandingkan password biasa. Di bagian ini, kita akan membuat SSH key di komputer client dan menghubungkannya ke server agar bisa login tanpa password.

Buka terminal baru dikomputer client, dan jalankan perintah di terminal:
```bash
ssh-keygen
```
output:
```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/home/username/.ssh/id_rsa): [ENTER]
Enter passphrase (empty for no passphrase): [ENTER]
Enter same passphrase again: [ENTER]
```
- Tekan ENTER untuk menyimpan di lokasi default (~/.ssh/id_rsa).
- Boleh kosongkan passphrase jika tidak ingin diminta setiap login (tidak disarankan untuk produksi).

Setelah selesai, akan terbentuk dua file di direktori ~/.ssh/:
```bash
id_rsa → private key (jangan dibagikan kepada siapapun)
id_rsa.pub → public key (dibagikan ke server)
```
Salin public key ke server, gunakan perintah:
```bash
ssh-copy-id user@ip-server

ssh-copy-id dapit@192.168.1.14
```
Jika berhasil, kamu akan melihat pesan:
```bash
Number of key(s) added: 1
Now try logging into the machine...
```
Restart SSH:
```bash
systemctl restart ssh
```
Login tanpa password (SSH Key):
```bash
ssh user1@192.168.1.14 -p 2025
```
Login dengan password:
```bash
ssh user2@192.168.1.14 -p 2025
```

