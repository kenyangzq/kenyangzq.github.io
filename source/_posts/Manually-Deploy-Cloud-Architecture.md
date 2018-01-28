---
title: Manually Deploy Cloud Architecture
date: 2017-09-23 13:31:01
tags: Cloud
---



# Manually Deploy Cloud Architecture

　　This is a step by step tutorial of how to setup LAMP stack on two virtual machines on OpenStack cloud infrastructure. After configuration, we want to deploy the <a href="https://github.com/Anirban2404/phpMySQLapp.git">phpMySQLapp</a> on our servers. The two virtuall machines will be functioned as web server and database server respectively.

　　Creation and configuration of virtual machines on open stack can be found <a href="https://www.openstack.org/software/start/">here</a>.  We will be using Ubuntu 16.04 as the OS of both virtuall machines. Note, we need to associate a floating IP address to our public web server.

## Step 1: Install LAMP stack on Web Server

　　Use ssh to login into the Web server. We will first install the LAMP stack manually. LAMP stack includes Linux, Apache, MySQL and PHP. It's considered by many as the platform of choice for development and deployment of web applications. 

　　Note: you need to have a non-root user with *sudo* privileges to continue. 

　　To install Apache, use the following command:

```
$ sudo apt-get update
$ sudo apt-get install apache2
```

　　Then for MySQL, the command is very similar:

```
$ sudo apt-get install mysql-server
```

　　For PHP, we need some additional metapackages:

```
$ sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
```

　　Your server should restart the apache server; if not, use the following command:

```
$ sudo /etc/init.d/apache2 restart
```

　　You should now have the Ubuntu LAMP server works. You can check php works by running any php files in `/var/www/` directory. 



## Step 2: Configure Apache on Web Server

　　Before we test our apache, the first thing we need to do is to modify our firework to allow outside access to the default web ports. For a default setting, you should have a UFW firewall configured to restrict access to your server. 

　　During installation, Apache registers itself with UFW to provide a few application profiles. We can use these profiles to simplify the process of enabling or disabling access to Apache through our firewall.

　　To check the ufw application profiles, use

```
$ sudo ufw app list
```

　　You should get an output like the following:

```shell
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```

 　　For our purposes, we will allow incoming traffic for the **Apache Full** profile by typing:

```
$ sudo ufw allow 'Apache Full'
```

　　You can verify the change by:

```
$ sudo ufw status
```

　　The output should look like:

```shell
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Apache Full                ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Apache Full (v6)           ALLOW       Anywhere (v6)
```

　　Now, hopefully you will have your Apache working.



## Step 3 Get the App repository and Configure PHP

　　Use git clone to clone the repository into your front end server (Web Server). 

```
$ git clone https://github.com/Anirban2404/phpMySQLapp.git
```

　　Move the directory to under `/var/www/html/` to enable php script. Use `tree` can clearly see the structure of the directory.

```
ubuntu@mywebserver:/var/www/html$ tree
.
├── admin_area
│   ├── insertbook.php
│   ├── insert_books.php
│   ├── insertmovie.php
│   └── insert_movies.php
├── books
│   ├── functions
│   │   ├── fetch.php
│   │   ├── functions.php
│   │   └── getbook.php
│   ├── home.php
│   ├── images
│   │   └── background_image.jpg
│   └── includes
│       └── bookDatabase.php
├── homePage.JPG
├── index.php
├── index.html
├── movies
│   ├── functions
│   │   ├── fetch.php
│   │   ├── functions.php
│   │   └── getmovie.php
│   ├── home.php
│   ├── images
│   │   └── background_image.jpg
│   └── includes
│       └── movieDatabase.php
├── mySqlDB
│   ├── bookDB.sql
│   └── movieDB.sql
├── README.md
└── siteImages
    ├── books.jpg
    └── movies.jpg
```

　　Note that `index.html` is the default page in the Apache server. You can either delete that file or configure PHP so that it will look into `index.php` to setup the front page.

　　To configure PHP, use `vim ` or any texteditor like `nano`  to open `/etc/apache2/mods-enabled/dir.conf ` as:

```i
$ sudo vim /etc/apache2/mods-enabled/dir.conf 
```

　　Change the order of the directory index into:

```i
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```

　　Now php will look for `index.php` before `index.html`. 



## Step 4 Install MySQL and Configure it on Database Server

　　SSH into our database server. As in the front end, we use apt-get to install MySQL. 

```i
$ sudo apt-get install mysql-server
```

　　When installing MySQL, it will prompt for password. Please remember it and we will use it later.

　　We also need to clone the repository of the Web App here. We need to import the 	database in the repository to our MySQL. So we do git clone again:

```i
$ git clone https://github.com/Anirban2404/phpMySQLapp.git
```

　　Following the instruction on the repository README, we import the database into our MySQL database by 

```i
$ mysql -u <username> -p <databasename> < <filename.sql>
```

　　Here, default username is root. Database names can be anything you like, but we are going to use them later. I chose 'bookstore' for my book database and 'movieDb' for my movie database. The files you need to import are in the 'mySqlDB' folder, as shown in the above tree. Argument -u is for username, -p means using password. This command will prompt you to enter your password for the database. 

　　Now that we have import our database, we need to configure MySQL. First, use whatever texteditor you prefer to open the configuration file of MySQL. The file location may vary depend on different package. For this demonstration, the command is:

```i
$ sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

　　In the configuration file, we need to bind our IP address to our database. Scroll down to find a line that looks like:

```i
bind-address		= xxx.xxx.xx.xx
```

　　Replace the IP address with our database server's IP address. If there is a line starts with skip-networking, comment it out:

```i
#skip-networking
```

　　Now save and exit the file. Restart the MySQL service:

```i
$ service mysql restart
```

　　The final step is to grant the privilege of our user from other IP address. First Enter MySQL database by 

```i
$ mysql -u root -p
```

　　Replace root with your username if needed. In MySQL, type the following command:

```i
mysql> GRANT ALL PRIVILEGES ON *.* TO 'USERNAME'@'%' IDENTIFIED BY 'PASSWORD' WITH GRANT OPTION;
```

　　Here 'USERNAME' should be your username, 'PASSWORD' should be your password. This command will grant privilege to user from any IP address. You can check that privileges are granted by:

```
SELECT * from information_schema.user_privileges;
```

　　You should see your user at the bottom of the chart. Now your database server is ready for use. 



## Step 5 Final Configuration on Web App

　　A final modification is on our Web server. SSH into it as usual. Use text editor to open `/var/www/html/books/includes/bookDatabase.php` and `/var/www/html/movies/includes/movieDatabase.php`. You need to replace the IP address there with the database server's IP address. If your password is not default password, replace it as well.

 　　After this final step, **Congratulations!** You should have manually deploy a cloud architecture! 

　　To run this app, open a browser and enter your web server's public floating IP address. You should see a page like:

<img src="images/Others/homePage.JPG">



## Reference

1. http://howtoubuntu.org/how-to-install-lamp-on-ubuntu
2. https://stackoverflow.com/questions/8348506/grant-remote-access-of-mysql-database-from-any-ip-address
3. https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-16-04