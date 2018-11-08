---
name: Testing Components
description: An overview of how to unit test Custom Elements and Custom Attributes.
author: Martin Gustafsson (http://github.com/martingust)
---
## Introduction

With the Component Tester you can easily stage a custom element or custom attribute in isolation inside a mini Aurelia application, assert how it responds to data-binding and assert its behavior throughout the component's lifecycle (bind, attached etc).

## Getting Started

If you are using JSPM:

```Shell
jspm install aurelia-testing
```

If you are using NPM:

```Shell
npm install aurelia-testing
```

Once you've got the library installed, you can use it in a unit test. In the following examples we will be using Jasmine, but any testing framework would work.

## Testing a Custom Element

Let's start with a simple custom element that we want to test:

<code-listing heading="A Custom Element's View">
  <source-code lang="HTML">
    <template>
      <div class="firstName">${firstName}</div>
    </template>
  </source-code>
</code-listing>

<code-listing heading="A Custom Element's View-Model">
  <source-code lang="JavaScript">
    import {bindable} from 'aurelia-framework';

    export class MyComponent {
      @bindable firstName;
    }
  </source-code>
</code-listing>

In order to test that the component renders expected HTML, based on what the view is bound to, we can write following test:

<code-listing heading="A Custom Element Test">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
    import {bootstrap} from 'aurelia-bootstrapper';

    describe('MyComponent', () => {
      let component;

      beforeEach(() => {
        component = StageComponent
          .withResources('my-component')
          .inView('<my-component first-name.bind="firstName"></my-component>')
          .boundTo({ firstName: 'Bob' });
      });

      it('should render first name', done => {
        component.create(bootstrap).then(() => {
          const nameElement = document.querySelector('.firstName');
          expect(nameElement.innerHTML).toBe('Bob');
          done();
        }).catch(e => { console.log(e.toString()) });
      });

      afterEach(() => {
        component.dispose();
      });
    });
  </source-code>
</code-listing>

Running the test should result in the following html should be rendered `<div class="firstName">Bob</div>` and the test should pass. But let's take a step back and see what is going on here. First, we import `StageComponent` from `aurelia-testing`:

<code-listing heading="Importing StageComponent">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
  </source-code>
</code-listing>

`StageComponent` is just a convenience factory that creates a new instance of the `ComponentTester` class. `ComponentTester` is the actual class doing all the work. Next we use the `StageComponent` factory to stage our component:

<code-listing heading="Staging The Element">
  <source-code lang="JavaScript">
    component = StageComponent
      .withResources('src/my-component')
      .inView('<my-component first-name.bind="firstName"></my-component>')
      .boundTo({ firstName: 'Bob' });
  </source-code>
</code-listing>

`StageComponent` comes with one property, `withResources`, that lets you start off the staging with a fluent API. `withResources` lets you specify which resource or resources for Aurelia to register.  It takes either a string for registering one single resource or an Array of strings for registering multiple resources. `inView` lets you provide the html markup to be run. This is just a standard Aurelia view where you can do all the data binding you are used to in a full-blown Aurelia application. `boundTo` lets you provide a test `viewModel` with the data that the view will get bound to. In this example, the staging of the component is done in Jasmine's `beforeEach` method in order to reuse the same setup for multiple tests.

> Info
> If you are using `karma` and your configuration already has a path for `'*': 'src/*'` set you may not need to use `src/`, and just `my-component`.

Next, we come to the actual test where we call `create` on the `ComponentTester`. Create will kick everything off and bootstrap the mini Aurelia application, configure it with `standardConfiguration` (we will take a look later at how you can run with your own configuration), register provided resources as global resources, start the application and finally render your component so you can assert the expected behavior. In this case, we want to make sure our `firstName` property gets rendered correctly in the HTML by selecting the `div` tag via it's class name. We use `document.querySelector('.firstName');` to grab that and then check that its innerHTML is `Bob`. Next we call Jasmine's `done` function to tell Jasmine that the test is complete. Calling `done` is needed since the `create` method is asynchronous and returns a Promise.

Finally, we call `dispose` on our `ComponentTester` instance. This will clean up the DOM so our next test will start out with a clean document. That's pretty much all there is to it. Easy right? Imagine doing the same assert with stand alone unit tests that run outside of Aurelia. It would be pretty difficult, especially for a more complex component.


## Manually handling lifecycle

When testing a component sometimes you want to have tests run at certain points of the lifecycle.  To do this we can tell the component we created that we will manually handle the lifecycle methods -

<code-listing heading="Manually handling lifecycle">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
    import {bootstrap} from 'aurelia-bootstrapper';

    describe('MyComponent', () => {
      let component;

      beforeEach(() => {
        component = StageComponent
          .withResources('src/my-component')
          .inView('<my-component first-name.bind="firstName"></my-component>')
          .boundTo({ firstName: 'Bob' });
      });

      it('can manually handle lifecycle', done => {
        let nameElement;

        component.manuallyHandleLifecycle().create(bootstrap)
          .then(() => {
            nameElement = document.querySelector('.name');
            expect(nameElement.innerHTML).toBe(' ');
          })
          .then(() => component.bind())
          .then(() => {
            expect(nameElement.innerHTML).toBe('Foo bind');
          })
          .then(() => component.attached())
          .then(() => {
            expect(nameElement.innerHTML).toBe('Foo attached');
          })
          .then(() => component.detached())
          .then(() => component.unbind())
          .then(() => {
            expect(component.viewModel.name).toBe(null);
          })
          .then(() => component.bind({ name: 'Bar' }))
          .then(() => {
            expect(nameElement.innerHTML).toBe('Bar bind');
          })
          .then(() => component.attached())
          .then(() => {
            expect(nameElement.innerHTML).toBe('Bar attached');
          })
          .then(done);
        });

      afterEach(() => {
        component.dispose();
      });

    });
  </source-code>
</code-listing>

As you see, the test helper lets you easily push components through their lifecycle, testing various aspects of it at each point along the way.

## Testing a Custom Attribute

Testing a Custom Attribute is not much different than testing a Custom Element. Let's look at how it's done by starting with a simple example custom attribute that lets you change the background color of the element it is placed on:

<code-listing heading="A Custom Attribute">
  <source-code lang="JavaScript">
    export class MyAttributeCustomAttribute {
      static inject = [Element];
      constructor(element) {
        this.element = element;
      }

      valueChanged(newValue){
        this.element.style.backgroundColor = newValue;
      }
    }
  </source-code>
</code-listing>

Now, let's assert that the element actually gets the background color it is bound to:

<code-listing heading="A Custom Attribute Test">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
    import {bootstrap} from 'aurelia-bootstrapper';

    describe('MyAttribute', () => {
      let component;

      beforeEach(() => {
        component = StageComponent
            .withResources('src/my-attribute')
            .inView('<div my-attribute.bind="color">Bob</div>')
            .boundTo({ color: 'blue' });
      });

      it('should set the background color to provided color', done => {
         component.create(bootstrap).then(() => {
           expect(component.element.style.backgroundColor).toBe('blue');
           done();
         }).catch(e => console.log(e.toString()));
      });

      afterEach(() => {
        component.dispose();
      });
    });
  </source-code>
</code-listing>

As you can see, everything follows the same pattern we had for our custom element test. One exception is that we take advantage of the `element` property which gets provided by the `ComponentTester` instance. The `element` property is the actual HTML element that gets rendered. This can also be used when testing custom elements.

## Testing custom component with a real view-model

If you want to test a custom component with a real view-model, mocking
out all dependencies, you can do this as well.  A common scenario is
to test the view/view-model, mocking out service calls to the backend.

If the view model has a dependency on a class called Service for all backend communication:

<code-listing heading="A Custom Component with a Real View-model">
  <source-code lang="JavaScript">
    export class MockService {
      firstName;

      getFirstName() { return Promise.resolve(this.firstName);
    }

    describe('MyComponent', () => {
      let component;
      let service = new MockService();

      beforeEach(() => {
        service.firstName = undefined;

        component = StageComponent
          .withResources('src/component')
          .inView('<component></component>');

        component.bootstrap(aurelia => {
          aurelia.use.standardConfiguration();

          aurelia.container.registerInstance(Service, service);
        });
      });

      it('should render first name', done => {
        service.firstName = 'Bob';

        component.create(bootstrap).then(() => {
          const nameElement = document.querySelector('.first-name');
          expect(nameElement.innerHTML).toBe('Bob');

          done();
        });
      });

      afterEach(() => {
        component.dispose();
      });
    });
  </source-code>
</code-listing>

## Using a Real Parent View-model

If you want to test using a custom element inside of a real parent view-model this can be done just as easily.  This can be really helpful when needing to test the state of a parent that is affected by the child custom element or attribute -

<code-listing heading="A Custom Attribute Test with Real Parent View-model">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
    import {bootstrap} from 'aurelia-bootstrapper';
    import {MyComponent} from 'src/my-component';

    describe('MyAttribute', () => {
      let component;
      let viewModel;

      beforeEach(() => {
        viewModel = new MyComponent();
        component = StageComponent
            .withResources('src/my-attribute')
            .inView('<div my-attribute.bind="color">Bob</div>')
            .boundTo(viewModel);
      });
      //...
    });
  </source-code>
</code-listing>

Using this you can also use the `ref` custom attribute to get access to things and check their state in the view-model.

Or if your view-model has dependencies to load through DI -

<code-listing heading="A Custom Attribute Test with Real Parent View-model with DI dependencies">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
    import {bootstrap} from 'aurelia-bootstrapper';
    import {MyComponent} from 'src/my-component';
    import {Container} from 'aurelia-dependency-injection';
    import {MyService} from 'src/my-service';

    describe('MyAttribute', () => {
      let component;
      let container;
      let viewModel;
      let myService;

      beforeEach(() => {
        container = new Container();
        myService = container.get(MyService);
        viewModel = container.get(MyComponent);
        component = StageComponent
            .withResources('src/my-attribute')
            .inView('<div my-attribute.bind="color">Bob</div>')
            .boundTo(viewModel);
      });
      //...
    });
  </source-code>
</code-listing>

Now the service dependency for `MyComponent` will be resolved through DI automatically.

## Improving Readability with Multi-line Strings

You can improve the readability of your complex views by using template literals in your tests -

<code-listing heading="Multi-line Strings for Views">
  <source-code lang="JavaScript">
    import {StageComponent} from 'aurelia-testing';
    import {bootstrap} from 'aurelia-bootstrapper';

    describe('MyAttribute', () => {
      let component;

      beforeEach(() => {
        let view = `
          <div class="row">
            <div class="col-xs-12">
              <div my-attribute.bind="color">Bob</div>
            </div>
          </div>
        `;
        component = StageComponent
            .withResources('src/my-attribute')
            .inView(view)
            .boundTo(viewModel);
      });
      //...
    });
  </source-code>
</code-listing>

## Helpful Properties and Functions

The `ComponentTester` exposes a set of properties that can be handy when doing asserts or to stage a component in a specific way. Here's a list of what is available:

* `element` - The HTML element that gets rendered.
* `viewModel` - The view-model for the component.
* `configure` - The `ComponentTester`'s configure method can be overwritten in order to set it up with custom configuration or get a reference to the `container` instance.
* `dispose` - Cleans up the DOM after a test has run.
* `bind` - Manually handles `bind`.
* `unbind` - Manually handles `unbind`.
* `attached` - Manually handles `attached`.
* `detached` - Manually handles `detached`.
* `waitForElement` and `waitForElements` - Waits until one or several elements are present / absent. See below.

## Testing complex components

In some cases, the tested element is not rendered yet when the `component.create()` promise is resolved, and therefore when the actual test starts. For these situations, `aurelia-testing` and `ComponentTester` expose helper methods and functions to wait for tested elements to be present in the page.

### Waiting for element(s)

If you want to wait for elements that can be looked up in the DOM using a query passed to `querySelector` or `querySelectorAll`, you can use one of the following:

* `ComponentTester.waitForElement` or `ComponentTester.waitForElements`: to wait for one or several HTML element(s) within the tested component. The query is carried out using `querySelector` and `querySelectorAll`, respectively.
* `waitForDocumentElement` or `waitForDocumentElements` (imported from `aurelia-testing`): to wait for one or several HTML element(s) within the document, not restricted to the descendants of the tested component. This is especially useful if you want to wait for elements created by third-party libraries such as context menus, date pickers, etc.

All these methods and functions take 2 arguments:

* `selector` (mandatory): is a selector string to look up the wanted element(s). It must be compatible with `querySelector` and `querySelectorAll`
* `options` is an object that can have the following properties:
  - `present`: `true` to test for presence, `false` for absence (defaults to `true`)
  - `interval`: the polling interval (defaults to 50ms)
  - `timeout`: the timeout (defaults to 5s)

They all return a `Promise` that resolves to an `Element` (`waitForElement`) or a `NodeList` (`waitForElements`). The `Promise` is rejected in the event of a timeout. The returned `Promise` can be used to execute some testing code only once a given element has been detected to be present or absent, either because the component was slow to be fully rendered or because the test relies on asynchronous actions such as events or animations.

### Waiting for matches to complex queries ... or anything else

If your query is complex (with non-trivial jQuery lookups for example), or you want to wait for the result of a callback to be something else than `null`, you can use the higher-level `waitFor` function imported from `aurelia-testing`.

`waitFor(getter, options)` works exactly the same way as the previously described methods and functions, but takes a callback (`getter`) as the first argument instead of a selector string. `waitFor` internally calls `getter` with no arguments at regular intervals times until the returned value is anything else than `null`, an empty `NodeList` or jQuery set. The returned `Promise` will resolve to the result of `getter()`.

### Examples

<code-listing heading="Here is how to wait for the `firstName` input control from the example above:">
  <source-code lang="JavaScript">
    component.waitForElement('.firstName').then((nameElement) => {
      expect(nameElement.innerHTML).toBe('Bob');
      done();
    });
  </source-code>
</code-listing>

<code-listing heading="... and here is the same using jQuery:">
  <source-code lang="JavaScript">
    import {waitFor} from 'aurelia-testing';

    waitFor(() => $('.firstName')).then((nameElement) => {
      expect(nameElement.html()).toBe('Bob');
      done();
    });
  </source-code>
</code-listing>

<code-listing heading="Waiting for the same element to be absent but timeout after 2s is as easy as:">
  <source-code lang="JavaScript">
    component.waitForElement('.firstName', {present: false, timeout: 2000}).then(done);
  </source-code>
</code-listing>
