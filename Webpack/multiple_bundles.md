## Multiple bundles

In production we might want to separte our own code (app code) from external (vendor) code, that is changed more often (during updates). In this example vendor.js will be an entry file for external code and index.js for our code.

We have to separate entry points in webpack.common.js - instead of passing singular string, we create an object:

Before:

    entry: "./src/index.js",

Now:

	entry: {
        	main: "./src/index.js",
        	vendor: "./src/vendor.js"
    	},

Still in webpack.common.js, we set up dynamic output file. "Name" can resolve to either "main" or "vendor".

    output: {
        filename: "[name].[contentHash].bundle.js",
        path: path.resolve(__dirname, "dist")
    },

Simillary, in webpack.dev.js:

    output: {
        filename: "[name].bundle.js",
        path: path.resolve(__dirname, "dist")
    }