# The Lifecycle Explained

Much like other frameworks and libraries, Aurelia utilizes the concept of a lifecycle inside of its components. Each lifecycle event allows you to perform specific actions at different times of the component lifecycle or where needed, override parts of the framework itself.

The lifecycle is a sequence of events run in a particular order during the initialization stage of a component. Every time you write a view-model, you have access to a view lifecycle which is what we will be talking about first.

## View Lifecycle

Rendered views, custom attributes and custom elements (anything with a view-model) has a view lifecycle. There is another lifecycle that exists within Aurelia called a router lifecycle which is explained in another section below.

### View Lifecycle Diagram

Below is a diagram of each view lifecycle event. Don't worry, we will go into more detail further on, this diagram only serves to show you the big picture of how everything pieces together.

![aurelia view lifecycle diagram](https://user-images.githubusercontent.com/368330/29503236-3e1e3c4c-8679-11e7-939a-2ab11d4d6985.png)

### View Lifecycle Explained

Further elaborating on our diagram above, let's do a deep dive on the view lifecycle events. Why do we have them, when might we need them and how can you leverage them in your Aurelia applications.

Worth noting, in our above diagram we mention `constructor` and this is not an Aurelia lifecycle event. The constructor is a native method inside of a Javascript class which is called when the class is initialized.

#### created (owningView: View, myView: View)
While the `constructor` itself is the first method called before anything else, `created` is the earliest Aurelia specific callback function that is called before anything else. At this point in the lifecycle, the view instance has been created and the view as well as view-model have been connected to their template controller.

The created callback will receive the instance of the owning view as the first argument, which is the view that the component is delcared inside of. If the component has its own view, this will be passed as the second argument. Custom attributes do not have views, so there will be no second argument passed in for custom attributes.
