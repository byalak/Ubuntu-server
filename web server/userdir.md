# UserDir
Dokumentasi ini membahas cara mengaktifkan dan mengelola fitur UserDir (User Directory) pada Apache Web Server di Ubuntu VPS, serta mengaitkannya dengan subdomain milik masing-masing user.

Fitur UserDir memungkinkan setiap user di sistem Linux memiliki direktori website pribadi.
## Install modul UserDir
```bash
apt update
apt install libapache2-mod-userdir -y
```
## Aktifkan dan restart apache
```bash
a2enmod userdir
systemctl restart apache2
```
## Buat user baru dan direktori public_html
user1:
```bash
adduser user1
su - user1
chmod 711 $HOME
mkdir public_html
chmod 755 public_html
cat << EOF > public_html/index.html
<h1>Welcome to $(whoami)'s site.</h1>
EOF
```
user2:
```bash
adduser user2
su - user2
chmod 711 $HOME
mkdir public_html
chmod 755 public_html
cat << EOF > public_html/index.html
<h1>Welcome to $(whoami)'s site.</h1>
EOF
```
## Uji coba melalui browser, akses:

http://domain_server_atau_ip_server/~user1

http://klandestin.site/~user1

http://202.10.36.17/~user1
![Screenshot 2025-04-19 155104](https://github.com/user-attachments/assets/31b30549-1c44-45ed-9e2f-35ea9915244c)

# Basic Authentication
## Aktifkan module apache
```bash
sudo a2enmod ssl rewrite headers auth_basic
sudo systemctl reload apache2
```
## Setup VirtualHost
Buat file konfigurasi
```bash
nano /etc/apache2/sites-available/user1.klandestin.site.conf
```
Isi:
```bash
<VirtualHost *:80>
    ServerName user1.klandestin.site
    DocumentRoot /home/user1/public_html

    <Directory /home/user1/public_html>
        Options -Indexes +FollowSymLinks
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/user1_error.log
    CustomLog ${APACHE_LOG_DIR}/user1_access.log combined
</VirtualHost>
```
Aktifkan dan reload
```bash
sudo a2ensite user1.klandestin.site.conf
sudo systemctl reload apache2
```
## Update DNS
Arahkan subdomain kamu ke IP VPS:
![Screenshot 2025-04-19 163619](https://github.com/user-attachments/assets/a30b0486-3562-483f-9d0c-793a5f5e9f0d)
## Pasang TLS/SSL dengan certbot
Jalankan perintah:
```bash
certbot --apache -d user1.klandestin.site
```
## Buat file .htpasswd
```bash
htpasswd -c -B /etc/apache2/.htpasswd user1
```
## Edit file konfigurasi HTTPS
```bash
nano /etc/apache2/sites-available/user1.klandestin.site-le-ssl.conf
```
Isi:
```bash
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName user1.klandestin.site
    DocumentRoot /home/user1/public_html

    <Directory /home/user1/public_html>
        Options -Indexes +FollowSymLinks
        AllowOverride All

        AuthType Basic
        AuthName "Restricted Area"
        AuthUserFile /etc/apache2/.htpasswd
        Require user user1
    </Directory>

        Header always set X-Content-Type-Options "nosniff"
        Header always set X-Frame-Options "SAMEORIGIN"
        Header always set X-XSS-Protection "1; mode=block"
        Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"

        ErrorLog ${APACHE_LOG_DIR}/user1_error.log
        CustomLog ${APACHE_LOG_DIR}/user1_access.log combined

SSLCertificateFile /etc/letsencrypt/live/user1.klandestin.site/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/user1.klandestin.site/privkey.pem
Include /etc/letsencrypt/options-ssl-apache.conf
</VirtualHost>
</IfModule>
```
Kemudian simpan dan reload:
```bash
systemctl reload apache2
```
## Uji Web Server
Akses di browser:

https://user1.klandestin.site

![Screenshot 2025-04-20 001736](https://github.com/user-attachments/assets/db33c65d-81e1-4047-b4f7-30acab44d4d5)
![Screenshot 2025-04-20 001749](https://github.com/user-attachments/assets/0c162673-4f15-4f3d-a5b3-a507a4eee974)

