## Development and production modes

Instead of using only one Webpack config file, we can set up different files for production and development modes, i.e. webpack.dev.js and webpack.prod.js. 

webpack.dev.js (i.e. no minification for faster outputting and easier debugging, css included in .js file):

    module.exports = {
        mode: "development",
        ...
    } 

webpack.prod.js (i.e. minification, separate .css files)

    module.exports = {
        mode: "development",
        ...
    } 

Usually webpack.dev.js is used with 'npm start' and webpack.prod.js with 'npm run build'. So in package.json we can set it up like this:

  "scripts": {
    "start": "webpack --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js"
  },

### Common configuration

Common settings can still be kept in a file such as webpack.common.js. To make dev and prod files use this file we have to install webpack-merge package:

    npm install --save-dev webpack-merge

Now we have to require webpack.common.js in webpack.dev.js. We also have to require merge and execute it with two arguments: 1) common 2) dev settings:

    const common = require("./webpack.common");
    const merge = require("webpack-merge");

    module.exports = merge(common, {
        mode: "development",
        entry: "./src/index.js",
        output: {
            filename: "main.js",
            path: path.resolve(__dirname, "dist")
        }
    });

We repeat this in webpack.prod.js in a similar way.

### Dev server

To use development server with hot reload (in other words - don't repeat npm start after each change) we can use webpack-dev-server package. It has to be added to start script in package.json in place of 'webpack'. If we add --open flag, the browser will automaticly open a window with correct localhost port.

    npm install --save-dev webpack-dev-server

    "scripts": {
        "start": "webpack-dev-server --config webpack.dev.js --open",
    	...
    },