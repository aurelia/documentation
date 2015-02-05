# Docs

We've got a very rich set of docs planned for Aurelia. Unfortunately, we haven't quite finished them yet. However, for this early preview period, we've put together this document, containing examples of the most common tasks you might want to perform. If you have questions, we hope that you will join us on our [gitter channel](https://gitter.im/aurelia/discuss).

## Startup & Configuration

Most platforms have a "main" or entry point for code execution. Aurelia is no different. If you've read the [Get Started](/get-started.html) page, then you've seen the `aurelia-app` attribute. Simply place this on an HTML element and Aurelia's bootstrapper will load an _app.js_ and _app.html_, databind them together and inject them into the DOM element on which you placed that attribute. If you don't want to use that convention, simply provide a value to the attribute indicating which view-model to load. For example `<body aurelia-app="todo">` will result in a _todo.js_ and _todo.html_ being loaded instead.

The `aurelia-app` attribute is convenient for getting started, but often times you want to configure the framework or run some code prior to displaying anything to the user. So chances are, as your project progresses, you will migrate towards using `aurelia-main`.

>**Note:** If you are using AtScript, add an `atscript` attribute to the DOM element for your app. If you are using ES5 instead of ES6, add an `es5` attribute. Doing so will "turn on" functionality with makes using these languages easier.

**What is the difference?**

`aurelia-app` instantiates an Aurelia app and pre-configures it with the default set of options for the framework, then loads your application view-model. `aurelia-main` loads your custom configuration module, _main.js_ by default, then invokes your `configure` function, passing it the Aurelia object which you can then use to configure the framework yourself and decide what, when and where to display your UI. Here's an example _main.js_ file:

```javascript
import {LogManager} from 'aurelia-framework';
import {ConsoleAppender} from 'aurelia-logging-console';

LogManager.addAppender(new ConsoleAppender());
LogManager.setLevel(LogManager.levels.debug);

export function configure(aurelia) {
  aurelia.use
    .defaultBindingLanguage()
    .defaultResources()
    .router()
    .eventAggregator()
    .plugin('./path/to/plugin');

  aurelia.start().then(a => a.setRoot('app', document.body));
}
```

With the exception of the custom plugin, this code is essentially what `aurelia-app` normally does for you. When you switch to `aurelia-main` you need to configure these things yourself, but you can also install custom plugins, set up the depedency injection container with some services and install global resources to be used in view templates.

>**Note:** To turn on AtScript when manually configuring, call `aurelia.use.atscript()` and to turn on ES5, call `aurelia.use.es5()`.

### Logging

Aurelia has a simple logging abstraction that the framework itself uses. By default it is a no-op. The configuration above shows how to install an appender which will take the log data and output it the console. You can also see how to set the log level. Options for this setting include: `none`, `error`, `warn`, `info` and `debug`.

