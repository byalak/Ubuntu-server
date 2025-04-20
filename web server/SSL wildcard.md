# SSL/TLS Wildcard
SSL/TLS Wildcard adalah jenis sertifikat yang digunakan untuk mengamankan semua subdomain dari satu domain utama menggunakan satu sertifikat saja. Sertifikat wildcard menggunakan simbol bintang (*) untuk mencakup berbagai subdomain  ``` *.klandestin.site```. SSL/TLS wildcard sangat cocok digunakan jika kamu memiliki banyak subdomain yang berjalan di server yang sama atau berbeda, dan ingin menghindari pengelolaan sertifikat secara terpisah untuk masing-masing subdomain.
# Install certbot
```bash
apt update
apt install certbot -y
```
# Pembuatan sertifikat wildcard
## Jalankan perintah:
```bash
certbot certonly --manual --preferred-challenges dns -d "*.klandestin.site" -d klandestin.site
```
## Tambahkan DNS TXT Record:
Certbot akan memunculkan pesan:
```bash
Please deploy a DNS TXT record under the name
_acme-challenge.klandestin.site with the following value:

abc12345xyz890example...

Before continuing, verify the record is deployed.
```
## Masuk ke panel DNS penyedia domain
Tambahkan record TXT dengan:

Name: _acme-challenge.klandestin.site

Type: TXT

Value: abc12345xyz890example... (yang muncul di terminal)

 Tunggu beberapa menit agar DNS terpropagasi (cek dengan dnschecker.org).

 Setelah itu kembali keterminal dan enter
 ## Lokasi sertifikat
 ```bash
/etc/letsencrypt/live/klandestin.site/fullchain.pem

/etc/letsencrypt/live/klandestin.site/privkey.pem
```
## Konfigurasi VirtualHost
``` nano /etc/apache2/sites-available/login.klandestin.site.conf```
isi:
```bash
<VirtualHost *:80>
        ServerName login.klandestin.site
        Redirect permanent / https://login.klandestin.site/
</VirtualHost>



<VirtualHost *:443>
    ServerName login.klandestin.site
    DocumentRoot /var/www/login

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/klandestin.site/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/klandestin.site/privkey.pem

    <Directory /var/www/login>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/login_error.log
    CustomLog ${APACHE_LOG_DIR}/login_access.log combined
</VirtualHost>
```
Aktifkan site dan restart:
```bash
sudo a2ensite login.klandestin.site
sudo systemctl reload apache2
```
## Uji webserver
```https://login.klandestin.site```
![Screenshot 2025-04-20 203158](https://github.com/user-attachments/assets/514da174-92c1-4ddf-81c9-7f93f69e35f7)
