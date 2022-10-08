## Installing pgAdmin4 in Ubuntu

pgAdmin4 is not available in the Ubuntu repositories. We need to install it from the pgAdmin4 APT repository. Start by setting up the repository. Add the public key for the repository and create the repository configuration file.

 ```
$ curl https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add
$ sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
```

Then install pgAdmin4,
```
$sudo apt install pgadmin4
```

The above command will install numerous required packages including Apache2 webserver to serve the pgadmin4-web application in web mode.

Once the installation is complete, run the web setup script which ships with the pgdmin4 binary package, to configure the system to run in web mode. You will be prompted to create a pgAdmin4 login email and password as shown in the screenshot below.


This script will configure Apache2 to serve the pgAdmin4 web application which involves enabling the WSGI module and configuring the pgAdmin application to mount at pgadmin4 on the webserver so you can access it at:

```
http://SERVER_IP/pgadmin4
```

It also restarts the Apache2 service to apply the recent changes.

Remember to replace admin@tecmint.lan with your email address and set a strong secure password as well:

```
$ sudo /usr/pgadmin4/bin/setup-web.sh
```

## Accessing pgAdmin4 Web Interface
To access the pgAdmin4 web application interface, open a web browser, and use the following address to navigate:

```
http://SERVER_IP/pgadmin4
```
