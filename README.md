
# :package: Webpack Notes


<br><br>


## Table of Contents
1. [Install](#install)
1. [Initial Config](#initial-config)
1. [Initial File Structure](#initial-file-structure)
1. [NPM Script](#npm-scripts)
1. [Loaders](#loaders)
1. [SCSS and Bootstrap Example](#scss-and-bootstrap-example)
1. [Cache Busting](#cache-busting)
1. [Plugins](#plugins)
1. [Disambiguate](#disambiguate)
1. [Dev Server](#dev-server)
1. [HTML-loader, File-loader, & Clean-webpack](#html-loader-file-loader-and-clean-webpack)
1. [Multiple Entrypoints and Vendor.js](#multiple-entrypoints-and-vendorjs)
1. [Extract CSS](#extract-css)
1. [Minify HTML, CSS, and JS](#minify-html-css-and-js)
1. [Package.json and Webpack.config.js Example](#packagejson-and-webpackconfigjs-example)
1. [Complete NPM Install](#complete-npm-install)
1. [Updated](#updated)


<br><br>


## Install

```
npm i -D webpack webpack-cli
```


<br><br>


## Initial Config

> :page_facing_up: webpack.config.js

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


<br><br>


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


<br><br>


## NPM Scripts

> :page_facing_up: package.json

```json
"scripts": {
    "start": "webpack --config webpack.config.js"
  },
```


<br><br>


## Loaders

Example: CSS

> :page_facing_up: webpack.config.js

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

:pushpin: Note: use goes in reverse order, 'css-loader' would run first, 'style-loader' second.

:pushpin: Note: "css-loader" turns CSS to JS, "style-loader" injects into DOM


<br><br>


## SCSS and Bootstrap Example

```
npm i -D sass-loader node-sass bootstrap
```

```
.
|- node_modules
|- src
| - index.js
| - main.scss <--
|- .gitignore
|- package.json
|- webpack.config.js
```

> :page_facing_up: webpack.config.js

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

:pushpin: Note: "sass-loader" turns SCSS to CSS

> :page_facing_up: main.scss

```scss
$primary: gold;
$danger: silver;
@import ' ~bootstrap/scss/bootstrap';
```


<br><br>


## Cache Busting

Use content hash to prevent browser from displaying outdated cached version of files.
Content hash - MD5 hash generated based on code content and appended to file name.

> :page_facing_up: webpack.config.js

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


<br><br>


## Plugins

Example: HTMLWebpackPlugin
Simplifies create of HTML files to serve your webpack bundles. This is especially useful for webpack bundles that inclde a hash in the filename (see Cache Busting) which changes every compilation.

```
npm i -D html-webpack-plugin
```

> :page_facing_up: webpack.config.js

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


<br><br>


## Disambiguate

Eventually you will find the need to disambiguate in your webpack.config.js between development and production builds.
See:

- https://webpack.js.org/configuration/mode/
- https://webpack.js.org/api/cli/#environment-options
- https://webpack.js.org/guides/environment-variables/
- https://webpack.js.org/configuration/configuration-types/

> :page_facing_up: package.json

```json
  "scripts": {
    "start": "webpack --env.development --config webpack.config.js",
    "build": "webpack --env.production --config webpack.config.js"
  },
```

:pushpin: Note: can also add --mode=development or --mode=production

> :page_facing_up: webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

let config = {
  entry: './src/index.js',
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

module.exports = env => {
  if (env.development == true) {
    config.mode = 'development';
    config.devtool = 'none';
    config.output = {
      filename: 'main.js',
      path: path.resolve(__dirname, 'dist')
    };
  }

  if (env.production === true) {
    config.mode = 'production';
    config.output = {
      filename: 'main-[contentHash].js',
      path: path.resolve(__dirname, 'dist')
    };
  }

  return config;
};
```


<br><br>


## Dev Server

```
npm i -D webpack-dev-server
```

> :page_facing_up: package.json

```json
"scripts": {
    "start": "webpack-dev-server --open --env.development --config webpack.config.js",
```


<br><br>


## HTML-loader, File-loader, and Clean-webpack

By default every local `<img src="image.png">` is required (require('./image.png')). You may need to specify loaders for images in your configuration (recommended file-loader or url-loader).

```
npm i -D html-loader file-loader
```

> :page_facing_up: webpack.config.js

```js
let config = {
  entry: './src/index.js',
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/template.html'
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.(svg|png|jpg|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: '[name]-[hash].[ext]',
            outputPath: 'imgs'
          }
        }
      }
    ]
  }
};
```

In general it's good practice to clean the /dist folder before each build, so that only used files will be generated.

```
npm i -D clean-webpack-plugin
```

> :page_facing_up: webpack.config.js

```js
const CleanWebpackPlugin = require('clean-webpack-plugin');

...

  if (env.production === true) {
    config.mode = 'production';
    config.output = {
      filename: 'main-[contentHash].js',
      path: path.resolve(__dirname, 'dist')
    };
    config.plugins = [new CleanWebpackPlugin()];
  }

  return config;
};
```


<br><br>


## Multiple Entrypoints and Vendor.js

```
.
|- node_modules
|- src
| - index.js
| - main.scss
| - template.html
| - vendor.js <--
|- .gitignore
|- package.json
|- webpack.config.js
```

> :page_facing_up: webpack.config.js

```js
let config = {
  entry: {
    main: "./src/index.js",
    vendor: "./src/vendor.js"
  },

...

module.exports = env => {

  if (env.development == true) {
    config.mode = 'development';
    config.devtool = 'none';
    config.output = {
      filename: "[name].bundle.js",
      path: path.resolve(__dirname, "dist")
    };
  }

  if (env.production === true) {
    config.mode = 'production';
    config.output = {
      filename: "[name]-[contentHash].bundle.js",
      path: path.resolve(__dirname, "dist")
    };
    config.plugins = [new CleanWebpackPlugin()];
  }

  return config;
};
```

Example: Bootstrap

```
npm i -D bootstrap jquery popper.js
```

> :page_facing_up: vendor.js

```js
import 'bootstrap';
```

:pushpin: Note: can remove bootstap import in scss


<br><br>


## Extract CSS

```
npm i -D mini-css-extract-plugin
```

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

let config = {
  entry: {
    main: './src/index.js',
    vendor: './src/vendor.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/template.html'
    })
  ],
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.(svg|png|jpg|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: '[name]-[hash].[ext]',
            outputPath: 'imgs'
          }
        }
      }
    ]
  }
};

module.exports = env => {
  if (env.development == true) {
    config.mode = 'development';
    config.devtool = 'none';
    config.output = {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    };
    config.module.rules = [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      }
    ];
  }

  if (env.production === true) {
    config.mode = 'production';
    config.output = {
      filename: '[name]-[contentHash].bundle.js',
      path: path.resolve(__dirname, 'dist')
    };
    config.plugins = [
      new MiniCssExtractPlugin({ filename: '[name]-[contentHash].css' }),
      new CleanWebpackPlugin()
    ];
    config.module.rules = [
      {
        test: /\.scss$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      }
    ];
  }

  return config;
};
```


<br><br>


## Minify HTML, CSS, and JS

```
npm i -D optimize-css-assets-webpack-plugin
```

:pushpin: Note: terser-webpack-plugin does NOT need to be installed, it is bundled with webpack by default

:pushpin: Note: removed HTMLWebpackPlugin from common to development and production; this allows for different settings (minification in production)

> :page_facing_up: webpack.config.js

```js
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');

...

let config = {
  entry: {
    main: "./src/index.js",
    vendor: "./src/vendor.js"
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.(svg|png|jpg|gif)$/,
        use: {
          loader: "file-loader",
          options: {
            name: "[name]-[hash].[ext]",
            outputPath: "imgs"
          }
        }
      }
    ]
  }
};

module.exports = env => {

  if (env.development == true) {
    config.mode = 'development';
    config.devtool = 'none';
    config.output = {
      filename: "[name].bundle.js",
      path: path.resolve(__dirname, "dist")
    };
    config.plugins = [new HtmlWebpackPlugin({
      template: './src/template.html'
    })];
    config.module.rules = [{
      test: /\.scss$/,
      use: ['style-loader', 'css-loader', 'sass-loader']
    }];
  };

  if (env.production === true) {
    config.mode = 'production';
    config.output = {
      filename: "[name]-[contentHash].bundle.js",
      path: path.resolve(__dirname, "dist")
    };
    config.plugins = [
      new MiniCssExtractPlugin({ filename: "[name]-[contentHash].css" }),
      new CleanWebpackPlugin(),
      new OptimizeCssAssetsPlugin(),
      new TerserPlugin(),
      new HtmlWebpackPlugin({
        template: './src/template.html',
        minify: {
          removeAttributeQuotes: true,
          collapseWhitespace: true,
          removeComments: true
        }
      })
    ];
    config.module.rules = [{
      test: /\.scss$/,
      use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
    }];
  };

  return config;
};
```


<br><br>


## Package.json and Webpack.config.js Example

> :page_facing_up: package.json

```json
{
  "name": "name",
  "version": "1.0.0",
  "private": true,
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server --open --env.development --config webpack.config.js",
    "build": "webpack --env.production --config webpack.config.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "bootstrap": "^4.3.1",
    "clean-webpack-plugin": "^3.0.0",
    "css-loader": "^3.2.0",
    "file-loader": "^4.2.0",
    "html-loader": "^0.5.5",
    "html-webpack-plugin": "^3.2.0",
    "jquery": "^3.4.1",
    "mini-css-extract-plugin": "^0.8.0",
    "node-sass": "^4.12.0",
    "optimize-css-assets-webpack-plugin": "^5.0.3",
    "popper.js": "^1.15.0",
    "sass-loader": "^8.0.0",
    "style-loader": "^1.0.0",
    "webpack": "^4.40.2",
    "webpack-cli": "^3.3.9",
    "webpack-dev-server": "^3.8.1"
  }
}
```

> :page_facing_up: webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');

let config = {
  entry: {
    main: './src/index.js',
    vendor: './src/vendor.js'
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.(svg|png|jpg|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: '[name]-[hash].[ext]',
            outputPath: 'imgs'
          }
        }
      }
    ]
  }
};

module.exports = env => {
  if (env.development == true) {
    config.mode = 'development';
    config.devtool = 'none';
    config.output = {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    };
    config.plugins = [
      new HtmlWebpackPlugin({
        template: './src/template.html'
      })
    ];
    config.module.rules = [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      }
    ];
  }

  if (env.production === true) {
    config.mode = 'production';
    config.output = {
      filename: '[name]-[contentHash].bundle.js',
      path: path.resolve(__dirname, 'dist')
    };
    config.plugins = [
      new MiniCssExtractPlugin({ filename: '[name]-[contentHash].css' }),
      new CleanWebpackPlugin(),
      new OptimizeCssAssetsPlugin(),
      new TerserPlugin(),
      new HtmlWebpackPlugin({
        template: './src/template.html',
        minify: {
          removeAttributeQuotes: true,
          collapseWhitespace: true,
          removeComments: true
        }
      })
    ];
    config.module.rules = [
      {
        test: /\.scss$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      }
    ];
  }

  return config;
};
```


<br><br>


## Complete NPM Install

```
npm i -D clean-webpack-plugin css-loader file-loader html-loader html-webpack-plugin mini-css-extract-plugin node-sass optimize-css-assets-webpack-plugin sass-loader style-loader webpack webpack-cli webpack-dev-server
```


<br><br>


## Updated

> :page_facing_up: webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  mode: 'production',
  entry: {
    './': path.resolve(__dirname, 'src/index.js'),
    'folder/address': path.resolve(__dirname, 'src/address/index.js'),
  },
  output: {
    filename: '[name]js/script.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    new MiniCssExtractPlugin({ filename: '[name]css/styles.css' }),
    new CleanWebpackPlugin(),
    new OptimizeCssAssetsPlugin(),
    new TerserPlugin(),
    new HtmlWebpackPlugin({
      template: './src/index.html',
      filename: 'index.html',
      // favicon: './src/favicon.ico',
      chunks: ['./'],
      minify: {
        removeAttributeQuotes: true,
        collapseWhitespace: true,
        removeComments: true
      }
    }),
    new HtmlWebpackPlugin({
      template: './address/index.html',
      filename: './address/index.html',
      favicon: './address/favicon.ico',
      chunks: ['chunkname'],
      minify: {
        removeAttributeQuotes: true,
        collapseWhitespace: true,
        removeComments: true
      }
    })
  ],
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      },
      {
        test: /\.(svg|png|jpg|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: '[name].[ext]',
            outputPath: 'img'
          }
        }
      }
    ]
  }
}
```


