## The loop in WordPress - iterating through posts

The loop in WP determines what content to display on the page you're visiting. This is WP's way of displaying a dynamic content.

WP can grab the content it needs based on a url. Then we can cycle through that content using a php while loop.

The first step of a loop is an if statement. It checks if there are any posts. If this evaluates to true, then we can start the loop:

	if ( have_posts() ) :
	// in case of true, start a while loop

Now, a while loop will go through all of the retrieved content (posts):

	while ( have_posts() ) :
	
As long as there are posts, posts' data will be loaded using the_post() - a WP function. This sets up the data that will be used. This data is then passed to a template tags, which output data of a specific post:

	the_post(); ?> // getting post's data
	
	<h1><?php the title() ?></h1> // this is a template tag
	<?php the content(); ?>	// this also is a template tag

Here we can output whatever we want of these posts: image, author etc. There are many template tags that can display specific data. Here's a list of template tags we can use in WP: https://codex.wordpress.org/Template_Tags

Below is a full example of a most basic WP loop:

	<?php
	
	// wordpress retreives the content it needs behind the scenes and stores it
	// in a global variable

	// check if wordpress retrieved post(s):
	if ( have_posts() ) :
		// if there are any posts, start a while loop
		while ( have_posts() ) :
			// for each post, load the post data:
			the_post(); ?>

		// content is passed to the template tags:
			<h1><?php the title() ?></h1>
			<?php the content(); ?>

		<?php endwhile;
	endif;

	?>

This example is based on NetNinja's PSD to WP tutorial - thanks!
	




