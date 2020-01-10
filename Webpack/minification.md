## Minification

In production mode we should always output minified .js and .css files.

### CSS minification

CSS minification can be performed with optimize-css-assets-webpack-plugin. We use it in production mode as optimization.minimizer property:

    npm install --save-dev optimize-css-assets-webpack-plugin

In webpack.prod.js:

    const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");

    ...

    optimization: {
        minimizer: [
            new OptimizeCssAssetsPlugin()
        ]
    },

### JS minification

By default in production mode Webpack minifies .js with TerserPlugin. But if we set up .css minification as shown above, default .js optimization is going to be overwritten and we'll get only minified .css. To prevent this from happening we have to manually set up Terser Plugin. Since it's a default js minimizer for Webpack, it should already be included in node modules, so we don't have to install it.

In webpack.prod.js:

    optimization: {
        minimizer: [
            new OptimizeCssAssetsPlugin(),
            new TerserPlugin()
        ]
    },

### HTML minification

Additionaly we can also minify and clean html. For this we use HtmlWebpackPlugin, which is usually also used for html templates. We can set it up in both dev and prod modes with different options - we can use templete functionality in both modes,but add minification only in production.

In webpack.dev.js - no minification, only template:

    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/template.html"
        })
    ],

In webpack.prod - both template and minification (it can take various options, like removing comments, whitespaces and so on):

    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/template.html",
            minify: {
                removeAttributeQuotes: true,
                collapseWhitespace: true,
                removeComments: true
            }
        })
    ],