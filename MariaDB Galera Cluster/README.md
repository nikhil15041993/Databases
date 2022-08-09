## Prerequisites

Three servers running Ubuntu 20.04.
A root password is configured on the server.
Getting Started
Before starting, you will need to update your system packages to the latest version. You can update them using the following command:
```
apt-get update -y
```

## Install MariaDB Server
First, you will need to install the MariaDB server on all nodes. You can install it by running the following command:

```
apt-get install mariadb-server -y
```
Once the installation has been finished, start the MariaDB service and enable them to start at system reboot:

```
systemctl start mariadb
systemctl status mariadb
```
Next, you will need to secure the MariaDB installation and set a MariaDB root password on each node. You can do it with the following command:

```
mysql_secure_installation
```

You will be asked to set a MariaDB root password as shown below:
```
Enter current password for root (enter for none): 
Switch to unix_socket authentication [Y/n] n
Change the root password? [Y/n] Y
New password:
Re-enter new password:
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```
Once your MariaDB server is secured, you can proceed to the next step.

## Configure Galera Cluster

Next, you will need to create a Galera configuration file on each node so that each node can communicate with each other.
On the first node, create a galera.cnf file using the following command:

```
nano /etc/mysql/conf.d/galera.cnf
```
Add the following lines:
```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="galera_cluster"
wsrep_cluster_address="gcomm://node1-ip-address,node2-ip-address,node3-ip-address"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="node1-ip-address"
wsrep_node_name="node1"
```
Save and close the file when you are finished.

On the second node, create a galera.cnf file using the following command:

```
nano /etc/mysql/conf.d/galera.cnf
```

```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="galera_cluster"
wsrep_cluster_address="gcomm://node1-ip-address,node2-ip-address,node3-ip-address"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="node2-ip-address"
wsrep_node_name="node2"
```

continue same steps for node 3

## Initialize the Galera Cluster

At this point, all nodes are configured to communicate with each other.

Next, you will need to stop the MariaDB service on all nodes. You can run the following command to stop the MariaDB service:
```
systemctl stop mariadb
```
On the first node, initialize the MariaDB Galera cluster with the following command:
```
galera_new_cluster
```

Now, check the status of the cluster with the following command:
```
mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
```
You should see the following output:
```
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 1     |
+--------------------+-------+
```

On the second node, start the MariaDB service with the following command:

```
systemctl start mariadb
```
Next, check the status of the MariaDB Galera cluster with the following command:
```
mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
```
You should see the following output:

```
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 2     |
+--------------------+-------+
```

On the third node, start the MariaDB service with the following command:
```
systemctl start mariadb
```
Next, check the status of the MariaDB Galera cluster with the following command:

```
mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
```
You should see the following output:
```
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 3     |
+--------------------+-------+
```

## Verify Cluster Replication

Next, you will need to verify whether the replication is working or not.

On the First node, connect to the MariaDB with the following command:

```
mysql -u root -p
```
Once your are connected, create some database with the following command:

```
MariaDB [(none)]> create database db1;
MariaDB [(none)]> create database db2;
```
Next, exit from the MariaDB with the following command:
```
MariaDB [(none)]> exit;
```
At this point, the MariaDB Galera cluster is initialized. You can now proceed to the next step.


Next, go to the second node and log in to the MariaDB with the following command:

```
mysql -u root -p
```
Next, run the following command to show all databases:
```
MariaDB [(none)]> show databases;
```
You should see that both databases that we have created on the first node are replicated on the second node:

```
+--------------------+
| Database           |
+--------------------+
| db1                |
| db2                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.001 sec
```


## Setup Haproxy for Galera:

Install haproxy load balancer if not installed already,

```
sudo apt-get update
sudo apt-get install -y haproxy
```
update haproxy with new configurations.
```
sudo vim /etc/haproxy/haproxy.cfg
```
Add the below line in the haproxy configuration file.

```
# Galera Cluster Fontend configuration
frontend galera_cluster_frontend
bind *:3306
mode tcp
option tcplog
default_backend galera_cluster_backend
# Galera Cluster Backend configuration
backend galera_cluster_backend
mode tcp
option tcpka
balance leastconn
option mysql-check user haproxy
server db-server-01 ​ 172.31.15.157:3306 ​ check weight 1server
db-server-02 ​ 172.31.10.235:3306 ​ check weight 1
server db-server-03 ​ 172.31.9.237:3306 ​ check weight 1
```
```
sudo systemctl restart haproxy
sudo systemctl status haproxy
```
