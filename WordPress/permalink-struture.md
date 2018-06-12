## Permalink structure

Permalink structure is basicly a structure of URLs. We can control this from Settings / Permalinks.

For example, default post's URL can look like this:

	http://localhost/index.php/2018/06/12/hello-world/

In Permalink settings we can change this, for example, to URL based on post's name:

	http://localhost/sample-post/

Now, if we haven't enabled rewrite module in Apache, going to the post will cause an error. To solve this, we should enable this by typing in the terminal:

	sudo a2enmod rewrite

Also, we might have to edit Apache config file. The path to this file should look like this:

	/etc/apache2/apache2.conf

Open this file in an editor:

	sudo nano /etc/apache2/apache2.conf

In the following line:

	<Directory /var/www/>
    		Options Indexes FollowSymLinks
    		AllowOverride None
    		Require all granted
	</Directory>

Change "AllowOverride None" to "AllowOverride All":

	<Directory /var/www/>
    		Options Indexes FollowSymLinks
    		AllowOverride All
    		Require all granted
	</Directory>

	
Finally, restart Apache:

	sudo /etc/init.d/apache2 restart

Now new permalink settings should work fine.
