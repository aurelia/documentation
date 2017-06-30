# Polyfilling

Aurelia out-of-the-box is built to work in; Chrome, Firefox, Edge, Safari and Internet Explorer 11. However, that doesn't mean you can't use Aurelia to build applications that support Internet Explorer 9 and up, you just need to polyfill a few missing things.

## Internet Explorer 9 & 10

For Internet Explorer 9, you'll need to add in a `requestAnimationFrame` polyfill and for Internet Explorer 9 and 10, you will need to install a Mutation Observer polyfill as well.

Both required polyfills can be found in the form of Npm packages. [This one](https://www.npmjs.com/package/raf) for polyfilling `requestAnimationFrame` and [this one](https://www.npmjs.com/package/mutationobserver-shim) for polyfilling Mutation Observer.

The recommended approach to adding in polyfills is to create a file in the root directory of your application and call it something like `polyfills.js`.

```javascript
import 'mutationobserver-shim/MutationObserver'; // IE10 MutationObserver polyfill
import 'raf/polyfill'; // IE9 requestAnimationFrame polyfill
```

If you are using Webpack, you'll want to include this file before including the `aurelia-bootstrapper` library.

```javascript
...
entry: {
    main: ['./polyfills', 'aurelia-bootstrapper'],
}
...
```

If you are a JSPM/System.js user, then you'll want to add the polyfills into your `index.html` file (or equivalent) to load the polyfills before loading anything else.

```html
<!doctype html>
<html>
  <head>
    <title>My App</title>
  </head>
  <body>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
      SystemJS.import('raf/polyfill').then(function() {
        return SystemJS.import('aurelia-polyfills');
      }).then(function() {
        return SystemJS.import('webcomponents/webcomponentsjs/MutationObserver');
      }).then(function() {
        SystemJS.import('aurelia-bootstrapper');
      });
    </script>
  </body>
</html>
```

## Promises

Surprisingly, the performance of native promises in all major web browsers can vary from browser to browser. Edge notoriously had some severe issues with its promise implementation (fixed in Edge 14) for quite sometime.

A popular Promise library called Bluebird consistently beats out native browser implementation of Promises quite considerably. While not essential, including Bluebird can help reduce possible performance bottlenecks of relying on potentially slower native implementations of `Promise`.

If you are using one of the official Aurelia skeletons, then you get Bluebird already configured out-of-the-box. The same thing applies to all Aurelia CLI projects, which come with Bluebird configured as well.