## Array destructuring in ES6

Without destructuring:

    const colors = ['red', 'green'];
    const firstColor = colors[0];
    const secondColor = colors[1];

With destructuring:

    const [firstColor, secondColor] = colors;