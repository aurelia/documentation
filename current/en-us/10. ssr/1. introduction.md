---
name: "Server Side Rendering"
description: An introduction to server-side rendering with Aurelia.
author: Jeroen Vinke (jeroenvinke.nl)
---
The Aurelia framework is a client-side JavaScript framework. One of the characteristics is that on page load, a bundle of JavaScript code is fetched and executed, starting the framework and the application. The server does not do much other than serve the client static files.

There are a few downsides to this. Since the HTML that's returned from the server does not contain any content (only a boilerplate HTML file that references JavaScript bundles), crawlers of search engines can't do their job effectively.

Another downside is the time it takes to load the application. First the browser has to fetch the HTML file from the server, load all bundles (which could be relatively large in size) and finally the application has to be started. Only then will the user see the page they requested.

A solution to these problems is Server Side Rendering (SSR), which the Aurelia framework supports. Read on to learn more.

> Info
> Server Side Rendering is a new feature of the Aurelia framework, intended for use by early adopters.

## What is Server Side Rendering?

Server Side Rendering is a concept where the server gets more responsibilities, namely rendering the application on the server and sending the fully rendered page back to the client. By doing this the user will see a rendered page more quickly, before the bundles are loaded and the application has started client-side. Since the page that's returned by the server is fully rendered (the page content is already in the HTML), your site will receive a higher ranking by search engines.

Server Side Rendering is only feasible because of something called isomorphism. An isomorphic framework allows you to run one application both on the client and the server. Aurelia is such a framework.

## Caveats

Even though Server Side Rendering can solve some problems of client side applications, there are some caveats that you should be aware of. The most important one is the fact that your application would run in two different environments: the browser and Node.js. This is possible mostly because of Aurelia's [Platform Abstraction Layer](https://github.com/aurelia/pal) which abstracts logic that is platform specific. It is important that you use these abstractions.

Node.js is in some ways quite different from a browser environment. There is no (real) DOM, `window` or `document` global. Anything that depends on such globals (jQuery for example) will not work. To emulate the DOM, a library called jsdom is used. The Platform Abstraction Layer that's used in the server environment uses jsdom so that basic DOM operations can be done. As long as you use the Platform Abstraction Layer to do any modification to the DOM, you should be fine.

Developing your application in a way so that it can be rendered both on the client and the server takes time and effort. Our recommendation is to only use Server Side Rendering if you can spend the time required to make your code work for both the client and server.

Since the server will be doing more than just serving static files in a Server Side Rendering setup, more resources are used. So it is necessary to have enough memory and CPU resources available.

## How It Works

There are a three libraries that are used for Server Side Rendering:

* `aurelia-middleware-koa`
* `aurelia-ssr-bootstrapper-webpack`
* `aurelia-ssr-engine`

