# first of all init package.json file which will track all installed packages
npm init

# create .gitignore with following lines:

...
lines which will exclude node.js generated files (you can generate lines following by the link: https://www.toptal.com/developers/gitignore)
...

...
lines which will exclude vscode generated files (you can generate lines following by the link: https://www.toptal.com/developers/gitignore)
...

# create files and folders with following structure:
src
  main
    assets
      js
        index.js // you need to import scss file into js file to bundle it
        // et cetera
      scss
        style.scss
        // et cetera
      img
        asfsd.jpg
        // et cetera
      fonts
        dssdfsd.otf
        // et cetera
    index.html
  about
    assets
      js
        index.js // you need to import scss file into js file to bundle it
        // et cetera
      scss
        style.scss
        // et cetera
      img
        asfsd.jpg
        // et cetera
      fonts
        dssdfsd.otf
        // et cetera
    index.html
  // et cetera

# install following packages
npm install webpack webpack-cli webpack-dev-server html-loader html-webpack-plugin sass-loader node-sass css-loader mini-css-extract-plugin file-loader babel-loader @babel/core @babel/preset-env clean-webpack-plugin --save-dev

# add to package.json new scripts which you will use to start development server or bundle html, scss and js files
"build": "webpack --mode production",
"start": "webpack serve --mode development"

# create webpack.config.js file

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
  context: path.resolve(__dirname, 'src'),
  entry: {
    './': './assets/js/index.js',
    'about/': './about/assets/js/index.js'
    // et cetera
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name]index.js',
    publicPath: ''
  },
  devServer: {
    contentBase: path.resolve(__dirname, 'dist'),
    publicPath: ''
  },
  module: {
    rules: [
      {
        test: /\.html$/i,
        loader: 'html-loader',
        options: {
          attributes: {
            list: [
              // All default supported tags and attributes
              '...',
              {
                tag: 'link',
                attribute: 'href',
                type: 'src',
                filter: (tag, attribute, attributes, resourcePath) => {
                  if (attributes.rel && attributes.rel.trim().toLowerCase() === 'icon') {
                    return true;
                  }
                  return false;
                }
              }
            ]
          }
        }
      },
      {
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        loader: 'file-loader',
        options: {
          name: 'img.[contenthash].[ext]',
          outputPath: (url, resourcePath, context) => {
            let relativePath = path.relative(context, resourcePath);
            relativePath = relativePath.replace(/\\/g, '/');
            const substring = relativePath.substring(0, relativePath.lastIndexOf('/'));
            return `${substring}/${url}`;
          },
          publicPath: (url) => {
            return `assets/img/${url}`;
          },
        }
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/i,
        loader: 'file-loader',
        options: {
          name: 'font.[contenthash].[ext]',
          outputPath: (url, resourcePath, context) => {
            let relativePath = path.relative(context, resourcePath);
            relativePath = relativePath.replace(/\\/g, '/');
            const substring = relativePath.substring(0, relativePath.lastIndexOf('/'));
            return `${substring}/${url}`;
          },
          publicPath: (url) => {
            return `assets/fonts/${url}`;
          },
        }
      },
      {
        test: /\.scss$/i,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          }, 
          'css-loader',
          'sass-loader'
        ]
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      chunks: ['./']
    }),
    new HtmlWebpackPlugin({
      filename: 'about/index.html',
      template: 'about/index.html',
      chunks: ['about/']
    }),
    // et cetera

    new MiniCssExtractPlugin({
      filename: '[name]style.css'
    })
  ]
};

# to run development server use following command: (to stop server use "ctrl + c")
npm run start

# to bundle html, scss and js files use following command:
npm run build