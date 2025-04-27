# Backup & Restore (ISSUE on Physical)
## Logical Backup & Restore
```
# Backup
[root@instance ~]# mariadb-dump db_wordpress > db_wordpress.sql

# Restore
[root@instance ~]# mariadb-dump db_wordpress < db_wordpress.sql
```

## Physical Backup & Restore
```
# Backup
[root@instance ~]# mariabackup --backup \
   --target-dir=/var/mariadb/backup/ \
   --user=root --password=asrul

# Restore
[root@instance ~]# mariabackup --prepare \
   --target-dir=/var/mariadb/backup/
[root@instance ~]# mariabackup --copy-back \
   --target-dir=/var/mariadb/backup
```
