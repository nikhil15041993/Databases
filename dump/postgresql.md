### GRANT privilages

```
GRANT ALL PRIVILEGES ON DATABASE myproject TO nikhil;
```

### iF we need superuser permision

Change the permission to the user: login as sudo user by using following cmd

```
sudo -u postgres psql
```
Alter the user role

```
alter role <user-name> superuser;
```

### create a backup file

```
pg_dump --inserts --column-inserts --username=nikhil --host=localhost --port=5432 test > dbbackup.sql
```
for dump all databases use following command.
```
su - postgres -c pg_dumpall > /var/lib/postgresql/dumpdata.sql
```

Dump Your Django Database and Load It into a New Project

```
python manage.py dumpdata  > data_dump.json
```

Dump Your Django Database with specific table use following command

```
python manage.py dumpdata master_models.Skill > data_dump.json
```

First... check that you have the lines permissioning to the myuser user in pg_hba.conf. For example:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
Or any other lines of permission to IPV4 (and IPv6 if you use) with: TYPE DATABASE USER ADDRESS METHOD

```
After this check, run the psql as follows:

```
psql -h localhost -U myuser mydatabase
```

### restoring

```
psql -h localhost -U nikhil test1 < dbbackup.sql 
```

Granting privileges on the database mostly is used to grant or revoke connect privileges. This allows you to specify who may do stuff in the database if they have sufficient other permissions.

You want instead:

 ```
 GRANT ALL PRIVILEGES ON TABLE side_adzone TO jerry;
 ```
 
### backup to zip 

```
pg_dump -F c -f backup.tar.gz --inserts --column-inserts --username=nikhil --host=localhost test
```
