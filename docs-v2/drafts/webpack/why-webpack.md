# Why Webpack

As the front-end landscape constantly evolves, you not only have your Javascript to contend with but rather a plethora of different assets and file formats; CSS, images, fonts, Javascript, TypeScript, JSON, CSS preprocessor formats and more.

Webpack explained in the simplest of terms takes all of your files and assets, builds a dependency graph and outputs bundled code. Think of Webpack as a minifier, bundler, deduper, task runner and every other front-end tool you might be acustomed to, all rolled into one.

In the olden days of web development, you just put a bunch of script tags into the bottom of your application that might have resembled the following:

```
<script src="/scripts/jquery.min.js"></script> 
<script src="/scripts/jquery.plugin.js"></script> 
<script src="/scripts/more-javascript.js"></script> 
<script src="/scripts/main.js"></script>
```

The biggest flaw of the above approach was it resulted in many requests to get those scripts. This approach is slow and while it definitely worked once upon a time, a modern web application built in such a manner would not scale at all.

Then we graduated to concatenation and minification. Instead of four requests, we would take the above files and squash them together into one or two files.

```
<script src="/scripts/vendor-bundle.js"></script> 
<script src="/scripts/main-bundle.js"></script>
```

The above once upon a time would have been achieved using a build tool such as Grunt or Gulp. Whilst it is certainly an improvement, it still has flaws. We are still relying on global variables, in the case of jQuery especially and then plugins would come along and extend said variables, obviously it's a flawed approach.

Somewhere along the way, compounded by the complexity of optimizing our applications using solutions that were flawed came solutions like Browserify and then shortly thereafter Webpack.

In comparison to other alternatives such as using Gulp, Grunt or Browserify, Webpack generates a true dependency graph. This allows Webpack to eliminate dead code, remove duplicated code, split your code into many bundles (with support for lazy loading), intelligently transform assets like fonts and images and everything else in between.

Webpack is a Swiss Army Knife for working with every popular web module format. Due to being configuration over convention, it will slow you down at the start, but provide long-term speed improvements to your workflow and output code.