
# :package: Webpack Notes

## Install

```
npm i -D webpack webpack-cli
```

## Initial Config

> webpack.config.js

```js
const path = require('path');
module.exports = {
  mode: 'development',
  devtool: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

## Initial File Structure

```
.
|- node_modules
|- src
| - index.js
| - main.css
|- .gitignore
|- package.json
|- webpack.config.js
```

## NPM Script

> package.json

```json
"scripts": {
    "start": "webpack --config webpack.config.js"
  },
```

## Loaders

Example: CSS

> webpack.config.js

```js
const path = require('path');
module.exports = {
  mode: 'development',
  devtool: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
};
```

Note: use goes in reverse order, 'css-loader' would run first, 'style-loader' second.

Note: "css-loader" turns CSS to JS, "style-loader" injects into DOM

## SCSS and Bootstrap Example

```
npm i -D sass-loader node-sass bootstrap
```

```
.
|- node_modules
|- src
| - index.js
**| - main.scss**
|- .gitignore
|- package.json
|- webpack.config.js
```

> webpack.config.js

```js
const path = require('path');
module.exports = {
  mode: 'development',
  devtool: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      }
    ]
  }
};
```

note: "sass-loader" turns SCSS to CSS

> main.scss

```scss
$primary: gold;
$danger: silver;
@import ' ~bootstrap/scss/bootstrap';
```

## Cache Busting

Use content hash to prevent browser from displaying outdated cached version of files.
Content hash - MD5 hash generated based on code content and appended to file name.

> webpack.config.js

```js
const path = require('path');
module.exports = {
  mode: 'development',
  devtool: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main-[contentHash].js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      }
    ]
  }
};
```

## Plugins

Example: HTMLWebpackPlugin
Simplifies create of HTML files to serve your webpack bundles. This is especially useful for webpack bundles that inclde a hash in the filename (see Cache Busting) which changes every compilation.

```
npm i -D html-webpack-plugin
```

> webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: 'development',
  devtool: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main-[contentHash].js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [new HtmlWebpackPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
};
```
