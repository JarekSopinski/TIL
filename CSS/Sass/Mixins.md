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
