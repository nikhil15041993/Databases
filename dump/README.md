## Backup MySQL Database

### How to Backup a Single MySQL Database?
To take a backup of a single database, use the command as follows. The command will dump the database [rsyslog] structure with data onto a single dump file called rsyslog.sql.

```
mysqldump -u root -ptecmint rsyslog > rsyslog.sql
```

### How to Backup Multiple MySQL Databases?
If you want to take backup of multiple databases, run the following command. The following example command takes a backup of databases [rsyslog, syslog] structure and data into a single file called rsyslog_syslog.sql.

```
mysqldump -u root -ptecmint --databases rsyslog syslog > rsyslog_syslog.sql
```

### How to Backup All MySQL Databases?
If you want to take a backup of all databases, then use the following command with the option –all-database. The following command takes the backup of all databases with their structure and data into a file called all-databases.sql.

```
mysqldump -u root -ptecmint --all-databases > all-databases.sql
```

### How to Backup a Single Table of Database?
With the below command you can take a backup of a single table or specific tables of your database. For example, the following command only takes a backup of the wp_posts table from the database wordpress.

```
mysqldump -u root -ptecmint wordpress wp_posts > wordpress_posts.sql
```

### How to Backup Multiple Tables of Database?
If you want to take a backup of multiple or certain tables from the database, then separate each table with space.

```
mysqldump -u root -ptecmint wordpress wp_posts wp_comments > wordpress_posts_comments.sql
```

### How to Backup Remote MySQL Database
The below command takes the backup of the remote server [172.16.25.126] database [gallery] into a local server.

```
mysqldump -h 172.16.25.126 -u root -ptecmint gallery > gallery.sql
```

## Restore MySQL Database

### How to Restore Single MySQL Database
To restore a database, you must create an empty database on the target machine and restore the database using msyql command. For example, the following command will restore the rsyslog.sql file to the rsyslog database.

```
mysql -u root -ptecmint rsyslog < rsyslog.sql
```

If you want to restore a database that already exists on the targeted machine, then you will need to use the mysqlimport command.

# mysqlimport -u root -ptecmint rsyslog < rsyslog.sql