You can easily create your own appenders. Simply implement a class that matches the appender interface. The best way to see how to do this is to look at our own [console log appender's source](https://github.com/aurelia/logging-console/blob/master/src/index.js).

### Plugins

A _plugin_ is only a module with an exported `install` function. During startup Aurelia will load all plugin modules and call their `install` functions, passing to them the Aurelia instance so that they can configure the framework appropriately. Plugins can optionally return a `Promise` from their `install` function in order to perform asynchronous configuration tasks. When writing a plugin, be sure to follow these rules:

1. Use a flat directory structure. Do not locate behaviors or views in subdirectories.
2. Your file name and your behavior name must match.
3. Explicilty supply all metadata, including a View Strategy for Custom Elements.

> **Note:** Regarding #2 and #3: Do not rely on naming conventions inside plugins. You do not know how the consumer of your plugin will change Aurelia's conventions. 3rd party plugins should be explicit in order to ensure that they function correctly in different contexts.

#### Promises

By default, Aurelia uses ES6 native Promises or a polyfill. However, you can replace this with the excellent [Bluebird](https://github.com/petkaantonov/bluebird) Promise library. Simply include it in your page before you reference the other scripts. It will provide its own standards-compliant Promise implementation which is currently faster than native and has better debugging support. Additionally, when used in combination with the 6to5 transpiler, you can use [coroutines](https://6to5.org/docs/usage/transformers/#bluebird-coroutines) for improved async code.

### The Aurelia Object

Since both a custom _main_ module and plugins do their work by interacting with the Aurelia object, we provide a brief explanation of that API in code below:

```javascript
export class Aurelia {
  loader:Loader; //the module loader
  container:Container; //the app-level dependency injection container
  use:Plugins; //the plugins api

  withInstance(type, instance):Aurelia; //DI helper method (pass through to container)
  withSingleton(type, implementation):Aurelia; //DI helper method (pass through to container)
  withResources(resources):Aurelia; //resource helper method

  start():Promise; //starts the framework, causing plugins to be installed and resources to be loaded
  setRoot(root, applicationHost):Promise; //set your "root" or "app" view-model and display it
}
```

## Views and View Models

In Aurelia, user interface elements are composed of _view_ and _view-model_ pairs. The _view_ is written with HTML and is rendered into the DOM. The _view-model_ is written with JavaScript and provides data and behavior to the _view_. The templating engine and/or DI are responsible for creating these pairs and enforcing a predictable lifecycle for the process. Once instantiated, Aurelia's powerful _databinding_ links the two pieces together allowing changes in your data to be reflected in the _view_ and vice versa.

### Dependency Injection (DI)

View-models and other interface elements, such as Template Controllers and Attached Behaviors, are created as classes which are instantiated by the framework using a dependency injection container. Code written in this style is easy to modularize and test. Rather than creating large classes, you can break things down into small objects that collaborate to achieve a goal. The DI can then put the pieces together for you at runtime.

In order to leverage DI you simply need to add a bit of metadata to your class to tell the framwork what it should pass to its constructor. Here's an example of a view-model that depends on Aurelia's HttpClient.

```javascript
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }
}
```

Just provide a static method named `inject` that returns an array of things to inject.

> **Note:** If writing in TypeScript or CoffeeScript, you can use a static array property instead of a method. In ES5 you can add the property onto the constructor itself. You can also do this with ES6 but we enable the static method option since it can be located closer to the constructor in Vanilla JS. If you are using AtScript, you can actually take advantage of type annotations by defining your constructor like this: `constructor(http:HttpClient)`. (Before this will work you need to place the `atscript` attribute on your application host element or call `aurelia.use.atscript()` manually.)

The dependencies in your inject array don't have to be just constructor types. They can also be instances of `resolvers`. For example, have a look at this:

```javascript
import {Lazy} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static inject() { return [Lazy.of(HttpClient)]; }
    constructor(getHTTP){
        this.getHTTP = getHTTP;
    }
}
```

The `Lazy` resolver doesn't actually provide an instance of `HttpClient`. Instead, it provides a function which, when called, will return you an instance of HttpClient. There are several different resolvers out-of-the-box and you can create your own by authoring a class that inherits from `Resolver`. Here's a list of what we provide for you:

* `Lazy` - Injects a function for lazily evaluating the dependency.
    * ex. `Lazy.of(HttpClient)`
* `All` - Injects an array of all services registered with the provided key.
    * ex. `All.of(Plugin)`
* `Optional` - Injects an instance of a class only if it already exists in the container; null otherwise.
    * ex. `Optional.of(LoggedInUser)`
* `Parent` - Bypasses the current DI container and attempts to inject an instance stored in a parent container.
    * ex. `Parent.of(Router)`

In addition to these resolvers, you can also use `Registration` annotations to specify the default registration or lifetime for an instance. By default, the DI container assumes that everything is a singleton instance; one instance per container. However, you can use a registration annotation to change this. Here's an example:

```javascript
import {Metadata} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static metadata(){ return Metadata.transient(); }
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }
}
```

Now, each time the DI container is asked for an instance of `CustomerDetail` the container will return a new instance, rather than a singleton. `Singleton` and `Transient` registrations are provided out-of-the-box, but you can create your own by writing a class that inherits from `Registration`.

> **Note:** This last example introduces _metadata_ to provide contextual information to the framework. You will see metadata again when we talk about behaviors.

## Templating

Aurelia's templating engine is responsible for loading your views and their imported resources, compiling your HTML for optimal performance and rendering your UI to the screen. To create a view, all you need to do is author an HTML file with an `HTMLTemplate` inside. Here's a simple view:

```markup
<template>
    <div>Hello World!</div>
</template>
```

Everything inside the `template` tag will be managed by Aurelia. However, since Aurelia uses HTMLImport technology to load views, you can also include links, and they will be properly loaded, including relative resource resolution semantics. In other words, you can do this:

```markup
<link rel="stylesheet" type="text/css" href="./hello.css">

<template>
    <div class="hello">Hello World!</div>
</template>
```

This enables you to dynamically load per-view style sheets and even Web Components on the fly.

Any time you want to import an Aurelia-specific resource, such as an Aurelia _Custom Element_, _Attached Behavior_, _Template Controller_ or _Value Converter_, you should use an `import` element inside your view instead. Here's an example:

```markup
<template>
  <import from='./nav-bar'></import>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

In this case `nav-bar` is an Aurelia _Custom Element_ which we've imported for use. Using Aurelia's `import` element causes the framework's resource pipeline to process the imported item, which has the following advantages:

* Deduping - The resource is downloaded once in the app. Even if other views import the same element, it will not be downloaded again.
* One-time Compilation - Templates for Custom Elements imported this way are compiled once for the entire application.
* Local Scope - The imported resource is only visible inside the view that imports it, reducing the likelihood of name conflicts.
* Renaming - Resources can be renamed upon import if two 3rd party resources with the same name need to be used in the same view.
    - ex. `<import from="./nav-bar" as="foo-bar"></import>` - Now instead of using a `nav-bar` element you can use a `foo-bar` element. (This is based on ES6 where renaming is considered a replacement for using an Alias because it strictly renames the type.)
* Packages - The import can point to a module with multiple resources which will all be imported into the same view.
* Extensibility - You can define new types of resources which, when imported in this way, can execute custom loading (async one-time) and registration (once per-view).
* ES6 - Code is loaded by the ES6 loader rather than the HTMLImport mechanism, enabling all the features and extensibility of your loader.

In your view you will often leverage the different types of resources mentioned above as well as databinding.

>**Note:** You may be concerned about the tediousness of having to import things into each view. Remember, during the bootstrapping phase you can configure Aurelia with global resources to be available in every view.

### Databinding

Databinding allows you to link the state and behavior in a JavaScript object to an HTML view. When this link is established, any changes in linked properties can be synced in one or both directions. Changes in the JavaScript object can be reflected in the view and changes in the view can be reflected in the JavaScript object. To establish this link, you will leverage "binding commands" in your HTML. Binding commands are clearly identifiable via their use of the "." as a kind of binding operator. Whenever an HTML attribute contains a ".", the compiler will pass the attribute name and value off to the binding language for interpretation. The result is one or more binding expressions that are capable of establishing the linkage when the view is created.

You can extend the system with your own binding commands, but Aurelia provides a collection to cover the most common use cases.

#### bind, one-way, two-way & one-time

The most common binding command is `.bind`. This will cause the property to be bound using a "one-way" binding for all attributes, except form element values, which are bound with a "two-way" binding.

_What does this mean though?_

One-way binding means that changes flow from your JavaScript view-models into the view, not from the view into the view-model. Two-way binding means that changes flow in both directions. `.bind` attempts to use a sensible default by assuming that if you are binding to a form element's value property then you probably wish the changes made in the form to flow into your view-model. For everything else it uses one-way binding, especially since, in many cases, two-way binding to non-form elements would be nonsensical. Here's a small binding example using `.bind`:

```markup
<input type="text" value.bind="firstName">
<a href.bind="url">Aurelia</a>
```

In the above example, the `input` will have its `value` bound to the `firstName` property on the view-model. Changes in the `firstName` property will update the `input.value` and changes in the `input.value` will update the `firstName` property. On the other hand, the `a` tag will have its `href` bound to the `url` property on the view-model. Only changes in the `url` property will flow into the `href` of the `a` tag, not the other way.

You can always be explicit and use `.one-way` or `.two-way` in place of `.bind`though. A common case where this is required is with Web Components that function as input-type controls. So, you can imagine doing something like this:

```markup
<markdown-editor value.two-way="markdown"></markdown-editor>
```

In order to optimize performance and minimize CPU and memory usage, you can alternatively leverage the `.one-time` binding command to flow data from the view-model into the view "one time". This will happen during the initial binding phase, after which no synchronization will occur.

#### delegate, trigger & call

Binding commands don't only connect properties and attributes, but can be used to trigger behavior. For example, if you want to invoke a method on the view-model when a button is clicked, you would use the `trigger` command like this:

```markup
<button click.trigger="sayHello()">Say Hello</button>
```

When the button is clicked, the `sayHello` method on the view-model will be invoked. That said, adding event handlers to every single element like this isn't very efficient, so often times you will want to use event delegation. To do that, use the `.delegate` command. Here's the same example but with event delegation instead:

```markup
<button click.delegate="sayHello()">Say Hello</button>
```

> **Note:** If you aren't familiar with event delegation, it's a technique that uses the bubbling nature of DOM events. When using `.delegete` a single event handler is attached to the document, rather than on each element. When the element's event is fired, it bubbles up the DOM until it reaches the document, where it is handled. This is a more memory efficient way of handling events and it's recommended to use this as your default mechanism.

All of this works against DOM events in some way or another. Occasionally you may have a custom Aurelia behavior that wants a reference to your function directly so that it can invoke it manually at a later time. To pass a function reference, use the `.call` binding (since the behavior will _call_ it later):

```markup
<div touch.call="sayHello()">Say Hello</button>
```

Now the attached behavior will get a function that it can call to invoke your `sayHello()` code.

#### string interpolation

Sometimes you need to bind properties directly into the content of the document or interleave them within an attribute value. For this, you can use the string interpolation syntax `${expression}`. String interpolation is a one-way binding, the output of which is converted to a string. Here's an example:

```markup
<span>${fullName}</span>
```

The `fullName` property will be interpolated directly into the span's content. You can also use this to handle css class bindings like so:

```markup
<div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
```

In this snippet "dot" is a statically present class and "green" is present only if `isHappy` is true, otherwise the "red" class is present. Additionally, whatever the value of `color` is...that is added as class.

> **Note:** You can use simple expressions inside your bindings. Don't try to do anything too fancy. You don't want code in your view. You only want to establish the linkage between the view and its view-model.

#### ref

In addition to commands and interpolation, the binding language recognizes the use of a special attribute: `ref`. By using `ref` you can create a local name for an element which can then be referenced in another binding expression. It will also be set as a property on the view-model, so you can access it through code. Here's a neat example of using `ref`:

```markup
<input type="text" ref="name"> ${name.value}
```

You can also use the special `.view-model` binding in conjuction with `ref` to get the view-model instance that backs an Aurelia Custom Element. By using this technique, you can connect different components to each other like so:

```markup
<i-produce-a-value ref.view-model="producer"></i-produce-a-value>
<i-consume-a-value input.bind="producer.output"></i-consume-a-value>
```

### Behaviors

In addition to databinding, you also have the power of Aurelia behaviors to use in your views. There are three types of behaviors provided out of the box:

* Custom Elements - Extend HTML with new tags! Your custom elements can have their own views (which use databinding and other behaviors) and optionally leverage [ShadowDOM](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/) (even if the browser doesn't support it).
* Attached Behaviors - Extend HTML with new attributes which can be added to existing or custom elements. These attributes "attach" new behavior to the elements.
* Template Controllers - Create new mechanisms for rendering templates. A template controller is a class that can dynamically create UI and inject it into the DOM.

Naturally, all of this works seemlessly with databinding. Let's look at the behaviors that Aurelia provides for you and which are available globally in every view.

#### show

The `show` Attached Behavior allows you to conditionally display an HTML element. If the value of show is `true` the element will be displayed, otherwise it will be hidden. This behavior does not add/remove the element from the DOM, but only changes its visibility. Here's an example:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

When the `isSaving` property is true, the `div` will be visible, otherwise it will be hidden.

#### if

The `if` Template Controller allows you to conditionally add/remove an HTML element. If the value is true, the element will also be present in the DOM, otherwise it will not.

```markup
<div if.bind="isSaving" class="spinner"></div>
```

This example looks similar to that of `show` above. The difference is that if the binding expression evaluates to false, the `div` will be removed from the DOM, rather than just hidden.

If you need to conditionally add/remove a group of elements and you cannot place the `if` behavior on a parent element, then you can wrap those elements in a template tag which has the `if` behavior. Here's what that would look like:

```markup
<template if.bind="hasErrors">
    <i class="icon error"></i>
    ${errorMessage}
</template>
```

#### repeat

The `repeat` Template Controller allows you to render a template multiple times, once for each item in an array. Here's an example that renders out a list of customer names:

```markup
<ul>
    <li repeat.for="customer of customers">${customer.fullName}</li>
</ul>
```

An important note about the repeat behavior is that it works in conjuction with the `.for` binding command. This binding command interprets a special syntax in the form "item of array" where "item" is the local name you will use in the template and "array" is a normal binding expression that evaluates to an array.

> **Note:**: Like the `if` behavior, you can also use a `template` tag to group a collection of elements that don't have a parent element. In fact this is true of all Template Controllers. When you place a Template Controller on an element, it transforms it into an HTMLTemplate during compilation, so you can always explicitly add the template in your markup if you want or need to.

#### compose

The `compose` Custom Element enables you to dynamically render UI into the DOM. Imagine you have a heterogeneous array of items, but each has a type property which tells you what it is. You can then do something like this:

```markup
<template repeat.for="item of items">
    <compose
      model.bind="item"
      view-model="widgets/${item.type}">
    </compose>
</template>
```

Now, depending on the _type_ of the item, the `compose` element will load a different view-model (and view) and render it into the DOM. If the view-model has an `activate` method, the `compose` element will call it and pass in the `model` as a parameter. The `activate` method can even return a `Promise` to cause the composition process to wait until after some async work is done before actually databinding and rendering into the DOM.

The `compose` element also has a `view` attribute which can be used in the same way as `view-model` if you don't wish to leverage the standard view/view-model convention.

What if you want to determine the view dynamically based on data though? or runtime conditions? You can do that too by implementing a `getViewStrategy()` method on your view-model. It can return a relative path to the view or an instance of a `ViewStrategy` for custom view loading behavior. The nice part is that this method is executed after the `activate` callback, so you have access to the model data when determining the view.

#### selected-item

HTMLSelectElement is an interesting beast. Usually, you can databind these by combining a `repeat` for the options with a binding on the value, like this:

```markup
<select value.bind="favoriteNumber">
    <option>Select A Number</option>
    <option repeat.for="number of numbers" value.bind="number">${number}</option>
</select>
```

But sometimes you want to work with selecting object instances rather than primitives. For that you can use the `selected-item` attached behavior. Here's how you would configure that for a theoretical list of employees:

```markup
<select selected-item.bind="employeeOfTheMonth">
  <option>Select An Employee</option>
  <option repeat.for="employee of employees" value.bind="employee.id" model.bind="employee">${employee.fullName}</option>
</select>
```

First, we specify the `.bind` binding command on `selected-item`. We then use a repeater as normal, being sure to bind `value` to some primitive. We also add a second property named `model` which the `selected-item` behavior will use to correlate selection with an object instance. In other words, when an option is selected the `employeeOfTheMonth` property will be set to the value of the `model` property on that option. When the `employeeOfTheMonth` property is set in the view-model, the option with the corresponding `model` value will be selected in the view.

> **Note:** We said earlier that only form element values bind two-way by default, but in this case our custom attribute `selected-item` is also bound with a two-way mode by default. How did that work? It turns out that when you define Aurelia behaviors, you can optionally specify the default binding mode on properties.

#### global-behavior

This is not an Attached Behavior that you will use directly. Rather, it works in conjunction with a custom binding command to dynamically enable the use of jQuery plugins and similar APIs declaratively in HTML. Let's look at an example in order to help clarify the idea:

```markup
<div jquery.modal="show: true; keyboard.bind: allowKeyboard">...</div>
```

This sample is based on the [Bootstrap modal widget](http://getbootstrap.com/javascript/#modals). In this case, the `modal` jQuery widget will be attached to the `div` and it will be configured with its `show` option set to `true` and its `keyboard` option set to the value of the `allowKeyboard` property on the view-model. When the containing view is unbound, the jQuery widget will be destroyed.

This capability combines the special `global-behavior` Attached Behavior with custom syntax to enable these dynamic capabilities. The syntax you see here is based on the syntax of the native `style` attribute which lists properties and values separated in the same fashion as above. Note that you can use binding commands such as `.bind` to pass data from your view-model directly to the plugin or `.call` to pass a callback function directly to the plugin.

Here's how it works:

When the binding system sees a binding command that it doesn't recognize, it dynamically interprets it. The attribute name is mapped to a global binding handler which interprets the binding command. The handler can use the values to create an options object which it can pass to the plugin. When the view is unbound, the handler can also cleanup after itself. In this case the jQuery handler knows the pattern for instantiating plugins and using the `destroy` method to cleanup.

> **Note:** The `global-behavior` has a handlers list you must configure. It is only configured with jQuery by default. You can turn all of this off, if you desire, but it makes it easy to take advantage of basic jQuery plugins without any work on your part.

## Routing

There are many different application styles you could be called upon to create. From navigation apps, to dashboards, to MDI interfaces, Aurelia can handle them all. In many of these cases a key component of your architecture is a client-side router, capable of translating url changes into application state.

If you've read the getting started guide, you know that there are two parts to routing. First, there's the `Router` which lives in your view-model. It's configured with route information and controls navigation. Then, there's the `router-view` which lives in the view and is responsible for displaying whatever the router identifies as the current state.

Let's look at an example configuration.

```javascript
import {Router} from 'aurelia-router';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['', 'home'],               moduleId: './home/index' },
        { route: 'users',                    moduleId: './users/index',                      nav: true },
        { route: 'users/:id/detail',         moduleId: './users/detail' },
        { route: 'files*path',               moduleId: './files/index',     href:'#files',   nav: true }
      ]);
    });
  }
}
```

We begin by asking for a `Router` to be injected. We then set this instance to a `router` property on the view-model. _You must name the property **router**_. Then we call the `configure` api. We pass it a function and it passes us a configuration object.

We can optionally set a `title` property to be used in constructing the document's title. But the most important part is setting up the routes. The router's `map` method takes a simple JSON data structure representing your route table. The two most important properties are `route` (a string or array of strings), which defines the route pattern, and `moduleId`, which has the *relative* module Id path to your view-model. You can also set a `title` property, to be used when generating the document's title, a `nav` property indicating whether or not the route should be included in the navigation model (it can also be a number indicating order) and an `href` property which you can use to bind to in the _navigation model_.

>**Note:** Any properties that you leave off will be conventionally determined by the framework based on what you have provided.

So, what options to you have for the route pattern?

* static routes
    - ie 'home' - Matches the string exactly.
* parameterized routes
    - ie  'users/:id/detail' - Matches the string and then parses an `id` parameter. Your view-model's `activate` callback will be called with an object that has an `id` parameter set to the value that was extracted from the url.
* wildcard routes
    - ie 'files*path' - Matches the string and then anything that follows it. Your view-model's `activate` callback will be called with an object that has a `path` parameter set to the wildcard's value.

All routes with a truthy `nav` property are assembled into a `navigation` array. This makes it really easy to use databinding to generate a menu structure. Another important property for binding is the `isNavigating` property. Here's some simple markup that shows what you might pair with the view-model shown above:

```markup
<template>
  <ul>
    <li class="loader" if.bind="router.isNavigating">
      <i class="fa fa-spinner fa-spin fa-2x"></i>
    </li>
    <li repeat.for="item of router.navigation">
      <a href.bind="item.href">${item.title}</a>
    </li>
  </ul>

  <router-view></router-view>
