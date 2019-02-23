## A guide to CSS Grid

### Parent container

* display: grid

    Required for all other grid properties. Causes child elements to be positioned according to grid rules.

* grid-template-columns: (values / function)

    Sets number and width of columns. I.e. 'grid-template-columns: 33.33% 33.33% 33.33%' will divide content into three columns with equal width. Typically, with css grid we use the fraction unit, i.e. 'grid-template-columns: 1fr 1fr 1fr' will give us the same result as in a previous example. 
    
    This can be simplified by using the repeat function, i.e. 'grid-template-columns: repeat(3, 1fr)' will also divide grid into three equal columns. In a repeat function the first argument is a number of repeats and the second is the unit.

* grid-template-rows: (values / function)

    Sets number and width of rows, analogous to grid-template-columns. I.e. 'grid-template-rows: repeat(3, 200px)'. By default new rows are created along with the content, so there is no need to use this. Still, we should use this if we want to specify row positions for an element with 'grid-row' property. Number of rows can be bigger than the number of rows with actual content - rows without content will be than invisible.


* grid-auto-rows: (values / function)

    Sets the height of rows. I.e. 'grid-auto-rows: 200px' will create rows with fixed(!) height of 200px. If we want rows to be flexible, we can use the minmax functions. I.e. 'grid-auto-rows: minmax(200px auto)' will create rows with minimal height of 200px, that can stretch along with its content's height. Note that minmax function can be used as an argument for repeat function - i.e. repeat(4, minmax(150px auto)).

* grid-column-gap: (value), grid-row-gap: (value), grid-gap: (value)

    Sets the gap between columns or rows (and only between them - no gaps on the edges of the whole grid). 'Grid-gap' is a shorthand property for two first properties.

* align-items: start, end, stretch (default)

    This property can be used to align grid element's content along the y-axis (verticaly).

* justify-items: start, end, stretch (default)

    This property can be used to align grid element's content along the x-axis (horizontaly).

* grid-template-areas: grid areas names

    Specifies positioning for elements tagged with 'grid-area' property. I.e. ' grid-template-areas: "header header header header" "aside aside main main" '.


### Child elements

* grid-column-start: (integer - start pos.), grid-column-end: (integer - end pos.), grid-column: (two integers: start pos. / end pos.)

    Sets the starting and ending point for an element in a x-axis of a grid. I.e with 'grid-column-start: 1' and 'grid-column-end: 3' element will fill the first two columns (from left border of first column - 1st line - to right border of second column - 3rd line). 'Grid-column' is a shorthand property, i.e. 'grid-column: 1 / 3' will give us the same result. We can also use the 'span' keyword to define how many columns an element should fill - i.e. 'grid-column: span 3' will cause it to fill three columns next to its starting point. Note that the order of elements can be totally different than their order in DOM.

* grid-row-start: (integer - start pos.), grid-row-end: (integer - end pos.), grid-row: (two integers: start pos. / end pos.)

    Sets the starting and ending point for an element in a y-axis of a grid, analogous to 'grid-column'. I.e. 'grid-row(2/4).

* justify-self, align-self

    Works as 'align-items' and 'justify-items', but for a single element.

* grid-area

    A shorthand property for 'grid-column' and 'grid-row'. I.e. item with 'grid-area: 2 / 3 / span 4 / span 4' will start on row 2 column 3, and span 4 rows and 4 columns. It can also be used to assign a name to an element, that will be referenced by 'grid-template-areas' property of its container. I.e. we can set 'main' tag as 'grid-area: main'.