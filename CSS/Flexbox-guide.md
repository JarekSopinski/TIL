## Flexbox guide

### Parent container

* display: flex, inline-flex

    Required for all other flex properties. Causes child elements to be positioned according to flexbox rules.

* flex-wrap: wrap / wrap-reverse / nowrap (default)

    Specifies how child elements should be displayed after they are shrinked below their minimum width. 'Wrap' property will cause them to automaticly take space in a next     row (below). With 'wrap-reverse' they will be placed in a row above. 'Nowrap' is default - children will always stay in a one row.

* flex-direction: row (default), column, row-reverse, column-reverse

    Controls the flow of child elementes. By default elements are set to flow horizontaly, along the x-axis ('flex-flow: row'). 'Flex-flow: column' causes them to flow verticaly, along the y-axis. Basicly you could say that flex-flow simply switches x-axis and y-axis. The way other properties work is also switched along with it. I.e. 'flex-flow: column' used with 'justify-content: center' will cause items to be justified in relation to the y-axis; 'flex-basis' will control inital height instead of initial width, and so on. With 'Row-reverse' or 'column-reverse' items will be placed in a reversed order ('flex-start' will behave as 'flex-end' and so on).

* flex-flow: (flex direction, flex-wrap)

    A shorthand property for flex-wrap and flex-direction, i.e 'flex-flow: row wrap'.

* justify-content: center, flex-end, flex-start (default), space-around, space-between, space-evenly

    Specifies how child items are positioned along the x-axis. 'Center' moves children to the center of parent container, 'flex-end' moves them to the right end. 'Space-around' positiones them along the whole x-axis with equal gaps. 'Space-between' is like 'space-around', but with no gaps on the edges. 'Space-evenly' works in a similar way, but provides equal instead of half-sized space on the edges. It should be noted that as of 2019 'space-evenly' is not supported by MS Edge (nor is it supported by IE). If used with 'flex-flow: column', 'justify-content' will apply to the y-axis.

* align-items: center, baseline, flex-end, flex-start (default)

    Specifies how child items are positioned along the y-axis. In other words, it's like a vertical version of 'justify-content'. If used with 'flex-flow: column', it will apply to the x-axis.

### Child elements

* flex-grow: value (integer)

    Specifies the grow rate. If all elements have the same value (i.e. flex-grow: 1) they will take equal space in their container. If one of them would have 'flex-grow:2', it would grow two times faster than all other. The values are relative to each other, so 'flex-grow:1' and 'flex-grow:2' would have the same effect as 'flex-grow:10' and 'flex-grow:20'.

* flex-shrink: value (integer)

    Specifies the shrink rate. As parent container is shrinked, child with 'flex-shrink:2' will shrink two times faster than a child with 'flex-shrink:1'. Rarely used.

* flex-basis: measurement unit value

    Controls initial width of child elements. Works in a similar way to 'min-width', but after container gets shrinked below its value, element using flex-basis will be shrinked along the container (it won't force scrollbar). If used with 'flex-flow: column', it will control height instead of width.

* flex: grow value, shrink value, flex basis

    A shorthand for three previous properties. I.e. 'flex: 1 0 200px' is the same as 'flex-grow: 1; flex-shrink: 0; flex-basis: 200px'. You can declare only one value - i.e. 'flex: 1' is the same as 'flex-grow: 1; flex-shrink: 1; flex-basis: 0' (the last value is set by default).

* order: integer

    Controls the order of items - item with smaller number is always first. 0 is the default value.