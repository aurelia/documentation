# The Lifecycle Explained

Much like other frameworks and libraries, Aurelia utilizes the concept of a lifecycle inside of its components. Each lifecycle event allows you to perform specific actions at different times of the component lifecycle or where needed, override parts of the framework itself.

The lifecycle is a sequence of events run in a particular order during the initialization stage of a component. Every time you write a view-model, you have access to a view lifecycle which is what we will be talking about first.

## View Lifecycle

Rendered views, custom attributes and custom elements (anything with a view-model) has a view lifecycle. There is another lifecycle that exists within Aurelia called a router lifecycle which is explained in another section below.

### Lifecycle diagram

Below is a diagram of each view lifecycle event. Don't worry, we will go into more detail further on, this diagram only serves to show you the big picture of how everything pieces together.

![aurelia view lifecycle diagram](https://user-images.githubusercontent.com/368330/29503236-3e1e3c4c-8679-11e7-939a-2ab11d4d6985.png)
