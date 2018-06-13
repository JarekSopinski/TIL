## Create a new WordPress theme

A "clean" boilerplate for a new theme can be generated from underscores.me. You'll be able to generate a zip archive, which will serve as a foundation on which we'll build a new theme.

Now, in WordPress dashboard, go to appearance / themes and choose "add new theme". Upload a zip archive from underscores.

If you'll have problems with WP asking for credentials (assuming you work on Ubuntu), type:

	chown -Rf www-data.www-data /var/www/html/

This will set the files to be owned by the user that the web service is run as and should resolve the problem. If you'll want to restore full access, simply type:

	sudo chmod 777 -R /var/www/html

If theme was succesfully installed, you should see it after returning to themes page. Also, you'll be able to access its files via wp-content/themes.

Click "activate" button to activate this new theme. Now, if you visit the page, you'll see pure html - and that's the point of a "clean" underscores boilerplate.

If you wish to create a thumbnail for this new theme, simply make a 1200x900 screenshot and overwrite screenshot.png in theme's folder.