</template>
```

### The Screen Activation Lifecycle

Whenever the router processes a navigation, it enforces a strict lifecycle on the view-models that it is navigating to and from. There are four stages in the lifecycle. You can opt-in to any of them by implementing the appropriate method on your view-model's class. Here's a list of the lifecycle callbacks:

* `canActivate(params, queryString, routeConfig)` - Implement this hook if you want to control whether or not vour view-model _can be navigated to_. Return a boolean value, a promise for a boolean value, or a navigation command.
* `activate(params, queryString, routeConfig)` - Implement this hook if you want to perform custom logic just before your view-model is displayed. You can optionally return a promise to tell the router to wait to bind and attach the view until after you finish your work.
* `canDeactivate()` - Implement this hook if you want to control whether or not the router _can navigate away_ from your view-model when moving to a new route. Return a boolean value, a promise for a boolean value, or a navigation command.
* `deactivate()` - Implement this hook if you want to perform custom logic when your view-model is being navigated away from. You can optionally return a promise to tell the router to wait until after your finish your work.

The `params` object will have a property for each parameter of the route that was parsed, `queryString` will have a property for each query string value and `routeConfig` will be the original route configuration object that you set up.

> **Note:** A _Navigation Command_ is any object with a `navigate(router)` method. When one is encountered, the navigation will be cancelled and control will be passed to the navigation command. One navigation command is provided out of the box: `Redirect`.

### Child Routers

If you haven't read the "Get Started" guide, we recommend that you do that now and pay special attention to the section titled "Bonus: Leveraging Child Routers".

Whenever you set up a route to map to a view-model, that view-model can actually contain its own router...and when you set up routes with that...those view-models can have their own routers...and so on. The route patterns are relative to the parent router and the module and view ids are relative to the view-model itself. This allows you to easily encapsulate features or child applications as well as handle complex hierarchical state.

A child router is just a router like any other. So, everything we've discussed above applies. To add a child router, just ask for a `Router` to be injected and configure it with your child routes. The screen activation lifecycle discussed above applies to child routers as well. Each phase of the lifecycle is run against the entire router hierarchy before moving on to the next phase. The activate hooks run from top to bottom and the deactivate hooks run from bottom to top.

### Conventional Routing

As with everything in Aurelia, we have strong support for conventions. So, you can actually choose to dynamically route rather than pre-configuring all your routes up front. Here's how you configure a router to do that:

```javascript
router.configure(config => {
  config.mapUnknownRoutes(instruction => {
    //check instruction.fragment
    //set instruction.config.moduleId
  });
});
```

All you have to do is set the `config.moduleId` property and you are good to go. You can also return a promise from `mapUnknownRoutes` in order to asynchronously determine the destination.

>**Note:** Though not necessarily related to conventional routing, you may sometimes have a need to asynchronously configure your router. For example, you may need to call a web service to get user permissions before setting up routes. To do this, implement a callback on your router's view-model named `configureRouter`. In this callback you can configure your router and optionally return a Promise if necessary.

## Extending HTML

Aurelia has a powerful and extensible HTML template compiler. The compiler itself is just an algorithm for interacting with various _behavior types_ which contain the logic for manipulating HTML. Out of the box, Aurelia provides three core behavior type implementations, which we believe cover the bulk of scenarios you will encounter from day to day. The tree types are _Attached Behaviors_, _Custom Elements_ and _Template Controllers_.

Behaviors are not visible to the compiler by default. There are three main ways to plug them in:

* Use the `import` element to import a behavior into a view. The `from` attribute specifies the relative path to the behavior's module. The behavior will be locally defined.
* Use the Aurelia object during your bootstrapping phase to call `.withResources(resources)` to register behaviors with global visibility in your application.
* Install a plugin that registers behaviors with global visibility in your application.

>**Note:** A reccommended practice for your own apps is to place all your app-specific behaviors, value converters, etc. into a _resources_ folder. Then create an _index.js_ file that turns them all into an internal plugin. Finally, install that plugin during your app's bootstrapping phase. This will keep your resources located in a known location, along with their registration code. It will also keep your _main.js_ file clean and simple.

All behaviors can opt into the view lifecycle by implementing any of the followinging hooks:

* `bind(bindingContext)` - Invoked when the databinding engine binds the view. The binding context is the instance that the view is databound to.
* `unbind()` - Invoked when the databinding engine unbinds the view.
* `attached()` - Invoked when the view that contains the behavior is attached to the DOM.
* `detached()` - Invoked whtn the view that contains the behavior is detached from the DOM.

>**Note:** If you choose to implement the `bind` callback, the initial binding of your behavior will flow a little differently. Usually, if you have callbacks for your Behavior Properties, these are each individually called during the bind phase. However, if you add the `bind` callback, they will not be called during initialization. Rather, the `bind` callback will be called once all properties have their initial bound values set. This is an important and useful characteristic, particularly for complex behaviors which may not want to "act" until they have all evaluated values available.

### Attached Behaviors

Attached behaviors "attach" new behavior or functionality to existing HTML elements by adding a custom attribute to your markup. Common uses for attached behaviors include:

* Wrapping jQuery and similar plugins (when the `global-behavior` is insufficient).
* Shortcuts for common style, class or attribute bindings.
* Just about anything that needs to change an existing HTML element or even a Custom Element which you cannot directly alter.

Attached Behaviors tend to represent cross-cutting concerns. For example you might create a custom tooltip behavior that you can then attach to any element. This is a better idea than building tooltip functionality directly into every custom element you create.

Let's look at one of Aurelia's own Attached Behavior implementations: `show`. Here's how it is used:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

The `show` behavior will conditionally apply a class to an element based on the falsiness of its value. (The class, when applied, hides the element.) Here's the implementation:

```javascript
import {Behavior} from 'aurelia-templating';

