# How To Install MySQL on Ubuntu 20.04


## Step 1 — Installing MySQL

On Ubuntu 20.04, you can install MySQL using the APT package repository. At the time of this writing, the version of MySQL available in the default Ubuntu repository is version 8.0.27.

To install it, update the package index on your server if you’ve not done so recently:
```
sudo apt update
```
Then install the mysql-server package:
```
sudo apt install mysql-server
```
Ensure that the server is running using the systemctl start command:
```
sudo systemctl start mysql.service
```

These commands will install and start MySQL, but will not prompt you to set a password or make any other configuration changes. Because this leaves your installation of MySQL insecure, we will address this next

## Step 2 — Configuring MySQL

For fresh installations of MySQL, you’ll want to run the DBMS’s included security script. This script changes some of the less secure default options for things like remote root logins and sample users.


```
Warning: As of July 2022, an error will occur when you run the mysql_secure_installation script without some further configuration. The reason is that this script will attempt to set a password for the installation’s root MySQL account but, by default on Ubuntu installations, this account is not configured to connect using a password.

Prior to July 2022, this script would silently fail after attempting to set the root account password and continue on with the rest of the prompts. However, as of this writing the script will return the following error after you enter and confirm a password:

Output
```
 ... Failed! Error: SET PASSWORD has no significance for user 'root'@'localhost' as the authentication method used doesn't store authentication data in the MySQL server. Please consider using ALTER USER instead if you want to change authentication parameters.
```

New password:
This will lead the script into a recursive loop which you can only get out of by closing your terminal window.

Because the mysql_secure_installation script performs a number of other actions that are useful for keeping your MySQL installation secure, it’s still recommended that you run it before you begin using MySQL to manage your data. To avoid entering this recursive loop, though, you’ll need to first adjust how your root MySQL user authenticates.

First, open up the MySQL prompt:

### sudo mysql
Then run the following ALTER USER command to change the root user’s authentication method to one that uses a password. The following example changes the authentication method to mysql_native_password:

### ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
After making this change, exit the MySQL prompt:

exit
Following that, you can run the mysql_secure_installation script without issue.

Once the security script completes, you can then reopen MySQL and change the root user’s authentication method back to the default, auth_socket. To authenticate as the root MySQL user using a password, run this command:

### mysql -u root -p
Then go back to using the default authentication method using this command:

### ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
This will mean that you can once again connect to MySQL as your root user using the sudo mysql command.

Run the security script with sudo:

### sudo mysql_secure_installation
```

