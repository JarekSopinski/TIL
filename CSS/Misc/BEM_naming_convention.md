## Basics of BEM methodology

[BEM (Block Element Modifier)](http://getbem.com/) is one of CSS methodologies. Basicly, it's a set of rules that define how to use selectors and how to name classes. Using BEM has many advantages, especially in a long term. Our CSS code will be much easier to maintain and to handle for other developers. Moreover, the code will be flat and we won't have to worry about selectors' specifity.

The primary idea behind BEM is that we have to think about CSS code as a set of blocks. These blocks are equal to each other and independent of a DOM structure.

### Most important rules

1. Select elements with classes only. Don't use tags or IDs.
2. Each class should belong to one of three categories: blocks, elements and modifiers (explained below).
3. Selectors should not depend on element's placement in DOM. Therefore you shouldn't use nested selectors or pseudoselectors like nth-child.
4. Name classes with small letters, don't use camelCase.

### Classes categories

1. Blocks: main, first-level classes. They are equal, independent and reusable. Examples: ".container", ".main-menu".
2. Elements: classes that are semantically tied to blocks. They define elements within block and have no standalone meaning. Element's name is built by addind its name to a block's name and separating it with two underscores (__). Examples: ".container__item", ".main-menu__fancy-button".
3. Modifiers: they are semantically tied to blocks or elements and can modify their appearance, behavior or state. Modifier's name is built by addind its name to a block's or element's name and separating it with two dashes (--). Examples: "container__item--visible", ".main-menu__fancy-button--red".

### Example

	<div class="container">
		<button class="container__button"></button>
		<button class="container__button button--red"></button>
		<button class="container__button button--black"></button>
	</div>

### Notes

1. You shouldn't build nested names, i.e. ".container__second-container__third-container". Instead use: ".container__third-container". Element's name should be tied only to its block.
2. You shouldn't use global classes (global modifiers) like ".visible", ".hidden", ".active".


