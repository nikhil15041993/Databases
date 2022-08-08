## MySQL Replication Setup

So, here is our MySQL replication lab setup.

```
MySQL Master - 10.0.0.1
MySQL Slave - 10.0.0.2
```

## Step 1: Install MySQL on Master and Slave Server

```
 root@master:~# sudo apt-get update
 root@rmaster:~# sudo apt-get install mysql-server mysql-client -y
 root@master:~# sudo mysql_secure_installation
```

## Step 2: Secure MySQL on Master and Slave Server

The next step is to secure the MySQL database on both the master and slave servers. This is because the default settings are insecure and present some loopholes which can easily be exploited by hackers.

So, to harden MySQL, run the command:

```
$ sudo mysql_secure_installation
```

## Step 3: Configure the Master Node (Server)

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
Add the following lines under the [mysqld] section.

```
bind-address	 = 10.0.0.1
server-id 	 = 1
log_bin		 = mysql-bin
```

Restart MySQL service using the following command:

```
sudo service mysql restart
```

Next, log into MySQL shell.

```
$ sudo mysql -u root -p
```
Execute the following commands to create a database user that will be used to bind the master and slave for replication.
```
mysql> CREATE USER 'replica'@'10.0.0.2' IDENTIFIED BY 'P@ssword321';
mysql> GRANT REPLICATION SLAVE ON *.*TO 'replica'@'10.0.0.2';
```
Apply the changes and exit the MySQL server.
```
mysql> FLUSH PRIVILEGES;
mysql> EXIT;
```
Verify the status of the master.
```
mysql> SHOW MASTER STATUS\G
```
## Step 4: Configure the Slave Node (Server)

Open the configuration file in your slave server and update these lines:

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

As before, paste these lines under the [mysqld] section. Change the IP address to correspond to the slave’s IP. Also, assign a different server-id. Here we have assigned it the value of 2.

```
bind-address	 = 10.0.0.2
server-id	 = 2
log_bin 	 = mysql-bin
```
Save the changes and exit the file. Then restart the database server.

```
sudo service mysql restart
```

To configure the Slave node to replicate from the Master node, log in to the Slave’s MySQL server.
```
$ sudo mysql -u root -p
```
First and foremost, stop the replication threads:

```
mysql> STOP SLAVE;
```
Then execute the following command to configure the slave node to replicate databases from the master.

```
mysql> CHANGE MASTER TO
     MASTER_HOST='10.0.0.2' ,
     MASTER_USER='replica' ,
     MASTER_PASSWORD='P@ssword321' ,
     MASTER_LOG_FILE='mysql-bin.000001' ,
     MASTER_LOG_POS=1232;
```     

the MASTER_LOG_FILE and MASTER_LOG_POS flags correspond to the file and Position values

Then start the slave replication threads:
```
mysql> START SLAVE;
```

## Step 4: Testing MySQL Master-Slave Replication

Now, to test if replication between the master and slave node is working, log in to the MySQL database server on the master node:
```
$ sudo mysql -u root -p
```
Create a test database. Here, our test database is called replication_db.
```
mysql> CREATE DATABASE replication_db;

mysql> SHOW DATABASES;
```
Now, head over to the slave node, log in to the MySQL server and confirm that the replication_db database is present. 
