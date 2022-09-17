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

```
sudo mysql
```
Then run the following ALTER USER command to change the root user’s authentication method to one that uses a password. The following example changes the authentication method to mysql_native_password:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
After making this change, exit the MySQL prompt:

exit
Following that, you can run the mysql_secure_installation script without issue.

Once the security script completes, you can then reopen MySQL and change the root user’s authentication method back to the default, auth_socket. To authenticate as the root MySQL user using a password, run this command:

```
mysql -u root -p
```
Then go back to using the default authentication method using this command:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
```
This will mean that you can once again connect to MySQL as your root user using the sudo mysql command.

Run the security script with sudo:

```
sudo mysql_secure_installation
```

This will take you through a series of prompts where you can make some changes to your MySQL installation’s security options. The first prompt will ask whether you’d like to set up the Validate Password Plugin, which can be used to test the password strength of new MySQL users before deeming them valid.


## Step 3 — Creating a Dedicated MySQL User and Granting Privileges


In Ubuntu systems running MySQL 5.7 (and later versions), the root MySQL user is set to authenticate using the auth_socket plugin by default rather than with a password. This plugin requires that the name of the operating system user that invokes the MySQL client matches the name of the MySQL user specified in the command, so you must invoke mysql with sudo privileges to gain access to the root MySQL user:

```
sudo mysql
```

Note: If you installed MySQL with another tutorial and enabled password authentication for root, you will need to use a different command to access the MySQL shell. The following will run your MySQL client with regular user privileges, and you will only gain administrator privileges within the database by authenticating:

```
mysql -u root -p
```

Once you have access to the MySQL prompt, you can create a new user with a CREATE USER statement. These follow this general syntax:
```
CREATE USER 'username'@'host' IDENTIFIED WITH authentication_plugin BY 'password';
```

You have several options when it comes to choosing your user’s authentication plugin. The ```auth_socket``` plugin mentioned previously can be convenient, as it provides strong security without requiring valid users to enter a password to access the database. But it also prevents remote connections, which can complicate things when external programs need to interact with MySQL.


As an alternative, you can leave out the WITH authentication_plugin portion of the syntax entirely to have the user authenticate with MySQL’s default plugin, ```caching_sha2_password```.


Run the following command to create a user that authenticates with ```caching_sha2_password```. Be sure to change sammy to your preferred username and password to a strong password of your choosing:
```
CREATE USER 'sammy'@'localhost' IDENTIFIED BY 'password';
```



Note: There is a known issue with some versions of PHP that causes problems with caching_sha2_password. If you plan to use this database with a PHP application — phpMyAdmin, for example — you may want to create a user that will authenticate with the older, though still secure, mysql_native_password plugin instead:

```
CREATE USER 'sammy'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
If you aren’t sure, you can always create a user that authenticates with caching_sha2_plugin and then ALTER it later on with this command:
```
ALTER USER 'sammy'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```


After creating your new user, you can grant them the appropriate privileges. The general syntax for granting user privileges is as follows:

```
GRANT PRIVILEGE ON database.table TO 'username'@'host';
```

Run this GRANT statement, replacing sammy with your own MySQL user’s name, to grant these privileges to your user:

```
GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
```


Warning: Some users may want to grant their MySQL user the ALL PRIVILEGES privilege, which will provide them with broad superuser privileges akin to the root user’s privileges, like so:

```
GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
```

Following this, it’s good practice to run the FLUSH PRIVILEGES command. This will free up any memory that the server cached as a result of the preceding CREATE USER and GRANT statements:

```
FLUSH PRIVILEGES;
```
Then you can exit the MySQL client:

```
exit
```
In the future, to log in as your new MySQL user, you’d use a command like the following:
```
mysql -u sammy -p
```


## Step 4 — Testing MySQL
Regardless of how you installed it, MySQL should have started running automatically. To test this, check its status.

```
systemctl status mysql.service
```

If MySQL isn’t running, you can start it with ```sudo systemctl start mysql```


For an additional check, you can try connecting to the database using the ```mysqladmin``` tool, which is a client that lets you run administrative commands. For example, this command says to connect as a MySQL user named sammy (-u sammy), prompt for a password (-p), and return the version. Be sure to change sammy to the name of your dedicated MySQL user, and enter that user’s password when prompted:.

```
sudo mysqladmin -p -u sammy version
```
