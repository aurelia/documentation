---
name: HTTP Services
description: Usually JavaScript applications need to get their data from or communicate with various web services. In this article we'll take a look at the options Aurelia makes available to you.
author: Bryan Smith (https://twitter.com/bryanarrsmith)
---
## Options

When building front-end applications, it's often necessary to leverage HTTP services to acquire data or persist state. There are many ways to accomplish this. You could use an advanced data library that implements object-relational mapping, an opinionated restful client or just a simple HTTP library. All these options are provided by the global web community and all of them can be used in an Aurelia application.

That said, the Aurelia team felt the need to provide a simple solution out of the box. We wanted our community to have a directly supported option while still keeping Aurelia open so that our own community could innovate or leverage any other library developed elsewhere.

Along these lines, the Aurelia project provides two options:

* `aurelia-http-client` - A basic HttpClient based on XMLHttpRequest. It supports all HTTP verbs, JSONP and request cancellation.
* `aurelia-fetch-client` - A more forward-looking HttpClient based on the Fetch specification. It supports all HTTP verbs and integrates with Service Workers, including Request/Response caching.

How should you choose between these two options? We recommend using `aurelia-fetch-client` if possible. It is based on the Fetch specification which will be the preferred way of handling all AJAX going forward. However, if you need request cancellation or download progress, the Fetch spec does not currently support these features. While these improvements to the spec are being considered for the future, it isn't available at present. So, if you need these capabilities, you may need to use `aurelia-http-client` instead.

## aurelia-fetch-client

As previously stated, the `aurelia-fetch-client` library aims to embrace and expose the new [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), while providing features important in web applications: default configuration of request parameters, interceptors and centralized request tracking. The main method, `HttpClient#fetch()`, has the same signature as `window.fetch()`. The difference is that our `HttpClient` will apply default configuration values, execute any registered interceptors and track the number of active requests.

> Info: Learn About The Fetch Spec
> If you are looking for some information on the Fetch API specification, we recommend that you start with the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API). You may also find [Jake Archibald's intro to the Fetch API](http://jakearchibald.com/2015/thats-so-fetch/) useful.

### Bring Your Own Polyfill

This library relies on the Fetch API, which is not yet supported by all popular browsers. *This library does not include a polyfill for Fetch.* If you need to support [browsers that have not implemented Fetch](http://caniuse.com/#feat=fetch), you will need to install a polyfill like [GitHub's Fetch polyfill](https://github.com/github/fetch).

First, install the polyfill using your package manager. Second, make sure to import the polyfill into your application code so that it's correctly initialized before you use our fetch client. The best place to load the polyfill is typically in your application's `main` module. That might look something like this:

<code-listing heading="Initializing the Fetch Polyfill">
  <source-code lang="ES 2015/2016">
    import 'whatwg-fetch';

    export function configure(aurelia) {
      aurelia.use
        .standardConfiguration()
        .developmentLogging();

      aurelia.start().then(() => aurelia.setRoot());
    }
  </source-code>
  <source-code lang="TypeScript">
    import {Aurelia} from 'aurelia-framework';
    import 'whatwg-fetch';

    export function configure(aurelia: Aurelia): void {
      aurelia.use
        .standardConfiguration()
        .developmentLogging();

      aurelia.start().then(() => aurelia.setRoot());
    }
  </source-code>
</code-listing>

> Info: Encapsulate HttpClient Use
> Generally, we recommend that you don't litter your application code with usage of the HttpClient. Instead, we reccommend that you create one or more service classes that encapsulate all HTTP access behind a friendly, application-specific API. If you do this, we also recommend that you import the `fetch` polyfill inside these service modules rather than in your application's main module. This helps to preserve the encapsulation.

### Basic Use

Data requests are accomplished by calling the `fetch` method on an instance of `HttpClient`. By default, `fetch` makes `GET` requests. All calls to `fetch` return a `Promise` which will resolve to a `Response` object. Using this `Response` object, you can easily parse content, read headers and inspect status codes. See the Fetch API spec for more information.

Below is a simple sample demonstrating a basic `GET` request to a JSON file, including parsing of the response content and writing data values out to the console.

<code-listing heading="GET JSON with Fetch">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    import {HttpClient} from 'aurelia-fetch-client';

    let client = new HttpClient();

    client.fetch('package.json')
      .then(response => response.json())
      .then(data => {
        console.log(data.description);
      });
  </source-code>
</code-listing>

### Configuration

An `HttpClient` instance can be configured with several options, such as default headers and interceptors to be run on requests or responses.

<code-listing heading="Configuring Fetch Client">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    httpClient.configure(config => {
      config
        .withBaseUrl('api/')
        .withDefaults({
          credentials: 'same-origin',
          headers: {
            'Accept': 'application/json',
            'X-Requested-With': 'Fetch'
          }
        })
        .withInterceptor({
          request(request) {
            console.log(`Requesting ${request.method} ${request.url}`);
            return request;
          },
          response(response) {
            console.log(`Received ${response.status} ${response.url}`);
            return response;
          }
        });
    });
  </source-code>
</code-listing>

In the example above, `withBaseUrl()` is used to specify a base url that all fetches will be relative to.

`withDefaults()` allows passing an object that can include any properties described in the optional `init` parameter to the [Request constructor](https://developer.mozilla.org/en-US/docs/Web/API/Request/Request), and will be merged into the new [Request](https://developer.mozilla.org/en-US/docs/Web/API/Request) before it is passed to the first request interceptor.

`withInterceptor()` enables passing an object which can provide any of these four optional methods: `request`, `requestError`, `response`, and `responseError`. Here's an explanation of how each of these methods work:

* `request` takes the [Request](https://developer.mozilla.org/en-US/docs/Web/API/Request) that will be passed to `window.fetch()` after interceptors run. It should return the same Request, or create a new one. It can also return a [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) to short-circuit the call to `fetch()` and complete the request immediately. Errors thrown in request interceptors will be handled by `requestError` interceptors.
* `requestError` acts as a Promise rejection handler during Request creation and request interceptor execution. It will receive the rejection reason, and can either re-throw, or recover by returning a valid Request.
* `response` will be run after `fetch()` completes, and will receive the resulting [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response). As with `request`, it can either pass the Response along, return a modified response, or throw.
* `responseError` is similar to `requestError`, and acts as a Promise rejection handler for response rejections.

These methods on the interceptor object can also return `Promise`s for their respective return values.

### Helpers

The Fetch API has a couple gotchas, documented by the [GitHub Fetch polyfill](https://github.com/github/fetch#caveats) docs. `aurelia-fetch-client` provides configuration helpers to apply the changes suggested by the polyfill docs.

* `config.rejectErrorResponses()` will add a response interceptor that causes responses with unsuccessful status codes to result in a rejected Promise.
* `config.useStandardConfiguration()` will apply `rejectErrorResponses()`, and also configure `credentials: 'same-origin'` as a default on all requests.
* The Fetch API has no convenient way of sending JSON in the body of a request. Objects must be manually serialized to JSON, and the `Content-Type` header set appropriately. aurelia-fetch-client includes a helper called `json` for this.

<code-listing heading="POST JSON with Fetch">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    import {HttpClient, json} from 'aurelia-fetch-client';

    let comment = {
      title: 'Awesome!',
      content: 'This Fetch client is pretty rad.'
    };

    httpClient.fetch('comments', {
      method: 'post',
      body: json(comment)
    });
  </source-code>
</code-listing>

### A Complete Example

This example creates a new `HttpClient` and configures it for use with an imaginary JSON API for managing comments at `api/`. The client is then used to POST a new comment to the API and display an alert dialog with the assigned comment ID.

<code-listing heading="POST JSON with Fetch">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    import {HttpClient, json} from 'aurelia-fetch-client';

    let httpClient = new HttpClient();

    httpClient.configure(config => {
      config
        .useStandardConfiguration()
        .withBaseUrl('api/')
        .withDefaults({
          credentials: 'same-origin',
          headers: {
            'X-Requested-With': 'Fetch'
          }
        })
        .withInterceptor({
          request(request) {
            let authHeader = fakeAuthService.getAuthHeaderValue(request.url);
            request.headers.append('Authorization', authHeader);
            return request;
          }
        });
    });

    let comment = {
      title: 'Awesome!',
      content: 'This Fetch client is pretty rad.'
    };

    httpClient
      .fetch('comments', {
        method: 'post',
        body: json(comment)
      })
      .then(response => response.json())
      .then(savedComment => {
        alert(`Saved comment! ID: ${savedComment.id}`);
      })
      .catch(error => {
        alert('Error saving comment!');
      });
  </source-code>
</code-listing>

### Limitations

* This library does not include a polyfill for Fetch. If you need to support [browsers that have not implemented Fetch](http://caniuse.com/#feat=fetch), you will need to install a polyfill like [GitHub's Fetch polyfill](https://github.com/github/fetch).
* This library does not work around any of the existing limitations in the Fetch API, including:
  * Fetch does not currently support aborting requests or specifying request timeouts.
  * Fetch does not currently support progress reporting.
* JSONP support is not currently provided by this library.
* The [Request constructor](https://developer.mozilla.org/en-US/docs/Web/API/Request/Request) provides its own default values, so if a Request is created before invoking `HttpClient#fetch` (eg, the `HttpClient#fetch(request)` signature is used instead of the `HttpClient#fetch(url, params)` signature), there is no way for the client to know which default values to merge into the Request. The base URL and headers can be merged, but currently no other defaults will be applied in this case.

If your application has requirements that don't fit within these limitations, then you may want to look into using `aurelia-http-client` instead.

## aurelia-http-client

In addition to `aurelia-fetch-client`, Aurelia includes a basic `HttpClient` to provide a comfortable interface to the browser's `XMLHttpRequest` object. Like `aurelia-fetch-client`, `aurelia-http-client` is not included in the modules that Aurelia's bootstrapper installs, since it's completely optional and many apps may choose to use a different strategy for data retrieval. So, if you want to use it, first you must install it with your preferred package manager.

### Basic Use

Once installed, you can use it like this:

<code-listing heading="GET JSON with Basic HTTP">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    import {HttpClient} from 'aurelia-http-client';

    let client = new HttpClient();

    client.get('package.json')
      .then(data => {
        console.log(data.description)
      });
  </source-code>
</code-listing>

The `HttpClient` has a variety of methods. The following is a description of the APIs available.

<code-listing heading="HTTPClient API">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    export class HttpClient {
      isRequesting: boolean;

      constructor();

      configure(fn: ((builder: RequestBuilder) => void)): HttpClient;
      createRequest(url: string): RequestBuilder;
      send(requestMessage: RequestMessage, transformers: Array<RequestTransformer>): Promise<HttpResponseMessage>;

      delete(url: string): Promise<HttpResponseMessage>;
      get(url: string): Promise<HttpResponseMessage>;
      head(url: string): Promise<HttpResponseMessage>;
      jsonp(url: string, callbackParameterName?: string): Promise<HttpResponseMessage>;
      options(url: string): Promise<HttpResponseMessage>;
      put(url: string, content: any): Promise<HttpResponseMessage>;
      patch(url: string, content: any): Promise<HttpResponseMessage>;
      post(url: string, content: any): Promise<HttpResponseMessage>;
    }
  </source-code>
</code-listing>

As you can see, the API provides convenience methods for all the standard verbs as well as `jsonp`. Each of these methods sends an `HttpRequestMessage` except `jsonp` which sends a `JSONPRequestMessage`. The result of sending a message is a `Promise` for an `HttpResponseMessage`.

The `HttpResponseMessage` has the following properties:

* `response` - Returns the raw content sent from the server.
* `responseType` - The expected response type.
* `content` - Formats the raw `response` content based on the `responseType` and returns it.
* `headers` - Returns a `Headers` object with the parsed header data.
* `statusCode` - The server's response status code.
* `statusText` - The server's textual status message.
* `isSuccess` - Indicates whether or not the status code falls within the success range.
* `reviver` - A function used to transform the raw `response` content.
* `requestMessage` - A reference to the original request message.

>Info: JSON By Default
>By default, the `HttpClient` assumes you are expecting a JSON responseType.

### Configuration

You can use `configure` to access a fluent api for configuring all requests sent by the client. You can also use `createRequest` to custom configure individual requests. Here's an example of configuration:

<code-listing heading="Configuring Basic HTTP">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    let client = new HttpClient()
      .configure(x => {
        x.withBaseUrl('http://aurelia.io');
        x.withHeader('Authorization', 'bearer 123');
      });

    client.get('some/cool/path');
  </source-code>
</code-listing>

In this case, all requests from the client will have the base url of 'http://aurelia.io' and will have the specified Authorization header. The same API is available via the `RequestBuilder`. So, you can accomplish the same thing on an individual request like this:

<code-listing heading="RequestBuilder API">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    let client = new HttpClient();

    client.createRequest('some/cool/path')
      .asGet()
      .withBaseUrl('http://aurelia.io')
      .withHeader('Authorization', 'bearer 123')
      .withParams({ abc: '123' })
      .send();
  </source-code>
</code-listing>

The fluent API has the following chainable methods: `asDelete()`, `asGet()`, `asHead()`, `asOptions()`, `asPatch()`, `asPost()`, `asPut()`, `asJsonp()`, `withUrl()`, `withBaseUrl()`, `withContent()`, `withParams()`, `withResponseType()`, `withTimeout()`, `withHeader()`, `withCredentials()`, `withReviver()`, `withReplacer()`, `withProgressCallback()`, and `withCallbackParameterName()`.

It is possible to hook into requests and responses with interceptors as well. Here's an example:

<code-listing heading="Basic HTTP Interceptors">
  <source-code lang="ES 2015/ES 2016/TypeScript">
    let client = new HttpClient();
    client.configure(x => {
        x.withInterceptor({
          request(message) {
            return message;
          },

          requestError(error) {
            throw error;
          },

          response(message) {
            return message;
          },

          responseError(error) {
            throw error;
          }
        });
      });
  </source-code>
</code-listing>

All interceptors used with a client form a chain. The return value of an intercept method is passed on as the argument to the next. Interceptors are called in the order they were added. See the `aurelia-fetch-client` interceptor documentation for more information on interceptors.
