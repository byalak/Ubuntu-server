# Apache web server
Apache HTTP Server (biasa disebut Apache) adalah salah satu web server paling populer dan banyak digunakan di dunia. Apache memungkinkan server untuk menyajikan konten website ke pengguna melalui protokol HTTP atau HTTPS.

Dokumentasi ini menjelaskan langkah-langkah membangun web server sederhana menggunakan **Apache2** di **VPS Ubuntu**, lengkap dengan pengaturan domain publik dan HTTPS (Let's Encrypt).
## Install apache
```bash
apt update
apt install apache2 -y
```
Pastikan statusnya active (running). Jika belum, aktifkan lebih dulu:
```bash
systemctl enable --now apache2
```
## Uji web server
Buka browser dan akses IP VPS kamu:
![Screenshot 2025-04-18 161652](https://github.com/user-attachments/assets/e212fe49-28c3-41a9-aa95-d97cb1398d3d)
## Konfigurasi domain
Pastikan DNS A Record domain kamu mengarah ke IP VPS. Jika belum, pergi ke penyedia domain dan tambahkan record:
![A record](https://github.com/user-attachments/assets/243cd310-4be2-4490-bbe4-014abce53c17)
![www record](https://github.com/user-attachments/assets/5387cda0-8060-4c6e-a42b-a23c7c98b108)
## Setup Virtual Host untuk domain
Buat direktori website
```bash
mkdir -p /var/www/klandestin.site
chown -R dapit:dapit /var/www/klandestin.site
```
buat file konfigurasi Virtual Host:
```bash
nano /etc/apache2/sites-available/klandestin.site.conf
```
Isi dengan:
```bash
<VirtualHost *:80>
    ServerName klandestin.site
    ServerAlias www.klandestin.site
    DocumentRoot /var/www/klandestin.site
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Aktifkan Virtual Host:
```bash
a2ensite klandestin.site.conf
systemctl reload apache2
```
## Uji web server
Buka browser dan akses domain kamu:
![Screenshot 2025-04-18 161343](https://github.com/user-attachments/assets/d59829a3-09c2-40bf-b6d0-35afbda7ef83)
## Pasang HTTPS dengan Let's encrypt
Install certbot:
```bash
apt install certbot python3-certbot-apache -y
```
Jalankan:
```bash
certbot --apache -d klandestin.site -d www.klandestin.site
```
Ikuti instruksi dan pilih redirect ke HTTPS.

Tambahkan corn job untuk otomatis renew

Jalankan:
```bash
crontab -e
```
Tambahkan baris berikut untuk menjalankan perpanjangan otomatis 1 kali sehari:
```bash
0 3 * * * certbot renew --quiet --deploy-hook "systemctl reload apache2"
```
Penjelasan:

0 3 * * * → Cron akan berjalan pukul 03:00 setiap hari

--quiet → Menyembunyikan output kecuali ada error

--deploy-hook → Reload web server setelah sertifikat diperbarui
## Uji Web Server
![Screenshot 2025-04-19 134137](https://github.com/user-attachments/assets/6a8c85df-279f-49c0-9eef-29793d82c4c8)
