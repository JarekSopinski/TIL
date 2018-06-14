## Get template directory

If we're referencing theme's files, for example images in the <img> tag, we have to use Reference/get template directory function.

For example, let's say that we have an image tag:

	<img src="<images/banner.jpg">

This path to the images folder inside theme's folder won't work correctly in WordPress. Instead, we have to use get_template_directory() function. Here's an example:

	<img src="<?php echo(get_template_directory_uri()); ?>/images/banner.jpg">


To quote WP Codex: 

_This function retrieves the absolute path (eg: /home/user/public_html/wp-content/themes/my_theme) to the directory of the current theme._

Reference:
https://codex.wordpress.org/Function_Reference/get_template_directory

	


