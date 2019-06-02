# Learning Webpack
[Concepts](https://webpack.js.org/concepts)

## Installation
[Installation](https://webpack.js.org/guides/installation)
[Getting Started](https://webpack.js.org/guides/getting-started)
- Node.js must be installed
- Lets initiate a package.json: `npm init -y`
- Local installation: `npm install --save-dev webpack`
- Command Line Interface (CLI) installation: `npm install webpack-cli --save-dev` -> this is necessary to make webpack actions in our terminal

## Our files
- We add an html and js to our directory structure:
  - /webpack-test
    - package.json
    - index.html
    - styles.css
    - index.js

## Adjusting package.json
We'll mark our package as private and remove the main entry to prevent accidental publishing of our starter files code.

```JS
{
  "name": "webpack-test",
  "version": "1.0.0",
  "description": "[Concepts](https://webpack.js.org/concepts)",
  "private": true, // <-
  // "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/gabysantosw/webpack-test.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/gabysantosw/webpack-test/issues"
  },
  "homepage": "https://github.com/gabysantosw/webpack-test#readme",
  "devDependencies": {
    "webpack": "^4.32.2"
  }
}
```

## Structuring our bundle
- we need to separate our project files into a source files folder(main code) and a distribution folder (final code, outputted by webpack)
- webpack-test
  - package.json
  -/src
    - index.html
    - styles.css
    - index.js
  - /dist

## Ignoring git files
We add a .gitignore for /node_modules since we don't want or need to add them on github

## Outputting to dist folder
- Running webpack: `npx webpack` will output our code into the /dist
- so far this will only generate a main.js from our index.js
- This main.js results in a transpiled js (so older browsers can use it)

## Why index.js?
By default webpack takes an `/src/index.js` as the input file, this can be changed in our webpack's configuration

## Configuration
Lets create a webpack.config.js

`webpack.config.js`
```JS
const path = require('path'); // current directory

module.exports = {
  entry: './src/index.js', // our entry file
  output: {
    filename: 'main.js', // output files
    path: path.resolve(__dirname, 'dist') // setting all outputs into the dist folder
  }
};
```

## Output html file
We'll need a plugin for that: `npm install --save-dev html-webpack-plugin`
But running `npx webpack` won't create that html yet, we need to add the plugin into our configuration:
1. Add `const HtmlWebpackPlugin = require('html-webpack-plugin');` to setup the plugin
2. The module.exports also has a `plugins` property, there we will set it to `new HtmlWebpackPlugin()`, we wrap it up into an array for readability and to add more plugins later
3. Then we set our input html (`src/index.html`) and output html (we want it to be `dist/index.html`)

`webpack.config.js`
```JS
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin'); // referencing the plugin

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [ // plugin property
    new HtmlWebpackPlugin({ // setting up the plugin
      filename: 'index.html', // this is the default
      template: './src/index.html'
    })
  ]
};
```

## Adding our css
- Install with: `npm install --save-dev css-loader` -> this loader will turn all our referenced styles into a string
- Install with: `npm install style-loader --save-dev` -> this loader takes 'css-loader' string, wraps it around a style tag and injects it into our /dist/index.html
- We add a module property with an object with a property of rules
- the rule property will contain another object inside
- we set two more properties: test and use
- test needs a regex where we specify which files this loader will process, in this case we want css: `/\.css$/`
- use sets which loaders will work with our test files (css), we set it to an array with 'style-loader' in it, its an array because we'll likely add more loaders for css later
- For style-loader to trigger, it needs a reference in our index.js

```JS
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: { // module property
    rules: [ // to set the rules
      {
        test: /\.css$/, // setting which files will be processed by upcoming loaders
        use: [ // declare what loaders to use with above files
          'css-loader', // to get styles from references
          'style-loader' // to inject styles given by 'css-loader' into a style tag in the resulting html file
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: './src/index.html'
    })
  ]
};
```

## Development mode
- we add this mode into our config, so each time we run it there's no warning.

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: 'development', // setting mode
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      },
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: './src/index.html'
    })
  ]
};
```

## Installing Sass
- `npm install node-sass --save-dev`
- Webpack loader: `npm install sass-loader --save-dev`

```js
{
  test: /\.scss$/,
  use: [
      "style-loader", // creates style nodes from JS strings
      "css-loader", // translates CSS into CommonJS
      "sass-loader" // compiles Sass to CSS, using Node Sass by default
  ]
}
```

We also need to update the index.js with .scss

## Babel
- Install babel: `npm install @babel/core @babel/preset-env --save-dev`
- Install babel loader: `npm install babel-loader --save-dev`
- Create .babelrc file with:

```js
{
  "presets": ["@babel/preset-env"]
}
```

```js
// add this rule
{
  test: /\.m?js$/,
  exclude: /(node_modules|bower_components)/,
  use: {
    loader: 'babel-loader',
    options: {
      presets: ['@babel/preset-env']
    }
  }
}
```


## Watch mode
For webpack to check for changes and automatically recompile: we run `npx webpack --watch`, use ctrl+C to stop it
