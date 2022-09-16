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
``

These commands will install and start MySQL, but will not prompt you to set a password or make any other configuration changes. Because this leaves your installation of MySQL insecure, we will address this next



