## Saas mixins

Mixin is a group of CSS declarations that can be called using @include keyword. Of course, every selector can use more than one mixin and mixins can be used with standard declarations.

Moreover, mixin can be modified based on arguments passed inside Saas variables. This flexibility causes mixins to be a better replacement for standard global classes.

Examples in SCSS syntax:

	@mixin flex-container ($justify-content) {
		display: flex;
		flex-flow: wrap;
		justify-content: $justify-content }

	.container {
		@include flex-container(center);
		max-width: 1200px }

	@mixin text ($font-family, $font-size, $text-align) {
		font-family: $font-family;
		font-size: $font-size;
		text-align: $text-align;
		color: black }

	h1 {
		@include text($title-font, 50px, center);
		text-transform: uppercase }

	p {
		@include text($paragraph-font, 15px, justify) } 

### Mixin with parent selector

Sometimes it's a good idea to setup mixin for pseudoselector. Here's where Sass parent selector (&) can be helpful. Below is an example of creating a mixin for a float clearfix:

	@mixin clearFix{
	    &:after{
	        content: '';
	        display: block;
	        clear: both;
	    }
	}

Now this mixin can work with any selector. In this case the parent selector acts like 'this' keyword in a class or object.

### Mixin and Sass math - writing a grid formula

Below is an example of combining mixing, arguments and math operators to create a grid of elements with equal width. This formula takes a number of columns as its first argument and a margin as its second argument.

Notice the usage of a Sass interpolation syntax: #{}

	@mixin grid($colsCount, $margin) {
	    float: left;
	    margin-right: $margin;
	    margin-bottom: $margin;
	    // formula for setting equal columns' width:
	    width: ( (100% - (($colsCount - 1) * $margin)) / 	$colsCount );
	    // target last child by the numer of columns in a 	row:
	    &:nth-child(#{$colsCount}n){
	        margin-right: 0;
	}

	.grid-item {
    	@include grid(4, 2%);
    }

### Mixin and @content keyword

Inside a mixin we can use the @content keyword, that will represent any code written inside that mixin (when it's included in the selector). This combination can be used to create mixins for media queries. Than we can easily include media queries in the selectors and our code will be cleaner.

	@mixin mediaQuery($width) {
	    @media screen and (max-width: $width){
	        @content;
	}

	.grid-item {
		width: 25%;
		@include mediaQuery(600px){
        // any content here will refer to @content in @mediaQuery mixin
        width: 100%;
        }
	}

### Mixin with any number of arguments

Now let's expand the previous example. We can create a mixin for media query that can take one or two arguments. If only one argument is provided, that mixin will only set max-width. If two arguments are provided, the second argument will be min-width and we'll get a 'double' media query, setting both max-width and min-width.

We can set optional number of arguments like this:

	@mixin someMixin($args...) {}

Then we can check for the number of provided arguments using the @if statement and length function:

	@if length($args) == 1 {}
	@if length($args) == 2 {}

Finally, we can select a certain argument using nth keyword:

	nth($args, 1) // first argument
	nth($args, 2) // second argument

Here's the whole example:

	@mixin mediaQuery($args...) {

	    @if length($args) == 1 {
	        @media screen and (max-width: nth($args, 1)){
	            @content;
	        }
	    }

	    @if length($args) == 2 {
	        @media screen and (max-width: nth($args, 1)) 	and (min-width: nth($args, 2)) {
	            @content;
	        }
		}
	}

	.title {
		font-size: 30px;
		@include mediaQuery(3000px, 1200px){
            font-size: 68px;
        }
	}