export class Show {
  static metadata(){
    return Behavior
      .attachedBehavior('show')
      .withProperty('value', 'valueChanged', 'show');
  }

  static inject() { return [Element]; }
  constructor(element) {
    this.element = element;
  }

  valueChanged(newValue){
    if (newValue) {
      this.element.classList.remove('aurelia-hide');
    } else {
      this.element.classList.add('aurelia-hide');
    }
  }
}
```

The first thing to note is that Attached Behaviors are classes and follow the same patterns we've already seen. Notice that the `metadata` plays an important role in defining a behavior. Here's what the metadata is doing:

* `.attachedBehavior('show')` - Creates an `AttachedBehavior` metadata instance to tell the HTML compiler how this class "plugs in". The behavior will be recognized by the compiler any time it sees an attribute named "show". In Aurelia, Attached Behaviors always map to a single HTML Attribute. There's a one-to-one relationship.
* `.withProperty('value', 'valueChanged', 'show')` - Creates a `BehaviorProperty` that tells the HTML compiler that there's a specific property on your class that maps to an attribute in HTML. The first parameter of this method is your class's property name. The last parameter is the attribute name, which is only required if it is different from the property name. The second parameter optionally indicates a callback on the class which will be invoked whenever the property changes.

Ok. Let's talk about conventions.

* If your callback function is named {propertyName}Changed, then you don't need to specify it. So, in the above case, we could omit the value of the second parameter.
* If your property name and attribute name are the same, then you don't need to specify it. In the above case, they are different, so we need to specify it.
* Attached behaviors always map to a single attribute. This allows us to optimize a simple usage pattern. If you name your property "value", then you don't need to include the property metadata at all. We will automatically map an attribute with the same name as your behavior to the `value` property.
* If you name your class {BehaviorName}AttachedProperty, then you don't need to include the attached behavior metadata at all. The attribute name will be inferred from the class name by stripping off "AttachedBehavior" and lowercasing and hyphenating the remaining part of the name. ie. behavior-name

These conventions mean that we can actually define our `show` behavior like this:

```javascript
export class ShowAttachedBehavior {
  static inject() { return [Element]; }
  constructor(element) {
    this.element = element;
  }

