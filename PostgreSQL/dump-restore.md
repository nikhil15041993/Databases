Sometimes you need to quickly dump and restore a PostgreSQL database  to Docker container.

### Dump using pg_dump
```
docker exec -i pg_container_name /bin/bash -c "PGPASSWORD=pg_password pg_dump --username pg_username database_name" > /desired/path/on/your/machine/dump.sql
```

### Restore using psql
Since you are not able to provide a password directly through arguments, we rely on the PGPASSWORD environment variable:
```
docker exec -i pg_container_name /bin/bash -c "PGPASSWORD=pg_password psql --username pg_username database_name" < /path/on/your/machine/dump.sql
```

If you are attempting to restore data from a custom format dump, you should instead use pg_restore

```
docker exec -i postgres pg_restore --verbose --clean --no-acl --no-owner -h localhost -U postgres -d <your-db-name> < ./latest.dump
```
