## Basic Webpack configuration

To initialize project using Webpack run:

    npm init
    npm install --save-dev webpack webpack-cli

Then add Webpack to npm scripts in package.json:

    "scripts": {
        "start": "webpack"
    }

Now we can run Webpack with npm start. Without any configuration Webpack will look for default entry file under src/index.js and it'll create default output under dist/main.js.

### Webpack config file

We can set up custom configuration using config file, such as webpack.config.js. I.e. we can customize entry point and output:

    const path = require("path");

    module.exports = {
        entry: "./src/index.js",
        output: {
            filename: "bundle.js",
            path: path.resolve(__dirname, "dist")
        }
    }   

To make Webpack recognize our config file, we have to specify it in package.json:

    "scripts": {
       "start": "webpack --config webpack.config.js"
    },

We can also specify if we want to use a production or development mode. By default in development mode output won't be minified.

    module.exports = {
        mode: "development",
        devtool: "none",
        ...
    }  

We can set up output file to be named with different hashes. This will prevent unwanted script caching in a browser:

    output: {
        filename: "bundle.[contentHash].js",
        path: path.resolve(__dirname, "dist")
    },