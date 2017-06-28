# Conditionals and Loops

Aurelia's templating syntax is intuitive and simple. We will get into templating specifics in a different section, for now we are just taking a quick look at how simple Aurelia's syntax is.

**If you wanted to toggle the visibility of an element in the DOM, it is simply a matter of the following HTML:**

```
<template>
    <h1 show.bind="showMe">Now you see me...</h1>
</template>
```

**Your view-model would look something like this:**

```
export class App {
    constructor() {
        this.showMe = true;
    }
}
```

The `show` attribute sets the element to its original display value or sets it to `display: none` to hide it, but not removed it from the page. If you want to conditionally add and remove an element from the page, in the above example you just replace `show.bind` with `if.bind` and it works the same way only the HTML is added and removed from the page.

You can see a working example and source code of the above in action [here](https://gist.run/?id=8522908d156b4e7b20c360adc63414ef) on Gist.run.

If you wanted to loop over a collection of elements inside of an array, just like any other modern framework or library: there is a method for that. Say hello to `repeat.for` which is akin to a `.forEach` or `for` loop in Javascript, only inside of your HTML templates.

**The View:**

```
<template>
    <ul>
        <li repeat.for="name of names">${name}</li>
    </ul>

    <ol>
        <li repeat.for="name of nameObjects">${name.name} has made ${name.commits} commits.</li>
    </ol>
</template>
```

**The View-model:**

```
export class App {
    constructor() {
        this.names = ['Rob', 'Jeremy', 'Ashley', 'Dwayne'];
        this.nameObjects = [
            {name: 'Rob', commits: 100},
            {name: 'Jeremy', commits: 74},
            {name: 'Ashley', commits: 23},
            {name: 'Dwayne', commits: 4}
        ];
    }
}
```

You can see a working example and source code of the above in action [here](https://gist.run/?id=1fb91f52f659cecfb74cb61e58afbeeb) on Gist.run.