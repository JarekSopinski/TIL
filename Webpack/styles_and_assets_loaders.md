## Styles and assets loaders

### CSS loaders

Webpack can inject css code directly into output .js file to improve performance.

To install css loaders run:

    npm install --save-dev style-loader css-loader

Then add css-loader to rules in webpack.config.js. Webpack will look for .css files using a regex test and then apply css-loader on such files:

    module.exports = {
    	...
        module: {
            rules: [
                {
                    test: /\.css$/,
                    use: ["css-loader"]
                }
            ]
        }
    }

Of course we have to import main .css file in the entry .js file:

    import "./main.css";

At this point css-loader will grab our .css file and turn it into JavaScript, but css code isn't applied just yet. To apply css into the DOM throgh the style tags we have to use another loader - style-loader. In webpack.config.js style-loader has to be added to the rules BEFORE css-loader:

    module.exports = {
    	...
        module: {
            rules: [
                {
                    test: /\.css$/,
                    use: [
                        "style-loader", // 2. Injects styles into the DOM
                        "css-loader" // 1. Turns css into common.js
                    ]
                }
            ]
        }
    }

### Sass loader

Loading sass code is also possible. First we have to install sass-loader, which depends on node-sass:

    npm install sass-loader node-sass --save-dev

Now we have to add sass-loader to webpack.config.js AFTER style-loader and css-loader:

    module.exports = {
    	...
        module: {
            rules: [
                {
                    test: /\.css$/,
                    use: [
                        "style-loader",  // 3. Injects styles into the DOM
                        "css-loader", // 2. Turns css into common.js
                        "sass-loader" // 1. Turns sass into css
                    ]
                }
            ]
        }
    }

### Extracting CSS

In production it's better to have a separate .css file rather than waiting for js (with included css) to load. Thus the need to extract css. It can be done with mini-css-extract-plugin.

    npm install --save-dev mini-css-extract-plugin

We have to require this plugin in webpack.prod.js (if we use separate config file for production mode) and add it to both plugins and rules. In production mode it can replace style-loader from previous example (style-loader can still be used for development to speed things up).

    const MiniCssExtractPlugin = require("mini-css-extract-plugin");

    module.exports = merge(common, {
        mode: "production",
    	...
        plugins: [
            new MiniCssExtractPlugin({ filename: "[name].[contentHash].css" }),
            ...
        ],
        module: {
            rules: [
                {
                    test: /\.scss$/,
                    use: [
                        MiniCssExtractPlugin.loader, // 3. Extracts css into separate .css files
                        "css-loader", // 2. Turns css into common.js
                        "sass-loader" // 1. Turns sass into css
                    ]
                }
            ]
        }
    });

To prevent possible conflict check if there are no other css in rules in the common config file (webpack.common.js).

### Loading assets

html-loader can be used to load images with require().

    npm install --save-dev html-loader

We add html-loader to the rules in webpack.common.js and test for .html files:

    {
        test: /\.html$/,
        use: ["html-loader"]
    }

At this point using require() with graphic extensions will cause webpack compilation error. To prevent this we have to install file-loader:

    npm install --save-dev file-loader

We add another rule in webpack.common.js for file-loader. We can set additional options, like building file name and path:

    {
        test: /\.(svg|png|jpg|gif)$/,
        use: {
            loader: "file-loader",
            options: {
                name: "[name].[hash].[ext]",
                outputPath: "imgs",
                esModule: false // a fix for getting correct path
            }
        }
    }