  valueChanged(newValue){
    if (newValue) {
      this.element.classList.remove('aurelia-hide');
    } else {
      this.element.classList.add('aurelia-hide');
    }
  }
}
```

> **Note:** So, why doesn't Aurelia itself leverage these conventions internally? Any time you are creating a 3rd party library of behaviors, it's best to be explicit. You don't know whether or not developers consuming your library will have changed Aurelia's conventions, thus breaking your library. In order to prevent this, always explicitly state the metadata for behaviors intended to be used in other apps. Inside your own apps though, you can use the conventions all you want to simplify development.

Next, let's look at the constructor.

AttachedBehaviors can easily gain access to the HTML element they are attached to by specifying it in the `inject` array. The `show` behavior stores the reference so that it can update the `classList` at a later time.

Finally, let's look at the `valueChanged` callback. We said previously that this is configured through the property metadata so that it is called whenever the value changes. The binding system will automatically update properties thus triggering the callback. So, all the implementation has to do is add/remove the appropriate class based on the value.

#### Options Properties

You may be wondering what to do if you want to create an Attached Behavior with multiple properties, since Attached Behaviors always map to a single attribute. For this scenario, we use an `OptionsProperty` which enables your single attribute to work like the browser's native `style` attribute, with multiple properties embedded within. Here's an examlple of how that is used:

```javascript
import {Behavior} from 'aurelia-templating';

