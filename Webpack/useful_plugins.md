## Various useful Webpack plugins

### HtmlWebpackPlugin

HtmlWebpackPlugin can be used to generate .html output template with automatically added .js and .css outputs.

    npm install --save-dev html-webpack-plugin

In webpack.config.js:

    const HtmlWebpackPlugin = require('html-webpack-plugin');

    module.exports = {
    	...
        plugins: [new HtmlWebpackPlugin()],
    }

The above will generate very simple index.html inside /dist. We can also generate this with our own template:

    plugins: [new HtmlWebpackPlugin({
        template: "./src/template.html"
    })],

### CleanWebpackPlugin

If we use content hash to generate new versions of scripts, we can use CleanWebpackPlugin to clear old version inside /dist, each time we run 'npm run build'. This plugin is used only in production (so don't use --save-dev flag) and is added to webpack.prod.js.

    npm install --save clean-webpack-plugin

    const { CleanWebpackPlugin } = require('clean-webpack-plugin');
    
    module.exports = merge(common, {
    	...
        plugins: [new CleanWebpackPlugin()]
    });