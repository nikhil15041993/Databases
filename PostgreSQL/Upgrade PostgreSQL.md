## A Step-by-Step Guide
Make a backup. Make sure that your database is not being updated.


```
pg_dumpall > outputfile
```

## Install Postgres 

Then run ``` sudo apt-get install postgresql-<version> ```. A newer version will be installed side-by-side with the earlier version.


## Run pg_lsclusters:

Run pg_lsclusters command to view the current clusters.
you may get an output linkethis

```
 Ver Cluster Port Status Owner    Data directory               Log file
 9.6 main    5432 online postgres /var/lib/postgresql/9.6/main /var/log/postgresql/postgresql-9.6-main.log
 10  main    5433 online postgres /var/lib/postgresql/10/main  /var/log/postgresql/postgresql-10-main.log
 ```
 
 There already is a cluster main for 10 (since this is created by default on package installation). This is done so that a fresh installation works out of the box without the need to create a cluster first, but of course it clashes when you try to upgrade 9.6/main when 10/main also exists. The recommended procedure is to remove the 10 cluster with pg_dropcluster and then upgrade with pg_upgradecluster.

## Stop the 10 cluster and drop it

```
sudo pg_dropcluster 10 main --stop
```

## Stop all processes and services writing to the database. Stop the database:

 ```
 sudo systemctl stop postgresql 
 ```
 ## Upgrade the 9.6 cluster:

 ```
 sudo pg_upgradecluster -m upgrade 9.6 main
 ```
 by using pg_upgradecluster the all database and other configurations are copy to the new upgraded version cluster.
 
 
 ## Start PostgreSQL again

 ```
 sudo systemctl start postgresql
 ```
 
 ## Run pg_lsclusters . Your 9.6 cluster should now be "down", and the 10 cluster should be online at 5432:

```
Ver Cluster Port Status Owner    Data directory               Log file
9.6 main    5433 down   postgres /var/lib/postgresql/9.6/main /var/log/postgresql/postgresql-9.6-main.log
10  main    5432 online postgres /var/lib/postgresql/10/main  /var/log/postgresql/postgresql-10-main.log
```


# How to switch the active PostgreSQL clusters

Check with pg_lscluster what is running:

```
$ sudo pg_lsclusters

Ver Cluster Port Status Owner    Data directory               Log file
9.5 main    5433 online postgres /var/lib/postgresql/9.5/main /var/log/postgresql/postgresql-9.5-main.log
11  main    5432 online postgres /var/lib/postgresql/11/main  /var/log/postgresql/postgresql-11-main.log
```

And what is the active PostgreSQL version for command line commands, for example pg_dump.

```
$ pg_dump --version
pg_dump (PostgreSQL) 11.5 (Ubuntu 11.5-1.pgdg16.04+1)
```


We want to switch and make 9.5 version active (binding to port 5432) and make version 11 bind to port 5433. Stop the running versions and check if the servers are down.

```
$ sudo pg_ctlcluster 9.5 main stop

$ sudo pg_ctlcluster 11 main stop

$ pg_lsclusters

Ver Cluster Port Status Owner    Data directory               Log file
9.5 main    5433 down   postgres /var/lib/postgresql/9.5/main /var/log/postgresql/postgresql-9.5-main.log
11  main    5432 down   postgres /var/lib/postgresql/11/main  /var/log/postgresql/postgresql-11-main.log
```

Change the port in the configuration files and list the new config to be sure.

```
$ sudo mcedit /etc/postgresql/9.5/main/postgresql.conf
change port=5433 to port=5432 

$ sudo mcedit /etc/postgresql/11/main/postgresql.conf
change port=5432 to port 5433
```


Start the clusters and check what is running

```
$ sudo pg_ctlcluster 9.5 main start
$ sudo pg_ctlcluster 11 main start

$ pg_lsclusters

Ver Cluster Port Status Owner    Data directory               Log file
9.5 main    5432 online postgres /var/lib/postgresql/9.5/main /var/log/postgresql/postgresql-9.5-main.log
11  main    5433 online postgres /var/lib/postgresql/11/main  /var/log/postgresql/postgresql-11-main.log
```


Mission accomplished! Let's see what is the active PostgreSQL version for command line commands.

```
$ pg_dump --version
pg_dump (PostgreSQL) 9.5.19
```
