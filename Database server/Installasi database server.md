Database Server adalah sebuah layanan yang menyediakan penyimpanan, pengelolaan, dan pengambilan data secara terstruktur untuk aplikasi atau sistem. Server ini melayani permintaan dari klien (seperti website atau aplikasi) untuk membaca, menulis, memperbarui, atau menghapus data.
# MariaDB
MariaDB adalah sistem manajemen basis data relasional (RDBMS) yang merupakan pengembangan dari MySQL. MariaDB dibuat oleh developer asli MySQL sebagai alternatif open-source yang tetap gratis dan komunitas-driven, setelah MySQL diakuisisi oleh Oracle.

## Install MariaDB
```bash
apt update
apt install mariadb-server -y
```
Aktifkan:
```bash
systemctl enable --now mariadb
```
## Konfigurasi keamanan
Jalankan skrip:
```bash
mysql-secure-installation
```
Ikuti panduan:

- Set root password

- Hapus user anonymous

- Disable remote root login

- Hapus database test
  
- Reload privileges
## Konfigurasi remote access
### Edit file konfigurasi mariadb:
```bash
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
#### Aktifkan port 3306
```bash
port                    = 3306
```
Hilangkan pagar ``` # ``` untuk mengaktifkan.
#### Secara default, MariaDB hanya menerima koneksi dari localhost. Atur supaya bisa menerima IP lain Untuk remote access.
ubah:
```bash
bind-address = 127.0.0.1
```

menjadi:
```bash
bind-address = 0.0.0.0
```
### Aktifkan SSL/TLS dengan SSL Wildcard
```bash
ssl-ca = /etc/letsencrypt/live/klandestin.site/chain.pem
ssl-cert = /etc/letsencrypt/live/klandestin.site/fullchain.pem
ssl-key = /etc/letsencrypt/live/klandestin.site/privkey.pem
```
Enkripsi koneksi client-server agar data tidak terbaca di jaringan.

simpan dan keluar.

### Login ke mariadb:
```bash
mariadb -u root -p
```
masukkan password root yang udah diset tadi.

#### Buat user dengan dengan batasan IP:
```bash
CREATE USER 'remote_user'@'192.168.1.10' IDENTIFIED BY 'PasswordKuat!';
```
hanya bisa login dengan IP ``` 192.168.1.10 ``` dari luar.

#### Pastikan root hanya bisa akses dari ``` localhost ``` :
```bash
UPDATE mysql.user SET Host='localhost' WHERE User='root';
FLUSH PRIVILEGES;
EXIT
```
## Konfigurasi firewall
#### Ijinkan IP ``` 192.168.1.10 ```:
```bash
sudo ufw allow from 192.168.1.10 to any port 3306 proto tcp
```
#### Blokir semua akses ke port 3306
```bash
sudo ufw deny 3306
```
## Restart MariaDB
```bash
systemctl restart mariadb
```
## Login dari Remote Client (Command Line)
```bash
mysql -u user -h ipserver -p

mysql remote_user -h 202.10.36.17 -p
```
masukkan password yang sudah dibuat.

## Monitoring
Edit file konfigurasi ``` /etc/mysql/mariadb.conf.d/50-server.cnf ``` :
Tambahkan dan ubah:
```bash
general_log_file       = /var/log/mysql/mysql.log
general_log            = 1
log_output = FILE
log_error = /var/log/mysql/error.log
```
Restart mariadb:
```bash
systemctl restart mariadb
```

