# MySQL Server Remote Connection


Allowing connections to a remote MySQL server is set up in 3 steps:

1. Edit MySQL config file.

2. Configure firewall.

3. Connect to remote MySQL server.


## Step 1: Edit MySQL Config File

```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

1.2 Change Bind-Address IP

You now have access to the MySQL server configuration file. Scroll down to the bind-address line and change the IP address. The current default IP is set to 127.0.0.1. This IP limits MySQL connections to the local machine.


The new IP should match the address of the machine that needs to access the MySQL server remotely. For example, if you bind MySQL to 0.0.0.0, then any machine that reaches the MySQL server can also connect with it.


1.3 Restart MySQL Service

Apply the changes made to the MySQL config file by restarting the MySQL service:
```
sudo systemctl restart mysql
```

## Step 2: Set up Firewall to Allow Remote MySQL Connection

While editing the configuration file, you probably observed that the default MySQL port is 3306.


Option 1: UFW (Uncomplicated Firewall)
UFW is the default firewall tool in Ubuntu. In a terminal window, type the following command to allow traffic and match the IP and port:
```
sudo ufw allow from remote_ip_address to any port 3306
```

Option 2: FirewallD

The firewalld management tool in CentOS uses zones to dictate what traffic is to be allowed.

Create a new zone to set the rules for the MySQL server traffic. The name of the zone in our example is mysqlrule, and we used the IP address from our previous example 133.155.44.103:

```
sudo firewall-cmd --new-zone=mysqlrule --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --permanent --zone=mysqlrule --add-source=133.155.44.103
sudo firewall-cmd --permanent --zone=mysqlrule --add-port=3306/tcp
sudo firewall-cmd --reload
```

You have successfully opened port 3306 on your firewall.


Option 3: Open Port 3306 with iptables

The iptables utility is available on most Linux distributions by default. Type the following command to open MySQL port 3306 to unrestricted traffic:
```
sudo iptables -A INPUT -p tcp --dport 3306 -j ACCEPT
```
To limit access to a specific IP address, use the following command instead:
```
sudo iptables -A INPUT -p tcp -s 133.155.44.103 --dport 3306 -j ACCEPT
```

This command grants access to 133.155.44.103. You would need to substitute it with the IP for your remote connection.


It is necessary to save the changes made to the iptables rules. In an Ubuntu-based distribution type the following commands:
```
sudo netfilter-persistent save
sudo netfilter-persistent reload
```

Type the ensuing command to save the new iptables rules in CentOS:
```
service iptables save
```


Step 3: Connect to Remote MySQL Server
Your remote server is now ready to accept connections. Use the following command to establish a connection with your remote MySQL server:
```
mysql -u username -h mysql_server_ip -p
```

## How to Grant Remote Access to New MySQL Database?

If you do not have any databases yet, you can easily create a database by typing the following command in your MySQL shell:
```
CREATE DATABASE 'yourDB';
```
To grant remote user access to a specific database:
```
GRANT ALL PRIVILEGES ON yourDB.* TO user1@'133.155.44.103' IDENTIFIED BY 'password1';
```
The name of the database, the username, remote IP, and password need to match the information you want to use for the remote connection.

How to Grant Remote Access to Existing MySQL Database
Granting remote access to a user for an existing database requires a set of two commands:
```
update db set Host='133.155.44.103' where Db='yourDB';
update user set Host='133.155.44.103' where user='user1';
```
User1 is now able to access yourDB from a remote location identified by the IP 133.155.44.103.
