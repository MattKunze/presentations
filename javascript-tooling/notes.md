# JavaScript Tooling

* Development vs production environments
* Manage dependencies
* Use fancy new features, languages, etc
* Script common tasks

## Node

[https://nodejs.org](https://nodejs.org)

* JavaScript on the server-side
* Runs all the tools we're going to talk about

## NPM

[https://www.npmjs.com/](https://www.npmjs.com/)

* Manages packages and dependencies

### npm init

* Generates a *package.json* file

### npm install

* `--save` and `--save-dev` flags persist into *package.json*

### module.exports

* exposes the public interface of your module

```javascript
var context = {...}
function doSomething() {
  // ....
}

module.exports = {
  doSomething: doSomething,
  version: 'whatever'
}
```

and then to use it:

```javascript
var test = require('./testmodule')
test.doSomething()
```

### npm run scripts

* let's you `npm run <script>` to automate common tasks

```javascript
{
  "name": "test-module",
  "author": "You",
  "description": "magical module that does fantastic things",
  "scripts": {
    "clean": "gulp clean",
    "test": "karma start --single-run"
  }
}
```

## Webpack

[https://webpack.github.io/](https://webpack.github.io/)

* Application assembler and compiler
* Loaders and plug-ins
  * process other languages/variants (ES6, CoffeeScript, TypeScript, JSX, etc)
  * wrap environment, manage globals and imports
  * linting, minification, code splitting
  * manage and package non-JS resources

### webpack.config.js

`npm install -g webpack`

Create a script that exports the configuration to use:

```javascript
module.exports = {
  entry: __dirname + '/src/main.js',
  output: {
    path: __dirname + '/build',
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
    ]
  }
}
```

### webpack-dev-server

[https://webpack.github.io/docs/webpack-dev-server.html](https://webpack.github.io/docs/webpack-dev-server.html)

* Simple express-based server to run code while developing
* Live reloading
* Hot module replacement

## Gulp

[http://gulpjs.com/](http://gulpjs.com/)

* Task runner and more (Make, Rake, etc)
* Uses node streams extensively
* Tons of plugins and modules to add additional behavior

## Yeoman

[http://yeoman.io/](http://yeoman.io/)

* Community-driven templates for a wide variety of stacks
* Generates initial project structure
* Shortcuts for creating new modules when applicable