export class Show {
  static metadata(){
    return Behavior
      .attachedBehavior('my-behavior')
      .withOptions().and(x => {
        x.withProperty('foo');
        x.withProperty('bar');
      });
  }
}
```

This creates an Attached Behavior named `my-behavior` with two properties `foo` and `bar`. Each of these properties are available directly on the class, however they are configured in HTML a bit different. Here's how that would be done:

```markup
<div my-behavior="foo: some literal value; bar.bind: some.expression"></div>
```

Notice that we don't use a binding command on the behavior itself. Instead, we can use them on each individual property inside the attribute's value. You can use literals as well as the standard binding commands.

> **Note:** You don't use `delegate` or `trigger` commands inside an options attribute. Those are always attached to the element itself, since they work directly with native DOM events. However, you can use `call`.

### Custom Elements

Custom Elements add new tags to your HTML markup. Each Custom Element can have its own view template which can be rendered into the Light DOM or the Shadow DOM. Custom Elements can also have any number of properties which they surface as attributes in HTML for databinding support and which they can databind to inside their view template.

Why don't we create a simple custom element so that we can see how that works? We'll make an element that says hello to someone, called `say-hello`. Here's how we want to be able to use it when we're done:

```markup
<template>
    <import from="./say-hello"></import>

    <input type="text" ref="name">
    <say-hello to.bind="name.value"></say-hello>
</template>
```

So, how do we build this? Well, we're going to start with a class, just like we did with the Attached Behavior. Here's what it looks like:

#### say-hello.js
```javascript
import {Behavior} from 'aurelia-templating';

export class SayHello {
  static metadata(){
    return Behavior
      .customElement('say-hello')
      .withProperty('to');
  }

  speak(){
    alert('Hello ${this.to}!');
  }
}
```

If you read the section on AttachedBehaviors, then you know what this does. There's some conventions too, which means we can do this if we want:

#### say-hello.js (with conventions)
```javascript
import {Behavior} from 'aurelia-templating';

export class SayHelloCustomElement {
  static metadata(){
    return Behavior.withProperty('to');
  }

  speak(){
    alert('Hello ${this.to}!');
  }
}
```

Be default, Custom Elements have a view. Here's the view for ours:

#### say-hello.html
```markup
<template>
    <button click.delegate="speak()">Say Hello To ${to}</button>
</template>
```

As you can see, we've got access to our class's properties and methods. It's important to note that you don't need to declare property metadata for every property you want to bind to in your template. You only need to declare it for properties you want to exist as attribute on your custom element.

That's really all there is to it. You follow the same view-model/view naming conventions and all the same patterns for custom elements. There are a few unique metadata options for custom elements you should know about:

* `.useShadowDOM()` - This causes your component's view to be rendered in the ShadowDOM rather than in the Light DOM. If you aren't familiar with these terms, have a read through of [this article](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/). When using Shadow DOM, you can use _content selectors_ in your view template.
* `.noView()` - If your custom element doesn't have a view, because all its behavior is implemented in code, then use this option.
* `.useView(relativePath)` - If you want to use a different view than the one that would be conventionally used, you can use this metadata option to specify a relative path to the view you want to use.

### Template Controllers

Template Controllers convert DOM into an inert HTML template. The controller can then decided when and where (or how many times) to instantiate the template in the DOM. Examples of this are the `if` and `repeat` behaviors. Simply place one of these behavior on a DOM node and it becomes a template, controlled by the behavior.

Let's take a look at the implementation of the `if` behavior to see how one of these is put together. Here's the full source code:

```javascript
import {Behavior, BoundViewFactory, ViewSlot} from 'aurelia-templating';

export class If {
  static metadata(){
    return Behavior
      .templateController('if')
      .withProperty('value', 'valueChanged', 'if');
  }

  static inject() { return [BoundViewFactory, ViewSlot]; }
  constructor(viewFactory, viewSlot){
    this.viewFactory = viewFactory;
    this.viewSlot = viewSlot;
    this.showing = false;
  }

  valueChanged(newValue){
    if (!newValue) {
      if(this.view){
        this.viewSlot.remove(this.view);
        this.view.unbind();
      }

      this.showing = false;
      return;
    }

    if(!this.view){
      this.view = this.viewFactory.create();
    }

    if (!this.showing) {
      this.showing = true;

      if(!this.view.bound){
        this.view.bind();
      }

      this.viewSlot.add(this.view);
    }
  }
}
```

Before we dig into the unique aspects of Template Controllers, let me remind you of what you see here that is simlar. First, we have a simple class with metadata. Our metadata is declared the same as in the two previous behavior types. The conventions work the same as well. So, you could name this class `IfTemplateController` and you wouldn't need to specify it in the metadata. Also, you can leave off the property metadata when you declare the `valueChanged` callback. It follows the same pattern as AttachedBehaviors.

Ok, what's different? Take a look at the constructor. Our Template Controller has two unique items being injected: `BoundViewFactory` and `ViewSlot`.

The `BoundViewFactory` is capable of generating instances of the the HTML template that the controller is attached to. No need to worry about compiling, etc. That's taken care of for you. Why is it called "Bound" View Factory though? Well, it's already referencing the parent binding context. It's "bound" in a sense. So, if you call its `create` method it will instantiate a new View from the template which will be bound to that context. This is what you want with an `if` behavior. It's not what you want with a `repeat` behavior. In that case, each time you call `create` you want a view bound to a particular array item. To achieve this, simply pass any object you want the view to be bound against into the `create` method.

The `ViewSlot` represents the slot or location within the DOM that the template was extracted from. This is usually the location that you want to add View instances to.

>**Note**: Unlike previous behaviors, the Template Controller works more directly with the _primitives_ of the framework. Views, ViewFactories and ViewSlots are all low level parts of the templating engine.

Take a close look at the `valueChanged` callback. Here you can see where the `if` behavior is creating the view and adding it to the slot, based on the truthiness of the value. There are a few important details of this:

* The behavior always calls `bind` on the View _before_ adding it to the ViewSlot. This ensures that all internal bindings are initially evaluated outside of the live DOM. This is important for performance.
* Similarly, always call `unbind` _after_ removing the View from the DOM.
* After the View is initially created, the `if` behavior does not throw it away even when the value becomes false. It caches the instance. Aurelia can re-use Views and even re-target them at different binding contexts. Again, this is important for performance, since it eliminates needless re-creation of Views.

## Eventing

Eventing is a powerful tool when you need decoupled components of your application to talk to one another. Aurelia supports both standard DOM events as well as more application-specific events via the `EventAggregator`.

### DOM Events

DOM events should be used when UI-specific messages need to be sent. They should not be used for application-specific messages. Aurelia doesn't add any functionality beyond the DOM for UI events. Any behavior can have its associated `Element` injected into its constructor. You can then use the `Element` to trigger events. To learn more about creating and triggering custom DOM events, [please read this article](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events).

### The Event Aggregator

If you need loosely coupled application-events, you want to use the `EventAggregator`. Its streamlined pub/sub interface makes it ideal for a wide range of messaging scenarios.

The Event Aggregator can publish events to a message channel or it can publish strongly-typed messages. Let's look at publishing to channels first:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class APublisher{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    publish(){
        var payload = {}; //any object
        this.eventAggregator.publish('channel name here', payload);
    }
}
```

