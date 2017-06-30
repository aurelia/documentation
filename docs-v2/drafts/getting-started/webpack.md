# Webpack

Aurelia supports a multitude of module loaders, namely; JSPM, RequireJS and Webpack. In this article, we are going to be going through the different ways you can get an Aurelia Webpack application up and running.

Until the CLI supports generating Webpack applications you can either use one of the official Webpack skeleton applications and tailor it to your needs or you can create an Aurelia Webpack application from scratch. Both approaches are documented below.

If you're confused as to which one you should choose, the skeleton applications are a great starting point as they require the least amount of effort in getting something functional. If you want to configure every aspect of the process, then going from scratch gives you more flexibility.

## From the skeleton

The benefit of basing your Webpack application on the skeleton is you have a functional Aurelia application out-of-the-box configured to work with your chosen format: Javascript or TypeScript. The skeleton projects also come with support for CSS and other formats out of the box.

The downside of the skeleton is the defaults might not reflect your needs and the skeleton applications come with additional dependencies such as; Bootstrap, Font Awesome and jQuery. Unless you require these, you'll want to remove them.

### The two choices that you have to choose from are

[skeleton-esnext-webpack](https://github.com/aurelia/skeleton-navigation/tree/master/skeleton-esnext-webpack) A Webpack application configured for working with Javascript and the popular Babel transpiler for transpiling your code to ES5. Comes with sane Webpack defaults and support for CSS, HTML, Fonts and images.

[skeleton-typescript-webpack](https://github.com/aurelia/skeleton-navigation/tree/master/skeleton-typescript-webpack) A Webpack application configured for working with TypeScript (which also serves as the transpiler). Comes with sane Webpack defaults and support for CSS, HTML, Fonts and images.

Unfortunately, individual skeletons cannot be downloaded via Github. To ensure that you get the latest and most stable skeleton, head over to the releases section [here](https://github.com/aurelia/skeleton-navigation/releases) and download a ZIP file of the latest release (which will contain all skeletons).

**TIP:** Downloading officially tagged releases ensures that you are getting a stable and tested version of the Webpack skeleton.

**IMPORTANT:** This section assumes that you have installed Node.js and Npm on your machine. To install the required dependencies, you will need to make sure you have already done this prior to following along with the below steps.

### Download and extract

Assuming that you have downloaded the ZIP file from the releases section and saved it somewhere locally, you'll want to open this and copy and the contents of the skeleton that you want to use. If you want to code using TypeScript, then pick the skeleton for TypeScript, otherwise pick the esnext one.

Extract the contents of the ZIP file and specific folder you want to base your application on somewhere locally on your machine.

Depending on your operating system, you'll want to open up Terminal if you're using macOS or a a Linux distro or if you are a Windows user, opening up either Command Prompt or PowerShell (either as an administrator). Then navigate to the location where you extracted your chosen skeleton.

### Installing dependencies

To install the dependencies all you need to type is: `npm install` which will pull down all of the dependencies required. Depending on your network speed and machine, this could take a while.

### Run the application

Once the dependencies have installed, to run the application type: `npm start` which will spin up a Webpack development server and expose your application at: `http://localhost:8080`. Have a click around and you'll see the router in action, fetching data via Aurelia Fetch Client and child routers.

### Running tests

All Aurelia skeletons come pre-configured to support unit tests as well as end-to-end tests.

- `npm test` runs unit tests using Karma
- `npm run e2e` runs end-to-end tests using Protractor

## From Scratch

If the idea of a kitchen sink starting base you have to clean does not sound appealing to you, you can also create an Aurelia application from scratch. The downside to this is you'll need to do a bit of work to get it to support CSS, images and the like (which we can copy from the skeleton).

The upside of going from scratch, is you get to choose what you want your application to include. There is nothing to remove, only add and the biggest one of them all is using the latest releases of Aurelia libraries such as Aurelia Webpack Plugin.

We will be using existing aspects from the skeletons to save time here and the code is included in the appropriate sections below. What the following from scratch sections will give you is an Aurelia application with support for routing, Bluebird for promises and polyfills for missing browser API's and features needed to support IE10 and up.

**BEFORE YOU PROCEED:** In the skeleton you get support for fonts, unit testing, end-to-end testing and other aspects we will not be detailing below. Keep this in mind if you want to support testing and other file formats. For reference, see the skeleton application if you would like to add in support for testing and additional file formats.

### Webpack + Babel

If you want a starting base that allows you to write modern Javascript and then use Babel to transpile it to ES5, this is the section you want. If you want to develop an Aurelia application using TypeScript and Webpack see the section below, "Webpack + Typescript".

#### Create a project folder

Create a new folder somewhere and call it whatever you like. For the purposes of this guide, call it `demo`.

#### Initialize our app

Open up a terminal window and type: `npm init` and fill out the required pieces of information such as author and description. This will create a `package.json` file where our dependencies will be saved.

#### Install development dependencies

Now, we need to install our required development dependencies. Namely, Babel and the Aurelia Webpack plugin. 

In the same directory, run:

```shell
npm install aurelia-webpack-plugin babel-core babel-loader babel-plugin-transform-class-properties babel-plugin-transform-decorators-legacy babel-polyfill babel-preset-env css-loader html-webpack-plugin style-loader webpack webpack-dev-server --save-dev
```

The above long Npm install command will download and save the following dependencies into the `devDependencies` section of `package.json`:

- **aurelia-webpack-plugin:** configures Webpack to be aware of Aurelia's libraries
- **babel-core:** the core of the babel transpiler
- **babel-loader:** a Webpack loader for using Babel with Javascript files
- **babel-plugin-transform-class-properties:** adds support for class properties in app classes
- **babel-plugin-transform-decorators-legacy:** adds support for decorators
- **babel-polyfill:** polyfills missing browser features and adds support for async/await via regeneratorRuntime
- **babel-preset-env:** a smart Babel plugin which determines based on your supported browsers configuration which polyfills and features to include
- **css-loader:** a Webpack loader for working with CSS imports and requires from inside of your Aurelia applications
- **html-webpack-plugin:** a Webpack plugin responsible for placing generated bundles into your main index file
- **style-loader:** takes CSS and places it inline
- **webpack:** responsible for bundling, minification, module loading and everything else in between
- **webpack-dev-server:** a fully-featured Webpack development server that allows you to run your Aurelia application locally, watches files and recompiles on the fly

#### Install dependencies

Lastly, we need application dependencies, which are mostly just Aurelia framework modules used for templating, routing and other aspects. 

```shell
npm install --save aurelia-bootstrapper aurelia-framework aurelia-history-browser aurelia-loader-webpack aurelia-logging-console aurelia-pal-browser aurelia-polyfills aurelia-router aurelia-templating aurelia-templating-binding aurelia-templating-resources aurelia-templating-router bluebird
```

We don't need to go through any of these, as they're all self-explanatory Aurelia dependencies, with exception of Bluebird.

#### Add Npm scripts for dev server and production build

Open up `package.json` and add in the following scripts section:

```json
"scripts": {
    "start": "webpack-dev-server --inline",
    "build": "webpack -p --progress --profile --colors"
}
```

This will allow us to type `npm start` to spin up a development server which will create a running site at `http://localhost:8080` or perform a production ready build by running `npm run build` which will output a functional application to the `dist` folder.

#### Create a Webpack configuration file

Let's create our `webpack.config.js` file in the root directory alongside `package.json`. This file is based on the `skeleton-esnext-webpack` configuration file, with a few things ommited and simplified.

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { AureliaPlugin } = require('aurelia-webpack-plugin');
const { optimize: { CommonsChunkPlugin }, ProvidePlugin } = require('webpack');

module.exports = {
  resolve: {
    extensions: ['.js'],
    modules: ['src', 'node_modules'],
  },

  entry: {
    main: ['aurelia-bootstrapper'],
    vendor: ['core-js/es6']
  },

  output: {
    path: path.resolve(__dirname, 'dist'),
    publicPath: '/',
    filename: '[name].js',
    chunkFilename: '[name].js'
  },

  devServer: {
    contentBase: path.resolve(__dirname, 'dist'),
    historyApiFallback: true,
  },

  module: {
    rules: [
      {
        test: /\.css$/i,
        issuer: [{ not: [{ test: /\.html$/i }] }],
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.css$/i,
        issuer: [{ test: /\.html$/i }],
        use: ['css-loader']
      },
      {
        test: /\.html$/i,
        loader: 'html-loader'
      },
      {
        test: /\.js$/i
        loader: 'babel-loader',
        exclude: 'node_modules'
      },
    ]
  },

  plugins: [
    new AureliaPlugin(),
    new ProvidePlugin({
      'Promise': 'bluebird'
    }),
    new HtmlWebpackPlugin({
        filename: 'index.html'
    })
  ]
};
```

#### Create an index.html file

Now we need an `index.html` file in the root for our app to bootstrap itself into. There is not really anything to this, except the `aurelia-app="main"` attribute on the body. This is for Aurelia to bootstrap itself into the body element.

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
  </head>
  <body aurelia-app="main">
  </body>
</html>
```

#### Configure Babel

Because we are using Babel, we need to add in a configuration file. In Babel 7, a new configuration format of `.babelrc.js` is introduced. Until Babel 7 is released, we have to add in two files. The old format simply includes the Javascript file meaning we can remove `.babelrc` when Babel 7 is released.

##### .babelrc.js

```javascript
// this file will be used by default by babel@7 once it is released
module.exports = {
  "plugins": [
    "transform-decorators-legacy",
    "transform-class-properties"
  ],
  "presets": [
    [
      "env", {
        "targets": {
          "browsers": [
            "last 2 versions",
            "not ie < 10"
          ],
          "uglify": process.env.NODE_ENV === 'production',
        },
        "loose": true,
        "modules": false,
        "useBuiltIns": true
      }
    ]
  ]
}
```

##### .babelrc

```dotfile
{
  "presets": [
    "./.babelrc.js"
  ]
}
```

#### Lastly, create some source files

We have a functional starting base, but if you were to try and run it nothing would happen. In-fact, you'd get an error because we have no main entry point, which we specified on the `aurelia-app` attribute on our body in `index.html`.

Let's create a new folder called `src` in the root of our application. Now we want to create a file called `main.js` (the main part aurelia-app refers to) and put in the following:

##### src/main.js

```javascript
import { Aurelia } from 'aurelia-framework';
import { PLATFORM } from 'aurelia-pal';
import 'babel-polyfill';

// remove out if you don't want a Promise polyfill (remove also from webpack.config.js)
import * as Bluebird from 'bluebird';
Bluebird.config({ warnings: { wForgottenReturn: false } });

export async function configure(aurelia) {
  aurelia.use
    .standardConfiguration()
    .developmentLogging();

  await aurelia.start();
  await aurelia.setRoot(PLATFORM.moduleName('app'));
}
```

This file is taken from the `skeleton-esnext-webpack` skeleton. In here we are bootstrapping the Aurelia application itself. But, we also include the `babel-polyfill` which polyfills missing browser features.

That `aurelia.use` line and subsequent function calls set up some default settings for our application like router and so on. You can get more verbose with these, but that is for a different article.

The last two lines are where the magic happens. We call the `aurelia.start` method which bootstraps Aurelia and gets the framework ready. Lastly, we tell Aurelia where the root entry file to our app is that handles rendering. In this case, it's `src/app.js` which we will now create.

##### src/app.js

```javascript
import { inject, PLATFORM } from 'aurelia-framework';
import { Router } from 'aurelia-router';

@inject(Router)
export class App {
  configureRouter(config, router) {
    this.router = router;

    config.map([
      { route: '', name: 'home', moduleId: PLATFORM.moduleName('home', 'home') },
    ]);
  }
}
```

This is our main rendering entry point for our Aurelia application. The `configureRouter` method is automatically called via the `aurelia-router` module and sets up our routes. We are defining just one route pointing to a file called `src/home.js` (which we need to create).

Before we continue, it is worth noting that we are passing `PLATFORM.moduleName('home', 'home')` to `moduleId` and this is very important. The `PLATFORM.moduleName` method is used to tell Webpack about dynamic dependencies. The first argument is the name of the file and the second (optional) argument is the name of a separately produced bundle which is lazy loaded.

We'll get into lazy loading in a different section. For now, keep this in mind as it is something you'll need to remember when dealing with dynamic dependencies in an Aurelia Webpack application.

Because Aurelia by default convention assumes all view-models have accompanying view files, we need to create a `src/app.html` file.

##### src/app.html

```html
<template>
  <h1>Aurelia Webpack</h1>
  <router-view></router-view>
</template>
```

A pretty basic view with a heading and `<router-view>` tags. The `router-view` element is where our routed view-model/view pairs are injected.

Lastly, we need to create our `src/home.js` and `src/home.html` files that are referenced inside of `app.js` for our home route.

##### src/home.js

```javascript
export class Home {

}
```

##### src/home.html

```html
<template>
    <h1>Welcome home</h1>
    <p>The homepage of your brand new Aurelia application using Webpack.</p>
</template>
```

#### Run the application

Assuming that everything went to plan above, to run the application type: `npm start` which will spin up a Webpack development server and expose your application at: `http://localhost:8080`. You won't see anything overly exciting, but you should see a functional Aurelia application with some text.

### Webpack + TypeScript

This section details getting an Aurelia application up and running from scratch with Webpack and TypeScript. Because TypeScript is a compiler and handles features such as async/await, we don't need a transpiler such as Babel. This results in an Aurelia application not needing many dependencies to work.

You will notice there is overlap between setting up a TypeScript project from scratch and a Babel application. The main difference being the lack of Babel (which makes things cleaner and more straightforward).

#### Create a project folder

Create a new folder somewhere and call it whatever you like. For the purposes of this guide, call it `demo`.

#### Initialize our app

Open up a terminal window and type: `npm init` and fill out the required pieces of information such as author and description. This will create a `package.json` file where our dependencies will be saved.

#### Install development dependencies

Now, we need to install our required development dependencies. The most important being TypeScript and the Aurelia Webpack plugin. 

In the same directory, run:

```shell
npm install aurelia-webpack-plugin awesome-typescript-loader css-loader html-webpack-plugin style-loader tslib typescript webpack webpack-dev-server --save-dev
```

The above long Npm install command will download and save the following dependencies into the `devDependencies` section of `package.json`:

- aurelia-webpack-plugin: configures Webpack to be aware of Aurelia's libraries
- awesome-typescript-loader: the loader for Webpack to work with our TypeScript files
- css-loader: a Webpack loader for working with CSS imports and requires from inside of your Aurelia applications
- html-webpack-plugin: a Webpack plugin responsible for placing generated bundles into your main index file
- style-loader: takes CSS and places it inline
- tslib: TypeScript library helpers (for cleaner produced code)
- typescript: the compiler which converts our TypeScript code to Javascript.
- webpack: responsible for bundling, minification, module loading and everything else in between
- webpack-dev-server: a fully-featured Webpack development server that allows you to run your Aurelia application locally, watches files and recompiles on the fly

#### Install dependencies

Lastly, we need application dependencies, which are mostly just Aurelia framework modules used for templating, routing and other aspects. 

Because the application dependencies are the same regardless of whether you choose Javascript or TypeScript, we can copy and paste the install command from the Javascript instructions above in, "Webpack + Babel".

```
npm install --save aurelia-bootstrapper aurelia-framework aurelia-history-browser aurelia-loader-webpack aurelia-logging-console aurelia-pal-browser aurelia-polyfills aurelia-router aurelia-templating aurelia-templating-binding aurelia-templating-resources aurelia-templating-router bluebird
```

#### Add Npm scripts for dev server and production build

Open up `package.json` and add in the following scripts section (taken from the Babel instructions above)

```json
"scripts": {
    "start": "webpack-dev-server --inline",
    "build": "webpack -p --progress --profile --colors"
}
```