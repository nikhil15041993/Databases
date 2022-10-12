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

```
mysqlimport -u root -ptecmint rsyslog < rsyslog.sql
```

## Backup a PostgreSQL

```
pg_dump -Fc -h 127.0.0.1 -U user vyi_prod -f  prod.dump

pg_restore -d <database> -h 127.0.0.1 -U user  dumpfile

```

PostgreSQL provides the pg_dump utility to help you back up databases. It generates a database file with SQL commands in a format that can be easily restored in the future.

To back up, a PostgreSQL database, start by logging into your database server, then switch to the Postgres user account, and run pg_dump as follows (replace tecmintdb with the name of the database you want to backup). By default, the output format is a plain-text SQL script file.

```
$ pg_dump tecmintdb > tecmintdb.sql
```

The pg_dump supports other output formats as well. You can specify the output format using the -F option, where c means custom format archive file, d means directory format archive, and t means tar format archive file: all formats are suitable for input into pg_restore.

For example:
```
$ pg_dump -F c tecmintdb > tecmintdb.dump
OR
$ pg_dump -F t tecmintdb > tecmintdb.tar
```
To dump output in the directory output format, use the -f flag (which is used to specify the output file) to specify the target directory instead of a file. The directory which will be created by pg_dump must not exist.

```
$ pg_dump -F d tecmintdb -f tecmintdumpdir	
```
To back up all PostgreSQL databases, use the pg_dumpall tool as shown.

```
$ pg_dumpall > all_pg_dbs.sql
```

### Restore a PostgreSQL database

To restore a PostgreSQL database, you can use the psql or pg_restore utilities. psql is used to restore text files created by pg_dump whereas pg_restore is used to restore a PostgreSQL database from an archive created by pg_dump in one of the non-plain-text formats (custom, tar, or directory).

Here is an example of how to restore a plain text file dump:

```
$ psql tecmintdb < tecmintdb.sql
```

As mentioned above, a custom-format dump is not a script for pgsql, so it must be restored with pg_restore as shown.

```
$ pg_restore -d tecmintdb tecmintdb.dump
OR
$ pg_restore -d tecmintdb tecmintdb.tar
OR
$ pg_restore -d tecmintdb tecmintdumpdir
```

### Backup Large PostgreSQL Databases
If the database you are backing up is large and you want to generate a fairly smaller output file, then you can run a compressed dump where you have to filter the output of pg_dump via a compression tool such as gzip or any of your favorite:

```
$ pg_dump tecmintdb | gzip > tecmintdb.gz
```
### Backup Remote PostgreSQL Databases

pg_dump is a regular PostgreSQL client tool, it supports operations on remote database servers. To specify the remote database server pg_dump should contact, use the command-line options -h to specify the remote host and -p specifies the remote port the database server is listening on. Besides, use the -U flag to specify the database role name to connect as.

Remember to replace 10.10.20.10 and 5432 and tecmintdb with your remote host IP address or hostname, database port, and database name respectively.

```
$ pg_dump -U tecmint -h 10.10.20.10 -p 5432 tecmintdb > tecmintdb.sql
```

it is also possible to dump a database directly from one server to another, use the pg_dump and psql utilities as shown.

```
$ pg_dump -U tecmint -h 10.10.20.10 tecmintdb | pqsl -U tecmint -h 10.10.20.30 tecmintdb
```

### Auto Backup PostgreSQL Database Using a Cron Job


You can configure a cron job to automate PostgreSQL database backup as follows. Note that you need to run the following commands as the PostgreSQL superuser:

```
$ mkdir -p /srv/backups/databases
```
Next, run the following command to edit the crontab to add a new cron job.

```
$ crontab -e
```
Copy and paste the following line at the end of the crontab. You can use any of the dump formats explained above.

```
0 0 * * *  pg_dump  -U postgres tecmintdb > /srv/backups/postgres/tecmintdb.sql
```
Save the file and exit.
