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

## Basic Authentication
Buat file auth untuk user1
```bash
htpasswd -c /etc/apache2/.htpasswd user1
```
Buat file auth untuk user2
```bash
htpasswd -c /etc/apache2/.htpasswd user2
```
## Konfigurasi VirtualHost dengan subdomain + Auth
Buat file: /etc/apache2/sites-available/user1.klandestin.site.conf
```bash
<VirtualHost *:80>
    ServerName user1.domain.com
    DocumentRoot /home/user1/public_html

    <Directory /home/user1/public_html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted

        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require user user1
    </Directory>
ErrorLog ${APACHE_LOG_DIR}/user1_error.log
CustomLog ${APACHE_LOG_DIR}/user1_access.log combined
</VirtualHost>
```
Buat file: /etc/apache2/sites-available/user2.klandestin.site.conf
```bash
<VirtualHost *:80>
    ServerName user2.domain.com
    DocumentRoot /home/user2/public_html

    <Directory /home/user1/public_html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted

        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require user user1
    </Directory>
ErrorLog ${APACHE_LOG_DIR}/user2_error.log
CustomLog ${APACHE_LOG_DIR}/user2_access.log combined
</VirtualHost>
```
## Enable Site dan Restart
```bash
sudo a2ensite user1.domain.com.conf
sudo a2ensite user2.domain.com.conf
sudo systemctl reload apache2
```
## Update DNS
Arahkan subdomain dipanel DNS kamu ke IP Server:
![Screenshot 2025-04-19 163619](https://github.com/user-attachments/assets/774e2419-1029-428e-90ec-00407b4eed8d)
Lakukan juga untuk user2

## Pasang TLS (HTTPS) dengan Let's Encrypt
```bash
certbot --apache
```
Ikuti instruksi dan pilih subdomain (user1.klandestin.site dan user2.klandestin.site)

