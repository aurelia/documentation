# Aurelia Webpack Plugin

Support for Webpack comes in the form of the Aurelia Webpack Plugin and in its basic form (as can be seen in the Webpck skeletons) it can be added into the `plugins` section as `new AureliaPlugin()` which is pretty standard way of adding in plugins.

The reason this plugin exists is because Aurelia takes a dynamic approach to how it is pieced together. As such, Webpack needs to know about every aspect upfront to compile (it creates a dependency graph) and this plugin is responsible for telling Webpack about the dynamic parts.

## Configuring The Plugin

The Aurelia plugin is quite configurable, accepting an object of one or more configuration options allowing you to choose how the plugin works, what aspects of the Aurelia framework you want to use and to opt out of supporting specific features like SVG bindings and polyfills.

### includeAll

```typescript
includeAll: false | string = false
```

The `includeAll` option allows you to specify to the Aurelia Webpack Plugin that you want to take an entire folder and include it in the final output bundle. This saves you from having to tell the plugin about dynamic dependencies that Webpack can't trace, although it comes with some tradeoffs like increased bundle size.

The following example will tell the plugin to take the entire `src` directory and bundle its contents.

```javascript
new AureliaPlugin({
  includeAll: 'src'
})
```

**NOTE:** When refering to external dependencies, you still need to wrap them into `PLATFORM.moduleName`, for example in a `.plugin('aurelia-datatable')` as they otherwise won't be in the bundle.

### aureliaApp

```typescript
aureliaApp: string | undefined = "main"
```

If you start your application with `aurelia-bootstrapper`, your startup module is probably in a `aurelia-app` attribute in your main HTML file (especially if you're using one of the skeletons. Because this file is generally not parsed by webpack, this initial dependency is lost.

That's why `AureliaPlugin` adds `aureliaApp` as a dependency to your entry point. By default it adds main but you can change it to another name like `app` or `index`.

If you have different startup code that doesn't require this initial dependency, you can remove it with `aureliaApp: undefined`.

Note that if a Webpack DllPlugin is detected, no default value is set. This is because a DLL typically doesn't include your app entry point.

### aureliaConfig

```typescript
aureliaConfig: string | string[] | undefined = ["standard", "developpmentLogging"]
```

When starting an Aurelia app, you commonly call `aurelia.use.standardConfig()` to enable the core default services.

This option makes sure all those services are dependencies of `aurelia-framework`. By default it includes everything, so that it just works.

If you don't use everything you can trim down your build by specifying what you actually use. All framework configurations have a matching string:

```text
"defaultBindingLanguage", "router", "history", "defaultResources", "eventAggregator", "developmentLogging",
"standard", "basic"
```

### dist

```typescript
dist: string | undefined = "native-modules"
```

This lets you easily switch the Aurelia distribution that you use. It adds a webpack resolver that tries to substitute `dist/xxx/` with `dist/[dist option]/` when resolving modules.

By default it is set to `native-modules`, which is a better choice than `commonjs` because it uses ES `import` and `export`, which support Webpack tree-shaking.

### entry

```typescript
entry: undefined | string | string[] = undefined
```

Aurelia sometimes needs to add dependencies that are not attached to a particular module, e.g. when you use `includeAll`, `aureliaApp` or `DLLReferencePlugin`. It also automatically adds `aurelia-loader-webpack` to your entry.

For all those things, `AureliaPlugin` adds them to your Webpack entry point. If you have multiple entry points, it adds them to the first one.

If you need to change that behavior, you can use this option to specify which entry point(s) `AureliaPlugin` should target. Note that this option expects an entry name, not a module name.

### features

```typescript
features: { 
  ie: boolean = true;
  svg: boolean = true;
  unparser: boolean = true;
  polyfills: "es2015" | "es2016" | "esnext" | "none" = "es2015";
}
```

This lets you remove Aurelia features that you don't use from a minified build. It works simply by defining free global variables with `DefinePlugin`.

- `ie: false -> FEATURE_NO_IE = true` saves 4K by removing IE support from `aurelia-pal-browser`.
- `svg: false -> FEATURE_NO_SVG = true` saves 20K but bindings on svg elements won't work anymore.
- `unparser: false -> FEATURE_NO_UNPARSER = true` saves 2K by removing a debugging feature that prints expression AST back into strings. Caution: this is currently used by aurelia-validation, if you use it don't set this to false. See aurelia/validation#412.
- `polyfills -> FEATURE_NO_ES5, FEATURE_NO_ES6, FEATURE_NO_ESNEXT` saves around 10K when set to esnext. You can use this to remove Aurelia's own polyfills if you don't need them (e.g. when targeting modern browsers or when providing your own polyfills). Note: the features required by Aurelia are listed [here](https://github.com/aurelia/polyfills).

### noHtmlLoader

```typescript
noHtmlLoader: boolean = false
```

By default ``AureliaPlugin`` adds ``html-resources-loader`` to .htm and .html resources. This loader detects Aurelia dependencies in views, like `<require from="...">`.

If the loader interferes with your build you can disable it by setting this option to `true`.

Note that if you don't use HTML views but another markup language, you need to manually add `html-resources-loader` to your default loaders at the right place (after your templating loader and before `html-loader`, so that `html-resources-loader` can consume HTML).

If you use `HtmlWebpackPlugin`, which creates static html files to load your app, you should note that this adds a loader for all `.html` files. It means that if you specify a template like `new HtmlWebpackPlugin({ template: 'index.html' }),` it will prevent the fallback ejs loader to kick in (see HtmlWebpackPlugin docs). There are several workarounds:

- use a different file extension like index.ejs.
- specify the loader you want explicitly, maybe prefixing with ! (which disables default loaders): { template: '!html-webpack-plugin/lib/loader!index.html' }
- set `noHtmlLoader: true` and manually use `html-resources-loader` with a more specific test.

### noModulePathResolve

```typescript
noModulePathResolve: boolean = false
```

This modules enables you to reference to files inside a package that might no be at the root.

For example, a library `aurelia-chart` might actually resolve to `aurelia-chart/dist/index.js`. In that setup you normally can't refer to sibling files with `aurelia-chart/pie`, which is what you would use with `aurelia-loader`.

Thanks to that plugin the example `aurelia-chart/pie` would work and resolve to `aurelia-chart/dist/pie.js`.

If the plugin interferes with your build you can disable it by setting this option to `true`.

### noWebpackLoader

```typescript
noWebpackLoader: boolean = false
```

AureliaPlugin automatically inserts `aurelia-loader-webpack` into your build. If you don't want this to happen (e.g. if you use a custom loader), you can set this option to `true`.

### pal

```typescript
pal: string | undefined
```

`AureliaPlugin` automatically bundles the correct `aurelia-pal-***` platform abstraction layer based on your webpack config target.

If you want a specific PAL module or none at all, you can use this option.

### viewFor and viewExtensions

```typescript
viewsFor: string = "src/**/*.{ts,js}" viewsExtensions: string | string[] | Function | Function[] = ".html"
```

Aurelia uses conventions to locate views for custom elements or view models. If your view model does not have `@noView` or `@useView("...")` or `@inlineView("...")` then Aurelia will just try to load a file with the same name but the extension swapped to `.html`.

This pair of options tries to add such dependencies automatically. For all files that match the glob `viewsFor`, it tries to resolve a module with the extension swap to `viewsExtensions` and adds it if it exists.

You typically would change `viewsFor` if your code is not in a `src` folder.

If you pass a string to `viewsExtensions` it will try a simple extension swap. For more complex scenarios you can also pass a function `(filename: string) => string` that takes the current module name and returns the matching view module name.