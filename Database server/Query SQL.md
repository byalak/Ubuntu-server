# Basic Command
## Login ke MariaDB
```
mariadb -u username -p
```

## Membuat Database
```bash
MariaDB [(none)]> CREATE DATABASE db_wordpress;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> CREATE DATABASE db_test;
Query OK, 1 row affected (0.001 sec)
```

## Menampilkan List Database
```bash
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| db_test            |
| db_wordpress       |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.001 sec)
```

## Memilih Database
```bash
MariaDB [(none)]> USE db_test
Database changed
MariaDB [db_test]> 
```

## Membuat Tabel
```bash
MariaDB [db_test]> CREATE TABLE list_users (
    -> id INT PRIMARY KEY AUTO_INCREMENT,
    -> username VARCHAR(255),
    -> uid VARCHAR(20),
    -> role VARCHAR(100)
    -> );
Query OK, 0 rows affected (0.005 sec)
```

## Menampilkan List Tabel
```bash
MariaDB [db_test]> show tables;
+-------------------+
| Tables_in_db_test |
+-------------------+
| list_users        |
+-------------------+
1 row in set (0.001 sec)
```

## Memasukkan Data
```bash
MariaDB [db_test]> INSERT INTO list_users (username, uid, role) VALUES
    -> ('Parto', '00001', 'Admin'),
    -> ('Tukiyem', '00002', 'Regular'),
    -> ('Dono', '00003', 'Admin'),
    -> ('Dina', '00004', 'Regular');
Query OK, 4 rows affected (0.004 sec)
Records: 4  Duplicates: 0  Warnings: 0
```

## Mengambil Data
```bash
MariaDB [db_test]> SELECT * FROM list_users;
+----+----------+-------+---------+
| id | username | uid   | role    |
+----+----------+-------+---------+
|  1 | Parto    | 00001 | Admin   |
|  2 | Tukiyem  | 00002 | Regular |
|  3 | Dono     | 00003 | Admin   |
|  4 | Dina     | 00004 | Regular |
+----+----------+-------+---------+
4 rows in set (0.001 sec)

MariaDB [db_test]> SELECT * FROM list_users WHERE role = 'Admin';
+----+----------+-------+-------+
| id | username | uid   | role  |
+----+----------+-------+-------+
|  1 | Parto    | 00001 | Admin |
|  3 | Dono     | 00003 | Admin |
+----+----------+-------+-------+
2 rows in set (0.001 sec)
```

## Mengupdate Data
```bash
MariaDB [db_test]> UPDATE list_users SET role = 'Admin' WHERE uid = '00004';
Query OK, 1 row affected (0.002 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [db_test]> select * from list_users;
+----+----------+-------+---------+
| id | username | uid   | role    |
+----+----------+-------+---------+
|  1 | Parto    | 00001 | Admin   |
|  2 | Tukiyem  | 00002 | Regular |
|  3 | Dono     | 00003 | Admin   |
|  4 | Dina     | 00004 | Admin   |
+----+----------+-------+---------+
4 rows in set (0.001 sec)
```

## Menghapus Data
```bash
MariaDB [db_test]> DELETE FROM list_users WHERE username = 'Dina';
Query OK, 1 row affected (0.002 sec)

MariaDB [db_test]> select * from list_users;
+----+----------+-------+---------+
| id | username | uid   | role    |
+----+----------+-------+---------+
|  1 | Parto    | 00001 | Admin   |
|  2 | Tukiyem  | 00002 | Regular |
|  3 | Dono     | 00003 | Admin   |
+----+----------+-------+---------+
3 rows in set (0.001 sec)
```

## Menghapus Tabel
```bash
MariaDB [db_test]> DROP TABLE list_users;
Query OK, 0 rows affected (0.004 sec)

MariaDB [db_test]> SHOW TABLES;
Empty set (0.001 sec)
```

## Menghapus Database
```bash
MariaDB [db_test]> DROP DATABASE db_test;
Query OK, 0 rows affected (0.003 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| db_wordpress       |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.001 sec)
```

## Membuat User Baru
```bash
MariaDB [(none)]> CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'resupw';
Query OK, 0 rows affected (0.003 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON db_wordpress.* TO 'wpuser'@'localhost';
Query OK, 0 rows affected (0.002 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.001 sec)

MariaDB [(none)]> SELECT User, Host FROM mysql.user;
+-------------+-----------+
| User        | Host      |
+-------------+-----------+
| mariadb.sys | localhost |
| mysql       | localhost |
| root        | localhost |
| wpuser      | localhost |
+-------------+-----------+
4 rows in set (0.002 sec)
```

## Menghapus User
```bash
MariaDB [(none)]> DROP USER 'wpuser'@'localhost';
```
