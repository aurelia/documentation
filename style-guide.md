#ES6 Style Guide

This style guide is meant to provide a style for the community to follow when building ES6 modules in Aurelia when contributing and for the community if needing a guide.

## General coding styles

1. Always use single spaces after class names
```
export class ClassName {
  constructor(){
  }
}
```

1. Always use spaces during property assignment (between name and value)
```
this.propertyName = property;
```

1. Never use extra spaces in function declarations
```
constructor(){
  this.propertyName = property;
}
```

1. Always end files with an extra line at the end
```
export class ClassName {
  constructor(){
  }
}
```

1. Always end lines with semi-colon (makes build tools' work easier)
```
this.propertyName = 'My Property';
```

1. Always cuddle if / else blocks
```
if(true = false){
  console.log('World has ended')
} else {
  console.log('World is A Ok')
}
```

## Properties

1. Always camelCase property names of properties whose values can be assigned
```
this.propertyName = 'My Property';
```


## Functions

1. Always comma separate function parameters
```
constructor(firstParam, secondParam){
  this.propertyName = secondParam;
}
```
