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
Edit file konfigurasi mariadb:
```bash
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
Secara default, MariaDB hanya menerima koneksi dari localhost. Atur supaya bisa menerima IP lain Untuk remote access:
