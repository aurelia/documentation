# The View/View-model Relationship Explained

Because Aurelia is a framework that leverages default conventions, it makes some safe assumptions out-of-the-box that can be configured. The biggest convention in Aurelia is its assumption of view and view-model pairs.

By default any view-model (with exception of a custom attribute) that is related to routing or rendering is expected to have a view and view-model.

For example, let's say you have an application that has a homepage, about page and a contact page. You might have three routes; `/home`, `/about` and `/contact` which all have an accompanying `moduleId` in the route configuration pointing to a view-model.

Your routes for the above scenario might look like this:

```javascript
configureRouter(config, router) {
    config.map([
      { route: ['', 'home'], name: 'home', moduleId: PLATFORM.moduleName('home'), nav: true, title: 'Homepage' },     

      { route: 'about', name: 'about', moduleId: PLATFORM.moduleName('about'), nav: true, title: 'About' },   

      { route: ['contact'], name: 'contact', moduleId: PLATFORM.moduleName('contact'), nav: true, title: 'Contact' },
    ]);

    this.router = router;
}
```

When we hit our default `home` route the router will look up our view-model of `home.js` and look for an accompanying `home.html` file. It takes the filename of the view-model and replaces the extension with `.html`. All of this is configurable, but this is how it works by default.

The same thing applies for custom elements as well.