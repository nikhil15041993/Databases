# phpMyAdmin


phpMyAdmin is a free web application that provides a convenient GUI for working with the MySQL database management system

### Prerequisites

* An Ubuntu 20.04 server. This server should have a non-root user with administrative privileges and a firewall configured with ufw. To set this up, follow our initial server setup guide for Ubuntu 20.04.
* A LAMP (Linux, Apache, MySQL, and PHP) stack installed on your Ubuntu 20.04 server. If this is not completed yet, you can follow this guide on installing a LAMP stack on Ubuntu 20.04.


## Step 1 — Installing phpMyAdmin

### 1. First install apache2  
```
sudo apt-get install apache2
```
### 2. moving to the root directory type the command : 
```
Sudo su 

sudo apt-get update && sudo apt-get upgrade
```
### 3. Now to install the lamp server type the command:
```
apt-get install lamp-server^
```
### 4. install phpmyadmin type the command as 
```
apt-get install phpmyadmin –y
```
One dialog box will appear and select the ‘apache2’ by pressing spacebar.
Now configuring phpmyadmin dialog box will appear and select ‘yes’ after that give the password in my case it’s  ‘admin’ 🡪 This password is of ‘root   password’ 

Now, goto the php directory as:-
    ```
    cd /var/www/html/
    ```
To see the php version you are using create info.php using command as:-
      ```nano info.php```
 after that type the code as:-
 ```
   phpinfo();    
 ```
 
 Now goto the browser and type ‘localhost/info.php’ and phpmyadmin for <ip address/phpmyadmin>
 
 ## Step 2 — Adjusting User Authentication and Privileges
 
 ### Configuring Password Access for the MySQL Root Account
 
 In order to log in to phpMyAdmin as your root MySQL user
 ```
 sudo mysql
 ```
 Next, check which authentication method each of your MySQL user accounts use with the following command:
 ```
 mysql>  SELECT user,authentication_string,plugin,host FROM mysql.user;
 ```
 
 In this example, you can see that the root user does in fact authenticate using the auth_socket plugin. To configure the root account to authenticate    with a password, run the following ALTER USER command. Be sure to change password to a strong password of your choosing:

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
```

However, some versions of PHP don’t work reliably with caching_sha2_password. PHP has reported that this issue was fixed as of PHP 7.4, but if you encounter an error when trying to log in to phpMyAdmin later on, you may want to set root to authenticate with mysql_native_password instead:

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
Then, check the authentication methods employed by each of your users again to confirm that root no longer authenticates using the auth_socket plugin:

```
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
```

## Step 3 Configuring Password Access for a Dedicated MySQL User


Alternatively, some may find that it better suits their workflow to connect to phpMyAdmin with a dedicated user.

open up the MySQL shell once again:

```
sudo mysql

mysql -u root -p
```

From there, create a new user and give it a strong password:
```
mysql> CREATE USER 'sammy'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
```
depending on what version of PHP you have installed, you may want to set your new user to authenticate with mysql_native_password instead of caching_sha2_password:
```
mysql> ALTER USER 'sammy'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

Then, grant your new user appropriate privileges.
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
```

You can now access the web interface by visiting your server’s domain name or public IP address followed by /phpmyadmin:
```
https://your_domain_or_IP/phpmyadmin
```

## Step 4 Securing Your phpMyAdmin Instance

Use your preferred text editor to edit the phpmyadmin.conf file that has been placed in your Apache configuration directory. Here, we’ll use nano:
```
sudo nano /etc/apache2/conf-available/phpmyadmin.con
```

Add an AllowOverride All directive within the <Directory /usr/share/phpmyadmin> section of the configuration file, like this:

/etc/apache2/conf-available/phpmyadmin.conf
```
<Directory /usr/share/phpmyadmin>
    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php
    AllowOverride All
    . . .
```

In order for this to be successful, the file must be created within the application directory. You can create the necessary file and open it in your text editor with root privileges by typing:

```
sudo nano /usr/share/phpmyadmin/.htaccess
```

Within this file, enter the following information:

```
/usr/share/phpmyadmin/.htaccess
AuthType Basic
AuthName "Restricted Files"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user
```

When you are finished, save and close the file.

The location that you selected for your password file was /etc/phpmyadmin/.htpasswd. You can now create this file and pass it an initial user with the htpasswd utility:
```
sudo htpasswd -c /etc/phpmyadmin/.htpasswd username
```

You will be prompted to select and confirm a password for the user you are creating. Afterwards, the file is created with the hashed password that you entered.

If you want to enter an additional user, you need to do so without the -c flag, like this:
```
sudo htpasswd /etc/phpmyadmin/.htpasswd additionaluser
```

Then restart Apache to put .htaccess authentication into effect:
```
sudo systemctl restart apache2
```
Now, when you access your phpMyAdmin subdirectory, you will be prompted for the additional account name and password that you just configured:
```
https://domain_name_or_IP/phpmyadmin
```

## Step 5  Restrict access to the phpMyAdmin by IP Address

edit the phpmyadmin config file 

```
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```
add these code inside <Directory /usr/share/phpmyadmin>

```
#Restrict phpMyAdmin via IP address
Order Deny,Allow
Deny from All
Allow from <your ip address>
```
Reset the apache service

```
sudo /etc/init.d/apache2 restart
```