The `aurelia-middleware-koa` library is a middleware for [Koa](http://koajs.com/) which instructs the `aurelia-ssr-engine` to render the requested page and returns the fully rendered HTML.

The `aurelia-ssr-bootstrapper-webpack` library exposes three functions: `initialize`, `start` and `stop`. These functions are called by the `aurelia-ssr-engine` for starting and stopping the application and must be the default export of the server bundle.

When a request hits the web server and the middleware kicks off the `aurelia-ssr-engine`, the engine launches a new Aurelia instance. Before it starts the application, the request url is used to set the current URL on the server so that Aurelia renders the page that the user has requested. As soon as the page is rendered, the HTML is sent back to the client. After that, the application is stopped and released from memory.

When the client receives the HTML from the server, it can already render the entire page, so it is much quicker to render the initial content. After that initial render, the browser will fetch the client side bundles and start the Aurelia application on the client.

There is a significant amount of time between the point where the browser has rendered the HTML it received from the server and the point where the browser has finished loading the bundles and starting the application. In this period of time the user can already click on buttons, write in text inputs and so forth, but there is no JavaScript to handle these events since the Aurelia application is not running yet.

This is where a library called `preboot` comes into play. Preboot captures all events that occur between the rendering of the server view and the rendering of the client view. As soon as the application is running on the client, `preboot` replays all events it has captured. This allows your application to handle the events like it would in a setup without Server Side Rendering. For example, when the user clicks on a button in the server view, the click event is registered and stored. After the application has started client-side the click event is replayed and the JavaScript is able to process the click event.

Another interesting aspect is that there are two bundles, one for the client side application and one for the server side application. This allows you to have different entry points for client and server.

## Isomorphic Code

In order to use server side rendering it is necessary to write your code in a way so that it can run both on the client (a browser environment) and on the server (a Node.js environment).

In a setup where there is no Server Side Rendering you could do the following without any issues:

<code-listing heading="DOM Manipulation in the Browser">
  <source-code lang="JavaScript">
    export class MyPage {
      activate() {
        document.body.appendChild(document.createElement('div'));
      }
    }
  </source-code>
</code-listing>

However, since there is no `document` global on the server, you need to use the PAL:

<code-listing heading="DOM Manipulation Using SSR">
  <source-code lang="JavaScript">
    import {DOM} from 'aurelia-pal';

    export class MyPage {
      activate() {
        DOM.appendChild(DOM.createElement('div'));
      }
    }
  </source-code>
</code-listing>

Please refer to the API documentation of the [Platform Abstraction Layer](http://aurelia.io/docs/api/pal) to find out what functions are available.

## Memory Management

Since the application will be executed on the server a lot of times, you'll want to do some memory management. There are a few things to keep in mind.

Using timers like `setTimeout` or `setInterval` should be avoided. If you do need to use them, take care to dispose of them properly and as soon as possible. Otherwise Node.js won't able to unload the application because timers are callbacks to application code, and as long as the timer is active the application can't be garbage collected.

Another thing to avoid are overrides of global prototypes. For example, overriding any function of `Array` (e.g. push, unshift etc) is bad practice since Node.js would not be able to unload the application because a global would have a pointer to a function in the application code.

## Different Main Entry Points

In a non-SSR application you can have a main module (`main.js` or `main.ts`) to configure the application during startup.

While that configuration works when the application runs client-side, you might want a different configuration when the application starts up on the server side.

To accomplish this you can create a `server-main.js` or `server-main.ts` as follows:

<code-listing heading="Alternative Entry Point">
  <source-code lang="JavaScript">
    import { Aurelia } from 'aurelia-framework';
    import { PLATFORM } from 'aurelia-pal';
    import bootstrapper from './ssr-bootstrapper-webpack';

    async function configure(aurelia: Aurelia) {
      aurelia.use
        .standardConfiguration();

      await aurelia.start();
      await aurelia.setRoot(PLATFORM.moduleName('app'));
    }

    module.exports = bootstrapper(configure);
  </source-code>
</code-listing>

Then, in `webpack.server.config.js` you can configure the server bundle to use the `server-main` as the main entry point of your application:

<code-listing heading="Alternative Entry Point in Webpack">
  <source-code lang="JSON">
    entry: {
      server: './src/server-main'
    },
  </source-code>
</code-listing>

## 404 pages

When using Server Side Rendering you will likely want to take care of requests for non-existing routes. This can be done by mapping unknown routes to a 404 module:

<code-listing heading="404 Page in Aurelia">
  <source-code lang="JavaScript">
    export class App {
      router: Router;

      configureRouter(config: RouterConfiguration, router: Router) {
        config.title = 'Aurelia';
        config.options.pushState = true;
        config.options.root = '/';

    	  // routes omitted
        config.map([]);

        config.mapUnknownRoutes(PLATFORM.moduleName('not-found'));

        this.router = router;
      }
    }
  </source-code>
</code-listing>

When a non-existing route is requested by a user, the server will render the 404 page just like any other page.

## Skeleton with SSR Pre-Configured

In order to make it easy for you to get started with Server Side Rendering we have put together a skeleton application that already has Server Side Rendering configured.

The pre-configured SSR skeleton can be found [here](https://github.com/aurelia/skeleton-navigation). Follow the instructions in the README to get started.
