## How to install LAMP (Apache, MySql, php) on Ubuntu

First run update:	

	sudo apt-get update

Install Apache:

	sudo apt-get install apache2

You can test Apache by going to localhost or your ip adress in the browser. If you see "Apache2 Ubuntu Default Page", than it works correctly.

Install php:

	sudo apt-get install php
	sudo apt-get install libapache2-mod-php

Test php by creating simple test file (restart Apache first):

	sudo /etc/init.d/apache2 restart
	sudo nano /var/www/html/test.php

In nano, type a basic php line:

	<?php 
	echo "It works"; 
	?>

Then ctrl-x, enter. Open localhost/test.php (or youripadress/test.php) in a browser.

Install mySQL:

	sudo apt-get install mysql-server
	(You'll be asked to create a password)

You can now enter mySQL by typing:

	mysql -u root -p

...and test it:

	create database testdb;
	show databases;
	exit;

Install phpMyAdmin:

	sudo apt-get install phpmyadmin

When asked to choose a server, choose default (apache2).
When asked if database should be configured with dbconfig-common, choose yes.
Again, you'll be asked to provide a password.

Now let's access phpMyAdmin. First, we have to edit php ini file, that is located in Apache folder. Let's open this file in nano:

	sudo nano /etc/php/7.0/apache2/php.ini

Inside this file, scroll down to "Dynamic Extensions" section (it takes a little time).
Remove semicolon in front on "extension=mysql.so" line. CTRL-X, enter to save and exit.

Enter Apache config file:

	sudo nano /etc/apache2/apache2.conf

Go to the very bottom of this file and add a following line (after "#vim syntax..."):

	Include /etc/phpmyadmin/apache.conf

Restart Apache again:

	sudo /etc/init.d/apache2 restart

Access phpMyAdmin by typing in a browser:

	localhost/phpmyadmin

Or:

	youripadress/phpmyadmin

Enter root as a user and type previously created password.

This guide is based on a YouTube tutorial: 
https://www.youtube.com/watch?v=vazRx1Ei8VA

	








	
