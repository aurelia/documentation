# Get Started

**Welcome to Aurelia!** This tutorial will take you through creating a simple application using Aurelia and briefly explain its main concepts. We assume you are familiar with JavaScript, HTML, and CSS. To get a quick overview, we recommend you skip down to the section titled "Setting Up The HTML Page" so you can see how to use Aurelia straight away. Then, when you are ready to actually build something, come back and read about "Configuring Your Environment" and "Setting up the Project Structure and Build". To view the completed results of this tutorial, please have a look at our [navigation skeleton project](https://github.com/aurelia/skeleton-navigation/releases).

> **Note:** Looking for this guide in another language? Have a look in our [documentation repo](https://github.com/aurelia/documentation).

## Configuring Your Environment

Let's start by getting you set up with a great set of tools that you can use to build modern JavaScript applications. All our tooling is built on [Node.js](http://nodejs.org/). If you have that installed already, great! If not, you should go to [the official web site](http://nodejs.org/), download and install it. Everything else we need will be installed via Node's package manager ([npm](https://docs.npmjs.com/getting-started/what-is-npm)). If you already have npm installed, make sure you've got the [latest version](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) to avoid any issues with the other tools.

First, let's begin by installing [Gulp](http://gulpjs.com/) which we'll be using for build automation. If you don't have it already, you can use npm to set it up like this (you may need to use `sudo`):

  ```shell
  npm install -g gulp
  ```

Next, we need to install [jspm](http://jspm.io/). This will serve as our client-side package manager. You can do that like this:

  ```shell
  npm install -g jspm
  ```

> **Note:** jspm, like Bower and Yeoman, leverages [git](http://git-scm.com/) so you need to install that if you don't have it. Also, jspm queries GitHub to install packages, but GitHub has a rate limit on anonymous API requests. It is advised that you configure jspm with your GitHub credentials in order to avoid problems. You can do this by executing `jspm registry config github` and following the prompts. Don't want to use jspm? No problem. All Aurelia packages are available via [Bower](http://bower.io/) as well.

## Setting up the Project Structure and Build

With the tooling installed, we can now turn our attention to setting up a basic structure for your app. Begin by [downloading the navigation skeleton](https://github.com/aurelia/skeleton-navigation/releases). Unzip it and rename the folder to _navigation-app_.

> **Note:** Alternatively use can use [Yeoman](http://yeoman.io) to "generate" the skeleton project in the target folder, as follows:
>
>  ```
>  npm install -g yo generator-aurelia
>  yo aurelia
>  ```

Inside the folder you will now find everything you need including a basic build, package configuration, styles and more.

You may examine the provided _index.html_ file and the rest of the files in _src_, however we recommend deleting both before moving on with this tutorial. That way you can more effectively learn how to build an Aurelia app from scratch.

With all this in place, let's run some commands.

1. Open a console and change your directory to _navigation-app_.

2. Execute the following command to install the Gulp plugins listed in the _devDependencies_ section of the package manifest.

  ```shell
  npm install
  ```
3. Next, execute the following command to install the Aurelia library, bootstrap and font-awesome, listed in the _jspm.dependencies_ section of the package manifest.

  ```shell
  jspm install -y
  ```

Everything we've done so far is standard Node.js build and package management procedures. It doesn't have anything specific to do with Aurelia itself. We're just walking you through setting up a modern JavaScript project and build configuration from scratch. You may be familiar with this already, but if not then welcome to this new and exciting world!

> **Note:** Bootstrap and Font-Awesome are **not** dependencies of Aurelia. We only leverage them as part of this tutorial in order to quickly achieve a decent look out-of-the-box.

## Setting Up The HTML Page

If you've followed along this far, you now have all the libraries, build configuration and tools you need to create amazing JavaScript apps with Aurelia. The next thing to do is create the _index.html_ file in the root of your project folder. This example file provides a good template for new Aurelia-based apps.

### index.html
```markup
<!doctype html>
<html>
  <head>
    <title>Aurelia</title>
    <link rel="stylesheet" type="text/css" href="jspm_packages/npm/font-awesome@4.3.0/css/font-awesome.min.css">
    <link rel="stylesheet" type="text/css" href="styles/styles.css">
  </head>
  <body aurelia-app>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
     System.config({
       "paths": {
         "*": "dist/*.js"
       }
     });
   </script>
    <script>
      System.import('aurelia-bootstrapper');
    </script>
  </body>
</html>
```

Yes, that's it. This is the only HTML page in our application. The head of the document is pretty straight forward: we link in our font-awesome and custom stylesheets. It's the body that's interesting.

> **Note:** Be sure to confirm that the local folder name for font-awesome matches the link href. It's possible that these libraries have updated their versions since the authoring of this document.

Let's start with the script tags. First we have _system.js_, our ES6 standards-based module loader. It's what loads the Aurelia library as well as your own code. Next we have _config.js_. This contains configuration for the loader. It's generated automatically whenever you execute a jspm command. jspm is the client-side package manager we recommend because it provides an amazing developer experience by integrating client-side package management with an ES6 compliant module loader. Immediately under that we have a call to `System.config`. This sets up the output location of our compiled JavaScript code.

>**Note:** The Aurelia Framework isn't tied to jspm or SystemJS. We also support require-style APIs like RequireJS and Dojo Loader out of the box. Also, you can implement your own loader and handle package management any way you want. However we do think jspm/SystemJS is the best ES6-oriented solution today and it's our recommended approach.

Once we have our module loader and its configuration, we load the `aurelia-bootstrapper` module with a call to `System.import`.

When the bootstrapper loads it inspects the HTML document for _aurelia-app_ attributes. In this case it will find that the body has an `aurelia-app` attribute. This tells the bootstrapper to load our _app_ view-model and its view, conventionally located in _app.js_ and _app.html_ and then compose them as an Aurelia application in the DOM.

Wait a minute....we don't have an _app_ view-model or view. Ummm...WHAT NOW!?

## Creating Your First Screen

In Aurelia, user interface elements are composed of _view_ and _view-model_ pairs. The _view_ is written with HTML and is rendered into the DOM. The _view-model_ is written with JavaScript and provides data and behavior to the _view_. Aurelia's powerful _databinding_ links the two pieces together allowing changes in your data to be reflected in the _view_ and vice versa. This Separation of Concerns is great for developer/designer collaboration, maintainability, architectural flexibility and even source control.

Let's see how it works...

In the _src_ folder create an _app.html_ file and an _app.js_ file. This is the app view and view-model that the bootstrapper was looking for. Let's start with the _view-model_ by creating a simple class to hold a _firstName_ and _lastName_. We'll also add a computed property for _fullName_ and a method to "welcome" the person. Here's what that would look like:

### app.js
```javascript
export class Welcome{
  heading = 'Welcome to the Aurelia Navigation App!';
  firstName = 'John';
  lastName = 'Doe';

  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }

  welcome(){
    alert(`Welcome, ${this.fullName}!`);
  }
}
```

What...is that JavaScript?

Yes. Yes it is. In fact it's ECMAScript 7 (ES7), the next..next version of JavaScript which introduces many new features to the language. Fortunately the Gulp file you downloaded above has you set up with [Babel](https://babeljs.io/), an amazing transpiler that allows you to write tomorrow's JavaScript and run it on today's browsers. Now you can use modules, classes, lambdas, string interpolation and more. Sweet! So, how do you create a _view-model_? You create a plain class and _export_ it to the framework. Piece. Of. Cake.

> **Note:** You don't have to use Babel or even ES7 to write an Aurelia app. You can use languages like TypeScript and CoffeeScript...or today's browser language: ES5. All you have to do is follow the language's standard pattern for creating classes and everything will work fine. We think ES7 is awesome though and hope you will consider it first. To learn more about the newest version of JavaScript including features like module exports and classes we recommend reading through [The Babel Learning Guide](http://babeljs.io/docs/learn-es6/).

Ok. Now that we have a _view-model_ with some basic data and behavior, let's have a look at its partner in crime...the _view_.

### app.html
```markup
<template>
  <section>
    <h2>${heading}</h2>

    <form role="form" submit.delegate="welcome()">
      <div class="form-group">
        <label for="fn">First Name</label>
        <input type="text" value.bind="firstName" class="form-control" id="fn" placeholder="first name">
      </div>
      <div class="form-group">
        <label for="ln">Last Name</label>
        <input type="text" value.bind="lastName" class="form-control" id="ln" placeholder="last name">
      </div>
      <div class="form-group">
        <label>Full Name</label>
        <p class="help-block">${fullName}</p>
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>
  </section>
</template>
```

All views are contained within a `template` tag. This view is a basic input form, styled using bootstrap classes. Look at the input controls. Did you see `value.bind="firstName"`? That databinds the input's _value_ to the _firstName_ property in our view-model. Any time the view-model's property changes, the input will be updated with the new value. Any time you change the value in the input control, Aurelia will push the new value into your view-model. It's that easy.

There's a couple more interesting things in this example. In the last form group you can see this syntax in the HTML content: `${fullName}`. That's a string interpolation. It's a one-way binding from the view-model into the view that is automatically converted to a string and interpolated into the document. Finally, have a look at the form element itself. You should notice this: `submit.delegate="welcome()"`. That's an event binding. This uses event delegation to bind the _submit_ event so that it executes the _welcome_ method any time the form is submitted.

> **Note:** If you haven't heard of event delegation, it's a technique used to more efficiently handle events in browser by attaching a single event handler at the document level which handles all events of a type, rather than attaching event handlers to every node.

Let's run it and see this in action. On your console use the following command to build and launch the server.

```shell
gulp watch
```

You can now browse to [http://localhost:9000/](http://localhost:9000/) to see the app. Type in the form input controls and notice that the Full Name computed property updates with any change. Click the button and see that your method is invoked.

> **Note:** If this isn't working, try [updating](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) to the latest version of npm.

> **Note:** Aurelia has a unique and powerful databinding engine that uses adaptive techniques to pick the best way to observe changes in each property. For example, if you are using a browser with [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) support, both _firstName_ and _lastName_ will be observed with that strategy. If not, we'll generate getters and setters that batch changes to the Micro Task Queue, correctly emulating Object.observe behavior. Since the computed property _fullName_ can't be observed with either of these techniques, we use dirty checking. But, you can optionally declare its dependencies in order to enable us to observe it properly. We'll use the best technique depending on the situation and you can even plug in custom strategies as well in order to "teach" the framework how to observe special types of model patterns. We think it's pretty cool.

> **Note:** The `.bind` command uses the default binding behavior for any property. The default is one-way binding (model to view) for everything except form controls, which default to two-way. You can always override this by using the explicit binding commands `.one-way`, `.two-way` and `.one-time`. Similarly, you can use `.delegate` for event delegation but you can also use `.trigger` to attach directly to the target element.

## Adding Navigation

Since this is a navigation app, we should probably add some more screens and set up a client-side router, don't you think? Let's begin by renaming our _app.js_ and _app.html_ to _welcome.js_ and _welcome.html_ respectively. This will be the first page of our app. Now, lets create a new _app.js_ and _app.html_ which will serve as our "layout" or "master page". The view will contain our navigation UI and the content placeholder for the current page and the view-model will have a router instance, configured with our routes. We'll start with the view-model so you can see how to set up the router:

### app.js

```javascript
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome',  moduleId: './welcome',      nav: true, title:'Welcome' }
    ]);

    this.router = router;
  }
}
```

Ok, there's some really interesting new stuff here. We want to use the router, so we begin by creating our _App_ class and having it implement the `configureRouter` callback. You can set a title to use when generating the document's title. Then you map your routes. Each route has the following properties:

* `route`: This is a pattern which, when matched, will cause the router to navigate to this route. You can use static routes like above, but you can also use parameters like this: `customer/:id`. There's also support for wildcard routes and query string parameters. The route can be a single string pattern or an array of patterns.
* `name`: This is a name to use in code when generating URLs for the route.
* `moduleId`: This is a path relative to the current view-model which specifies the view/view-model pair you want to render for this route.
* `title`: You can optionally provide a title to be used in generating the document's title.
* `nav`: If this route should be included in the _navigation model_ because you want to generate a UI with it, set this to true (or a number indicating order).

> **Note:** Did you notice how we used ES6 imports to load both bootstrap's JavaScript and CSS?

### app.html

```markup
<template>
  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle Navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">
        <i class="fa fa-home"></i>
        <span>${router.title}</span>
      </a>
    </div>

    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
          <a href.bind="row.href">${row.title}</a>
        </li>
      </ul>

      <ul class="nav navbar-nav navbar-right">
        <li class="loader" if.bind="router.isNavigating">
          <i class="fa fa-spinner fa-spin fa-2x"></i>
        </li>
      </ul>
    </div>
  </nav>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```
Following our simple app-building convention, the `App` class will be databound to the above view in _app.html_. A large part of this markup deals with setting up the main navigation structure. You've seen basic binding and string interpolation already, so let's focus on the new stuff.  Take a look at the navbar-nav `ul` element. Its `li` demonstrates how to use a repeater with the following expression `repeat.for="row of router.navigation"`. This will create one `li` for each item in the `router.navigation` array. The local variable is _row_ and you can see that used throughout the `li` and its child elements.

> **Note:** The `navigation` property on the router is an array populated with all the routes you marked as `nav:true` in your route config. Aurelia models its `repeat.for` syntax after the new standard ES6 `for..of` loop. So, you can think of looping over the array of navigable routes and generating UI for each.

Also on the `li` you can see a demonstration of how to use string interpolation to dynamically add/remove classes. Further down in the view, there's a second `ul`. See the binding on its single child `li`? `if.bind="router.isNavigating"` This conditionally adds/removes the `li` based on the value of the bound expression. Conveniently, the router will update its `isNavigating` property whenever it is....navigating.

The last piece we want to look at is the `router-view` element near the bottom of the view. This represents the location in the DOM where the current "page" will be rendered, based on the configured router's state.

With this in place, go ahead and start the dev server with `gulp watch`. Open the browser and have a look. You should now see a main navigation with a single selected tab for our "welcome" route. The _welcome_ view should display in the main content area and function as before. Open up the browser's debug tools and have a look at the live DOM. You will see that the _welcome_ view content is displayed inside the `router-view`.

> **Note:** If you left your gulp watch task running during this time, you may have noticed your browser auto-refreshing whenever you made changes. That's thanks to `browser-sync`, which we've conveniently configured for you as part of the default gulp configuration.

## Adding a Second Page

Well, we've technically got a navigation application now...but it's not very interesting because there's only one page. Let's add a second page. Can you guess how to do it? I bet you can...

Let's display some images from Flickr. To do that, let's first configure our router for the hypothetical page:

### app.js (updated)

```javascript
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome',  moduleId: './welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',       name: 'flickr',   moduleId: './flickr',       nav: true, title:'Flickr' }
    ]);

    this.router = router;
  }
}
```

If you guessed that we need to create a _flickr.js_ and _flickr.html_ file, you are correct. Here's the source:

### flickr.js

```javascript
import {inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@inject(HttpClient)
export class Flickr{
  heading = 'Flickr';
  images = [];
  url = 'http://api.flickr.com/services/feeds/photos_public.gne?tags=rainier&tagmode=any&format=json';

  constructor(http){
    this.http = http;
  }

  activate(){
    return this.http.jsonp(this.url).then(response => {
      this.images = response.content.items;
    });
  }

  canDeactivate(){
    return confirm('Are you sure you want to leave?');
  }
}
```

There's a lot of cool stuff here. Let's start at the beginning. We are importing `HttpClient` from Aurelia. This lets us make HTTP requests in a very simple way. It's not included with the default Aurelia configuration though, so you need to install the package. To do that, execute this command on the console:

```shell
jspm install aurelia-http-client
```

Now I hope you see the power of the integrated package manager and loader. You simply install a package with jspm and then you import it in your code using the same exact identifier. You can install anything from GitHub or NPM in this way.

Now, take a look at that ES7 `inject` decorator? What does that do? Well, Aurelia creates the UI components as needed to render your app. It does this by using a [Dependency Injection](http://en.wikipedia.org/wiki/Dependency_injection) container capable of providing constructor dependencies like HttpClient. How does the DI system know what to provide? All you have to do is add that ES7 `inject` decorator to your class that passes a list of types to provide instances of. There should be one argument for each constructor parameter. In the above example, we needed an HttpClient instance, so we added the `HttpClient` type in the `inject` decorator and then added an corresponding parameter in the constructor.

> **Note:** If you don't like using a decorator in this case, you can also add a static `inject` method or property to the class that returns an array of types to inject.

If you are using TypeScript >= 1.5, you can add the `@autoinject` decorator to your class and leave out the Types in the decorator call, but just use them on the constructor's signature. Here's what that would look like:

```javascript
import {autoinject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@autoinject
export class Flickr{
  ...

  constructor(public http:HttpClient){}

  ...
}
```

Aurelia's router enforces a lifecycle on view-models whenever routes change. This is referred to as the "Screen Activation Lifecycle". View-models can optionally hook into various parts of the lifecycle to control flow into and out of the route. When your route is ready to activate the router will call the `activate` hook, if present. In the above code, we use this hook to call the Flickr api and get some images back. Notice that we return the result of the http request back from our `activate` method. All the `HttpClient` APIs return a `Promise`. The router will detect a `Promise` and wait to complete navigation until after it resolves. So, in this way, you can optionally force the router to delay displaying the page until it is populated with data.

There's a second lifecycle hook demonstrated here as well: `canDeactivate`. The router calls this before navigation away from the route happens. It gives you the opportunity to allow or disallow the navigation to continue by returning a boolean. You can also return a `Promise` for that value. The full lifecycle includes `canActivate`, `activate`, `canDeactivate` and `deactivate` hooks.

>**Note:** If you aren't familiar with [Promises](http://www.html5rocks.com/en/tutorials/es6/promises/), these are a new feature of ES6 designed to improve asynchronous programming. A `Promise` is an object that represents a future result. Essentially, it represents a "promise" to complete some work or to provide some data at some point in the future.

### flickr.html

```markup
<template>
    <section>
        <h2>${heading}</h2>
        <div class="row">
        <div class="col-sm-6 col-md-3" repeat.for="image of images">
          <a class="thumbnail">
            <img style="width: 260px; height: 180px;" src.bind="image.media.m"/>
          </a>
        </div>
        </div>
    </section>
</template>
```

The view for this screen is pretty straight forward. There's nothing you haven't seen before, including the awesome use of the very taboo inline style. (Just look the other way...nothing to see there.)

Once you've got all this in place, go ahead and run your app again. You should now see two items in the nav bar and be able to switch back and forth between them. Huzzah!

Let's recap. To add a page to your app:

1. Add the route configuration to the _app.js_ router.
2. Add a view-model.
3. Add a view with the same name (but with an .html extension).
4. Celebrate.

## Bonus: Creating a Custom Element

Look at you, you overachiever! I see you're interested in learning some extra awesome on this fine day. In that case, let's create a custom HTML element. I think a good candidate for this is our navbar. That's a lot of HTML in our _app.html_ file. Why not extract a custom `<nav-bar>` element to make things a bit more declarative? Here's what we want to be able to write in the end:

### app.html

```markup
<template>
  <require from='./nav-bar'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

This code requires a `nav-bar` element from "./nav-bar" and once it's available in the view, we can use it like any other element, including databinding to its custom properties (like _router_). So, how do we get to this end product?

Guess what? Our simple view-model/view conventions still apply for custom elements. (In fact you've been creating what we sometimes call "anonymous" custom elements all along...you just didn't realize it.) Let's create a _nav-bar.js_ and a _nav-bar.html_. Here's the code for the view-model first:

### nav-bar.js

```javascript
import {bindable} from 'aurelia-framework';

export class NavBar {
  @bindable router = null;
}
```

To create a custom element, you create and export a class. Since this class is going to be used in HTML as an element, we need to tell the framework what properties on the class should appear as attributes on the element. To do that, we use the _bindable_ decorator. Like _inject_, _bindable_ is a way to provide information about your class to the Aurelia framework. Aurelia is smart and can infer many things, but when it can't or when you want to do something different than the conventions, you supply some additional information through decorators. The `bindable` decorator tells the framework that we want our class's `router` property to be surfaced as an attribute in the HTML. Once it's surfaced as an attribute, we can bind to it in the view.

### nav-bar.html

```markup
<template>
  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle Navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">
        <i class="fa fa-home"></i>
        <span>${router.title}</span>
      </a>
    </div>

    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
          <a href.bind="row.href">${row.title}</a>
        </li>
      </ul>

      <ul class="nav navbar-nav navbar-right">
        <li class="loader" if.bind="router.isNavigating">
          <i class="fa fa-spinner fa-spin fa-2x"></i>
        </li>
      </ul>
    </div>
  </nav>
</template>
```

This looks almost identical to the navbar HTML in our original _app.html_ file. We've basically extracted that and put it into this template. Instead of binding to _app.js_ though, it's now binding to _nav-bar.js_.

This is a very simple custom element with no real behavior, but it is complete and usable as shown above. We repeat that markup here for your convenience.

### app.html

```markup
<template>
  <require from='./nav-bar'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

To recap: First we have a `require` element. Aurelia uses this to load the custom element via the relative source indicated in the `from` attribute. It's following our simple conventions so it will know how to load our _nav-bar.js_ and _nav-bar.html_ files. Anything required into a view in this way is local to the view. As a result, you don't have to worry about name conflicts. The second point is the actual use of the element which uses databinding against the `App`'s router. We are piping the router instance on our `App` class through to the corresponding property on the `NavBar` element for internal rendering. Sweet!

> **Note:** You can also load app-wide elements and other behaviors for convenience so you don't have to require common resources in every view.

You may wonder how Aurelia determines the name of the custom element. By convention, it will use the export name, lowered and hyphenated. However, you can always be explicit. To do so, add `@customElement('nav-bar')` decorator. What if your custom element doesn't have a view template because it's all implemented in code? No problem, add `@noView()`. Want to use ShadowDOM for your custom element? Do it like a pro by using `@useShadowDOM()`. Don't worry about whether or not the browser supports it. We have an efficient, full-fidelity ShadowDOM fallback implementation.

In addition to creating custom elements, you can also create custom attributes which add new behavior to existing elements. On occasion you may even need an attribute to dynamically control templates by adding and removing DOM from the view, like the `repeat` and `if` we used above. You can do all that and much more with Aurelia's powerful and extensible templating engine.

## Bonus: Leveraging Child Routers

Can't get enough can you? Well, I've got a treat for you. Let's add a third page to our app...with its own router! We call this a child router. Child routers have their own route configuration and navigate relative to the parent router. Prepare thyself for insanity....

First, let's update our _app.js_ with the new configuration. Here's what it should look like:

### app.js (updated...again)

```javascript
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome',      moduleId: './welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',       name: 'flickr',       moduleId: './flickr',       nav: true },
      { route: 'child-router', name: 'childRouter',  moduleId: './child-router', nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Nothing new there. The interesting part is what's inside _child-router.js_...

### child-router.js

```javascript
export class ChildRouter{
  heading = 'Child Router';

  configureRouter(config, router){
    config.map([
      { route: ['','welcome'], name: 'welcome',     moduleId: './welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',       name: 'flickr',      moduleId: './flickr',       nav: true },
      { route: 'child-router', name: 'childRouter', moduleId: './child-router', nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

What!? It's practically the same configuration as `App`? What? Why? Well...you should probably never do this in real life...but it's pretty cool what this does. This, my friends, is a recursive router, and we're doing it because we can.

For completeness, here's the view:

### child-router.html

```javascript
<template>
  <section>
    <h2>${heading}</h2>
    <div>
      <div class="col-md-2">
        <ul class="well nav nav-pills nav-stacked">
          <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
            <a href.bind="row.href">${row.title}</a>
          </li>
        </ul>
      </div>
      <div class="col-md-10" style="padding: 0">
        <router-view></router-view>
      </div>
    </div>
  </section>
</template>
```

Run the app and see the magic....and pray the universe doesn't explode.

## Conclusion

With its strong focus on developer experience, Aurelia can enable you to not only create amazing applications, but also enjoy the process. We've designed it with simple conventions in mind so you don't need to waste time with tons of configuration or write boilerplate code just to satisfy a stubborn or restrictive framework. You'll never hit a roadblock with Aurelia either. It's been carefully designed to be pluggable and customizable.

Thanks for taking the time to read through our guide. We hope you'll explore the docs and build something awesome. We look forward to seeing what you will make.
