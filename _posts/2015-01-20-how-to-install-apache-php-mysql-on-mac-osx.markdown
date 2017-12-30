---
layout: post
title:  "How to install Apache PHP and MySQL on Mac OS"
image: posts/OS_X_Yosemite_Desktop.png
categories: [php, programming]
tags: [php, programming, apache, mysql, macos, macbook, virtualbox]
---

Need to get working your own local web server in your Mac? If you have Maverics or Yosemite, you can follow this guide!

There are a lot of ways to have a web server working on your Mac, for example:

Create your own Virtual Machine (installing VirtualBox or VMWare)
Using Vagrant + Puppet
Installing MAMP
Using PuPHPet
And finally, using the built in Apache and PHP in OSX (you can follow this guide :))

Apache

1. Open terminal app on your Mac. Just go in Application/Others and open the Terminal


2. Running Apache To run the web server just type in terminal this command:

```
sudo apachectl start`
```

And here is how you can stop and restart it:
```
sudo apachectl stop
sudo apachectl restart
```

3. Check Apache is running Open your favorite browser and go to http://localhost. You should see a blank page with just one text: "It works!"

Now, the default Document Root is this path : /Library/WebServer/Documents/.

You can change it editing httpd.conf at this path : /etc/apache2/httpd.conf or just adding a new Virtual Host configuration.

To add a new VH, you must edit this file: /etc/apache2/extra/httpd-vhosts.conf

You can add something like this:
```
<VirtualHost *:80>
    ServerAdmin yourmail@example.org
    DocumentRoot "/Users/YourUser/Code/example.org/"
    ServerName www.example.org
    ServerAlias www.example.org
    ErrorLog "/Users/YourUser/Code/example.org/app/logs/dev_error.log"
    CustomLog "/Users/YourUser/Code/example.com/app/logs/dev_access.log" common
    <Directory "/Users/YourUser/Code/example.org">
		AllowOverride All
    </Directory>
</VirtualHost>
```

You have added example.org to Apache configurations.
Normally, if you go to example.org in your browser, you would be redirected to the real website, but if you modify your /etc/hosts file, by adding this line:

127.0.0.1 www.example.org
your browsers will search for www.example.org website, at this IP: 127.0.0.1 that is the equivalent to localhost.

Now restart your Apache, as shown above, and go to http://www.example.org, you should see the content within this path /Users/YourUser/Code/example.org/


##PHP

As Apache, PHP is already installed in your OSX. The version, for Yosemite, should be 5.5.14.

How can we activate it?

Just modify /etc/apache2/httpd.conf, find this line LoadModule php5_module libexec/apache2/libphp5.so and remove the "#".

Save the file and restart Apache.

Now your web server should support php files!

But let's test it: create a test.php file in /Users/YourUser/Code/example.org/ and inside write this lines:

```
<?php phpinfo() ?>
```

Go to http://www.example.org/test.php, you should see PHP configurations.


## MySQL

MySQL is the one not included in the operative system, so download it here.

The version should be this Mac OS X 10.9 (x86, 64-bit), DMG Archive (don't worry for the 10.9, it's compatible with Yosemite too).

Once installed, you can start the MySQL server in two ways:

System preferences
Command Line (terminal)
If you want to start the database server from terminal, just open it and type:

```
sudo /usr/local/mysql/support-files/mysql.server start
```

The last step is to set the root password for MySQL Server.

Open terminal and type (replace yourpwd for the password you have choosen):

```
/usr/local/mysql/bin/mysqladmin -u root password 'yourpwd'
```

That's all! Now you should have an AMP, that means Apache MySQL PHP, environment working correctly!

Hope this guide is useful :)