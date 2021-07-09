---
name: ViewModel property is not being set
description: Troubleshooting passing data to Views
---

Where's my data? Why isn't the ViewModel property set? How to pass data from the
parent View's ViewModel into a child View?

## Debugging if the data is passed to ViewModel ##

### view-spy and compile-spy ###

[view-spy and compile-spy](https://aurelia.io/docs/templating/basics#view-and-compilation-spies
) do not tell you if the data you is passed to your ViewModel.

They are useful to check whether the data is available and could be passed to
the ViewModel.

For Example, with compile-spy you can open devtools and expand
`TargetInstruction > elementInstruction > type > attributes` to see whether the
`BindableProperty` is exposed. The `name` is what it is called in your
ViewModel, the `attribute` is what you must use in your View.

### Use Observable Events to debug if data is being passed to ViewModel ###

Use the [Observables
*changed](https://aurelia.io/docs/binding/observable-properties#observable-properties)
event to debug if the data has been passed to your ViewModel. A `@bindable` is
already an `@observable`.

If your `*changed` method is not being called then you may have a naming
mismatch between the View and ViewModel.

If you expect `firstName` to be set then use this function to check:

```javascript
  firstNameChanged(newValue, oldValue) {
    log.debug(`Firstname changed from '${oldValue}' to '${newValue}'`);
  }
```

## Check correct naming convention used for attribute name ##

See [Aurelia
convention](https://aurelia.io/docs/templating/custom-elements#html-only-custom-element).

The ViewModel naming convention for properties is camelCase.

Data can be bound into the ViewModel's property by using the dash-case
version of its name as an attribute to the View.

If the ViewModel contains `@bindable firstName` then when binding to that value
the attribute must be called `first-name`.

```jss Greeter.js
  @bindable firstName
```

```html using.html Passing data to Greeter
<template>
  <require from="./greeter"></require>
  
  <greeter first-name.bind="aPropertyFromThisTemplatesViewModel"></greeter>
</template
```

## Check the attribute has the .bind suffix ##

See [Bindable
Properties](https://aurelia.io/docs/templating/custom-elements#bindable-properties).

If you do not specify the `.bind` suffix then the data does not go through the
[expression syntax](https://aurelia.io/docs/binding/basics#expression-syntax)
and so the data will not be interpolated, and instead your ViewModel will be
given the raw expression.

If the ViewModel contains `@bindable firstName` then when binding to that value
the attribute must have a suffix `.bind`, for example `first-name.bind`.

For example, the Greeter ViewModel contains a `firstName` and the current View
has a ViewModel property of `data.firstName` that needs to be passed to the Greeter.
  
```javascript
  <greeter first-name.bind="data.firstName"></greeter>
```

## Check the ViewModel property is @bindable ##

See [Bindable
Properties](https://aurelia.io/docs/templating/custom-elements#bindable-properties).

Check that the ViewModel specifies the decorator `@bindable` on the property
that needs to be passed data from a View. If it is missing Aurelia won't set it
to the value passed in.

For example, when the Greeter view is used but the ViewModel property
`firstName` must specify `@bindable`

```javascript use of Greeter view
  <greeter first-name.bind="data.firstName"></greeter>
```

```javascript Greeter.js Greeter ViewModel
import { bindable } from "aurelia-framework";

export class Greeter {
  @bindable firstName;
}
```

## Check the expression syntax is correct ##

See [expression
syntax](https://aurelia.io/docs/binding/basics#expression-syntax)

If the data being passed is `undefined` then your expression syntax is likely
incorrect.

In the current view try using [String
Interpolation](https://aurelia.io/docs/binding/basics#string-interpolation) to
output the value on screen. For example `<p>${data.firstName}>/p>`, where the
current ViewModel must have a property `data.firstName`.

If the expression resolves correctly then check the other trouble shooting hints
above for more details.

## Quizzes ##

Try to resolve these issues:

[noView, processContent
demo](https://codesandbox.io/embed/listen-for-firstname-change-events-egxxn?autoresize=1&codemirror=1&fontsize=18&module=%2Fsrc%2Fgreeter.js&view=split&expanddevtools=1)

[noView, processContent
demo](https://codesandbox.io/embed/viewmodel-property-not-set-problem-1-n6y6h?autoresize=1&codemirror=1&fontsize=18&view=split)

[noView, processContent
demo](https://codesandbox.io/embed/viewmodel-property-not-set-problem-2-b8rej?autoresize=1&codemirror=1&fontsize=18&view=split)

[noView, processContent
demo](https://codesandbox.io/embed/viewmodel-property-not-set-problem-3-zxhez?autoresize=1&codemirror=1&fontsize=18&view=split)

[noView, processContent
demo](https://codesandbox.io/embed/viewmodel-property-not-set-problem-4-zolmo?autoresize=1&codemirror=1&fontsize=18&view=split)