We begin by having the DI provide us with the singleton Event Aggregator. Next we call its `publish` method, passing it the message channel name and the data payload to send on that channel. Here's how a subscriber would set themselvs up to receive this:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class ASubscriber{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    subscribe(){
        this.eventAggregator.subscribe('channel name here', payload => {
            //do something with the payload here
        });
    }
}
```

As you can see, they use the same channel name, but provide a callback, which will be invoked for every message sent on the channel.

Alternatively, you can publish and subscribe to strongly-typed messages. Here's an example publisher:

```javascript
export class SomeMessage{ }
```

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';
import {SomeMessage} from './some-message';

export class APublisher{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    publish(){
        this.eventAggregator.publish(new SomeMessage());
    }
}
```

In this case, we publish an instance of a particular message type. Here's a sample subscriber:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';
import {SomeMessage} from './some-message';

export class ASubscriber{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    subscribe(){
        this.eventAggregator.subscribe(SomeMessage, message => {
            //do something with the message here
        });
    }
}
```

The subscriber will be called any time an instance of `SomeMessage` is published. Subscription is polymorphic, so if a subclass of SomeMessage is published, this subscriber will be notified as well.

>**Note:** All forms of the `subscribe` method return a _dispose function_. You can call this function to dispose of the subscription and discontinue receiving messages. A good place to dispose is either in a view-model's `deactivate` callback, if it is managed by a router, or in its `detached` callback, if it is any other view-model.

## HTTP Client

As a convenience, Aurelia includes a basic `HttpClient` to provide a comfortable interface to the browser's XMLHttpRequest object. `HttpClient` is not included in the modules that Aurelia's bootstrapper installs, since its completely optional and many apps may choose to use a different strategy for data retrieval. So, if you want to use it, first you must install it with the following command:

```shell
jspm install aurelia-http-client
```

Then you can use it like this:

```javascript
import {HttpClient} from 'aurelia-http-client';

export class WebAPI {
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }

    return getAllContacts(){
        return this.http.get('uri goes here');
    }
}

```

The `HttpClient` has the following implementation:

```javascript
export class HttpClient {
  constructor(baseUrl = null, defaultRequestHeaders = new Headers()){
    this.baseUrl = baseUrl;
    this.defaultRequestHeaders = defaultRequestHeaders;
  }

  send(requestMessage, progressCallback){
    return requestMessage.send(this, progressCallback);
  }

  get(uri){
    return this.send(new HttpRequestMessage('GET', join(this.baseUrl, uri))
        .withHeaders(this.defaultRequestHeaders));
  }

  put(uri, content, replacer){
    return this.send(new HttpRequestMessage('PUT', join(this.baseUrl, uri), content, replacer || this.replacer)
        .withHeaders(this.defaultRequestHeaders));
  }

  patch(uri, content, replacer){
    return this.send(new HttpRequestMessage('PATCH', join(this.baseUrl, uri), content, replacer || this.replacer)
        .withHeaders(this.defaultRequestHeaders));
  }

  post(uri, content, replacer){
    return this.send(new HttpRequestMessage('POST', join(this.baseUrl, uri), content, replacer || this.replacer)
        .withHeaders(this.defaultRequestHeaders));
  }

  delete(uri){
    return this.send(new HttpRequestMessage('DELETE', join(this.baseUrl, uri))
        .withHeaders(this.defaultRequestHeaders));
  }

  jsonp(uri, callbackParameterName='jsoncallback'){
    return this.send(new JSONPRequestMessage(join(this.baseUrl, uri), callbackParameterName));
  }
}
```

As you can see, it provides convenience methods for `get`, `put`, `patch`, `post`, `delete` and `jsonp`. Each of these methods sends an `HttpRequestMessage` except `jsonp` which sends a `JSONPRequestMessage`. The result of sending a message is a `Promise` for an `HttpResponseMessage`.

The `HttpResponseMessage` has the followingn properties:

* `response` - Returns the raw conent sent from the server.
* `responseType` - The expected response type.
* `content` - Formats the raw `response` content based on the `responseType` and returns it.
* `headers` - Returns a `Headers` object with the parsed header data.
* `statusCode` - The server's response status code.
* `statusText` - The server's textual status message.
* `isSuccess` - Indicates whether or not the status code falls within the success range.
* `reviver` - A function used to transform the raw `response` content.
* `requestMessage` - A reference to the original request message.

> **Note:** By default, the `HttpClient` assumes you are expecting a JSON responseType.

## Customization

### View and View-Model Conventions

How are views and view-models linked? Our simple convention is based on module id. If you've got a view-model with id (essentially path) './foo/bar/baz' then that will map to `./foo/bar/baz.js` and `./foo/bar/baz.html` by default. Suppose you want to follow a different convention though. What if all your view-models live in a `view-models` folder and you want their views to live in a `views` folder? How would you do that? In order to do this, you want to change the behavior of the Conventional View Strategy. Here's how you do it:

```javascript
import {ConventionalView} from 'aurelia-framework';

ConventionalView.convertModuleIdToViewUrl = function(moduleId){
  return moduleId.replace('view-models', 'views') + '.html';
}
```

You should execute this code as part of your bootstrapping logic so that it takes effect before any behaviors are loaded. This will affect *everything* including custom elements. So, if you need or want those to act differently, you will need to account for that in your implementation of `convertModuleIdToViewUrl`.

> **Note:** This is an example of why 3rd party plugin authors should not rely on conventions. Developers may change these conventions in order to fit the needs of their own app.
