---
name: I18N with Aurelia
description: Getting Started with I18N in your Aurelia App.
author: Vildan Softic (http://pragmatic-coder.net)
---

## Introduction

This documentation explains how to get up and running with Aurelia-I18N
in order to provide localization and internationalization features with your app.

Under the hood it uses [i18next](http://i18next.com/), which is a generalized
open source library with an extensive set of features. By building on top of it
not only can you reuse your work across various other platforms and frameworks
but you are able to use an extensive eco-system full of various packages.

## Installing the Plugin

There are various ways to setup your Aurelia app. It not only boils down
to the question whether you use JavaScript or TypeScript but also which module loader
and bundler is in use.

Aurelia-I18N is tested and optimized to support both JS and TS, as well as the following loader/bundler scenarios:

* Aurelia CLI
* JSPM
* Webpack

Please continue with the section which suites your setup. In addition to this, you must
pick your own backend service. For this guide we're going to leverage the [XHR backend plugin](https://github.com/i18next/i18next-xhr-backend),
or a variation of this plugin — `aurelia-i18n-loader` — that uses the aurelia loader, which is bundled with the aurelia-i18n plugin.
We'll discuss TypeScript specifics in a later section.

### Aurelia CLI

In order to install the Plugin with a CLI Project, first install the plugin via npm, from within the root folder of your project:

```Shell
npm install aurelia-i18n --save
```

Since Aurelia-I18N is backed by i18next, you should install it and a backend plugin of your choice. You can use the built-in backend that uses aurelia's loader or any of your choice.
As an example we're going to leverage the i18next-xhr-backend:

```Shell
npm install i18next i18next-xhr-backend --save
```

After that we need to tell our CLI App about the new dependencies. To do so we're going to open the file *aurelia_project/aurelia.json* and scroll down to the section named *dependencies*. In there add the following three entries:

```JSON
{
  "name": "i18next",
  "path": "../node_modules/i18next/dist/umd",
  "main": "i18next"
},
{
  "name": "aurelia-i18n",
  "path": "../node_modules/aurelia-i18n/dist/amd",
  "main": "aurelia-i18n"
},
{
  "name": "i18next-xhr-backend",
  "path": "../node_modules/i18next-xhr-backend/dist/umd",
  "main": "i18nextXHRBackend"
}
```

> Info
> If you're planning to use the built-in `aurelia-i18n-loader` there is no need for an additional configuration and you're fine with the first two entries.

### JSPM

In your project install the plugin via `jspm` using the following command:

```Shell
jspm install aurelia-i18n
```

And optionally install the backend service using:

```Shell
jspm install npm:i18next-xhr-backend
```

> Info
> You can skip this part if you're planning to use the built-in aurelia-i18n-loader

### Webpack

Install the `aurelia-i18n` plugin in your project using `npm` and the following command:

```Shell
npm install aurelia-i18n --save
```

or if you prefer yarn

```Shell
yarn add aurelia-i18n
```

Also optionally install the `i18next-xhr-backend` plugin:

```Shell
npm install i18next-xhr-backend --save
```

or using yarn

```Shell
yarn add i18next-xhr-backend
```

> Info
> You can skip this part if you're planning to use the built-in aurelia-i18n-loader

Optionally, but recommended, add `aurelia-i18n` to your project's `Aurelia` bundles list in the `webpack.config.babel.js` (assuming you used the `skeleton-navigation` webpack build as your base). This will put the plugin in the `Aurelia` chunk, not the `App` chunk.

An example based directly on `skeleton-navigation`:

```JavaScript
const coreBundles = {
  bootstrap: [/* snip (to keep example short) */],
  aurelia: [
    /* snip (to keep example short) */
    'aurelia-i18n' // add aurelia-i18n to the array
  ]
}
```

## Setting up the Plugin

Now that you have installed and configured your loader, these are the steps to get started with the plugin.

First, use Manual Boostrapping. Open your `index.html` and locate the element with the attribute aurelia-app. Change it to look like this:

<code-listing heading="Setting up Aurelia Bootstrapping">
  <source-code lang="HTML">
    <body aurelia-app="main">
      ...
    </body>
  </source-code>
</code-listing>

> Info
> If you're using one of the [Aurelia Skeletons](https://github.com/aurelia/skeleton-navigation) as your base this is already done and you can safely skip this step. [See here to learn more about manual bootstrapping.](http://aurelia.io/hub.html#/doc/article/aurelia/framework/latest/cheat-sheet/1).

Second, create a folder named `locales` in your project's root.

Third, for each locale, create a new folder with it's name (e.g. `en`, `de`, ...).

Fourth, in those subfolders create a file named `translation.json` which contains your language specific translations. Below you can find a sample `en-EN` translation file. The full potential of i18next is achieved through a specific translation-file schema. Consult the [i18next docs](http://i18next.com/docs/) to find out more about it.

```JSON
{
  "score": "Score: {{score}}",
  "lives": "{{count}} life remaining",
  "lives_plural": "{{count}} lives remaining",
  "friend": "A friend",
  "friend_male": "A boyfriend",
  "friend_female": "A girlfriend"
}
```

Fifth, create (if you haven't already) a file `main.js` in your `src` folder to configure the plugin. Depending on which backend you've chosen there might
be slight differences. The following listings show the configuration for first the built-in aurelia loader, the second using i18next-xhr-backend.

> Info
> Notice that Aurelia I18N makes use of a non-standard attributes option, which is used to define custom aliases besides the default ones, being `t` and `i18n`. Calling `TCustomAttribute.configureAliases` is currently necessary in order make sure that the aliases are defined before view templates are fully processed.

<code-listing heading="Registering the Plugin - using the built-in aurelia loader backed:">
  <source-code lang="ES 2015">
    import {I18N, Backend, TCustomAttribute} from 'aurelia-i18n';

    export function configure(aurelia) {
      aurelia.use
        .standardConfiguration()
        .developmentLogging()
        .plugin('aurelia-i18n', (instance) => {
          let aliases = ['t', 'i18n'];
          // add aliases for 't' attribute
          TCustomAttribute.configureAliases(aliases);

          // register backend plugin
          instance.i18next.use(Backend.with(aurelia.loader));

          // adapt options to your needs (see http://i18next.com/docs/options/)
          // make sure to return the promise of the setup method, in order to guarantee proper loading
          return instance.setup({
            backend: {                                  // <-- configure backend settings
              loadPath: './locales/{{lng}}/{{ns}}.json', // <-- XHR settings for where to get the files from
            },
            attributes: aliases,
            lng : 'de',
            fallbackLng : 'en',
            debug : false
          });
        });

      aurelia.start().then(a => a.setRoot());
    }
  </source-code>
</code-listing>

<code-listing heading="Registering the Plugin - using the i18next-xhr-backend">
  <source-code lang="ES 2015">
    import {I18N, TCustomAttribute} from 'aurelia-i18n';
    import Backend from 'i18next-xhr-backend'; // <-- your previously installed backend plugin

    // if you use TypeScript and target ES5 you might need to import it like this instead
    // import * as Backend from 'i18next-xhr-backend';
    // otherwise add "allowSyntheticDefaultImports": true, to your tsconfig

    export function configure(aurelia) {
      aurelia.use
        .standardConfiguration()
        .developmentLogging()
        .plugin('aurelia-i18n', (instance) => {
          let aliases = ['t', 'i18n'];
          // add aliases for 't' attribute
          TCustomAttribute.configureAliases(aliases);

          // register backend plugin
          instance.i18next.use(Backend);

          // adapt options to your needs (see http://i18next.com/docs/options/)
          // make sure to return the promise of the setup method, in order to guarantee proper loading
          return instance.setup({
            backend: {                                  // <-- configure backend settings
              loadPath: './locales/{{lng}}/{{ns}}.json', // <-- XHR settings for where to get the files from
            },
            attributes: aliases,
            lng : 'de',
            fallbackLng : 'en',
            debug : false
          });
        });

      aurelia.start().then(a => a.setRoot());
    }
  </source-code>
</code-listing>

To have `webpack` serving your translation files for `i18next-xhr-backend`, they need to be copied into the virtual (development) or actual (production) output directory. To copy them from `src/locales/` to `$outputDir$/locales/`, add the following entry under the plugins section of your `webpack.config.js`.

<code-listing heading="Copy translation files for webpack and i18next-xhr-backend">
  <source-code lang="ES 2015">
    plugins: [
      // ...
      new CopyWebpackPlugin([
        { from: 'src/locales/', to: 'locales/' }
      ]),
      // ...
    ]
  </source-code>
</code-listing>

You may also group your translations by namespaces, spread across multiple files. Say you have the standard translation.json and an additional `nav.json` for the navigation items, you can configure aurelia-i18n by passing the `ns` setting in the config object containing the different namespaces as well as the default namespace.
When using namespaces, you will need to prepend string references with `ns:` for those strings that are not in the
defaultNS. For example `t='nav:profile'` would access the `profile` string in `nav.json`.

<code-listing heading="Setting up Multiple Namespaces">
  <source-code lang="ES 2015">
    instance.setup({
      ...
      ns: ['translation','nav'],
      defaultNS: 'translation'
    });
  </source-code>
</code-listing>

### Reflect-Metadata Compatibility

If you are using [`reflect-metadata`](https://www.npmjs.com/package/reflect-metadata), there is an outstanding [compatibility issue](https://github.com/aurelia/i18n/issues/209) which is resolved by ensuring `reflect-metadata` is loaded before Aurelia is initialized. If you are using SystemJS, this can be achieved in your `index.html` as follows:

<code-listing heading="Ensuring reflect-metadata is loaded first">
  <source-code lang="HTML">
    <script>
      System.import('reflect-metadata').then( () => {
        System.import('aurelia-bootstrapper');
      });
    </script>
  </source-code>
</code-listing>

## TypeScript Support

In order to get proper support for autocompletion and typesafety you should install
the necessary type definitions (d.ts) for the plugins dependencies.
Here we show how you can do that for i18next and the i18next-xhr-backend, but this
should be applicable to every other backend choice.

The way to get hold of those is using using npm or perhaps Yarn as with other packages.
In this case the typings are at [@types/i18next](https://www.npmjs.com/package/@types/i18next),
along with the instructions. The command is

```Shell
npm install --save-dev @types/i18next
```

or if you are using Yarn

```Shell
yarn add --dev @types/i18next
```

but in case of yarn there might be a complication as described at https://github.com/yarnpkg/yarn/issues/4226
with a solution proposed to fix complications should they arise.

> Info
> Alternatively, you can find this file in the plugins repository doc folder: `doc/i18next.d.ts`

As for the XHR-Backend you'll be using:

```Shell
npm install --save-dev @types/i18next-xhr-backend
```

or for yarn

```Shell
yarn add --dev @types/i18next-xhr-backend
```

> Info
> Alternative, you can find this file in the plugins repository doc folder: `doc/i18next-xhr-backend.d.ts`

Note: if you decide to use the `doc/*.d.ts` files, you should copy them to another folder, e.g. `custom_typings`.

If you're running a JSPM setup, in order to properly find the `aurelia-i18n.d.ts` file, you can alternatively install it via npm:

```Shell
npm install --save-dev github:aurelia/i18n
```

The next step is to let the compiler know about your `*.d.ts` files. Add the following section to your `tsconfig.json` file.

<code-listing heading="Configuring custom typings in tsconfig.json">
  <source-code lang="ES 2015">
    ... existing configuration code
    "filesGlob": [
        "./typings/browser.d.ts",
        "./your_custom_typings_folder_path/**/*.d.ts", // if you use both typings files from this repository (`doc/*.d.ts`)
      ],
    ... other existing configuration code
  </source-code>
</code-listing>

or if you are using TypeScript 2.0 or later, you can add them to the types section like

<code-listing heading="Configuring custom typings in tsconfig.json">
  <source-code lang="ES 2015">
    ... existing configuration code
    "types": [ "i18next", "i18next-xhr-backend" ]
      ],
    ... other existing configuration code
  </source-code>
</code-listing>

> Warning
> TypeScript will throw errors like `Module xxx not found` either for aurelia-i18n or one of the backends. This is due to the fact that TypeScript does not see proper ES6 exported defaults. So you can now either switch to alias imports `import * as Backend from 'i18next-xhr-backend'` or update your tsconfig with `"allowSyntheticDefaultImports": true` to maintain the same import style.

## Using the Plugin

i18next translations work by setting up an active locale, which you've setup above in the init phase with the property `lng`.

### Setting the active locale

In order to change the active language you'd have to call the function `setLocale(localeName)` via code.

<code-listing heading="Setting the active locale with setLocale">
  <source-code lang="ES 2015">
    import {I18N} from 'aurelia-i18n';

    export class MyDemoVM {
      static inject = [I18N];
      constructor(i18n) {
        this.i18n = i18n;
        this.i18n
            .setLocale('de-DE')
            .then( () => {
          // locale is loaded
        });
      }
      ...
    }
  </source-code>
</code-listing>

### Getting the active locale

To get the active locale you'd go with `getLocale()`:

<code-listing heading="Getting the active locale using getLocale">
  <source-code lang="ES 2015">
    import {I18N} from 'aurelia-i18n';

    export class MyDemoVM {
      static inject = [I18N];
      constructor(i18n) {
        this.i18n = i18n;
        console.log(this.i18n.getLocale());
      }
      ...
    }
  </source-code>
</code-listing>

### Translating via code

Translating stuff via code works by using the method `tr`. You pass in the `key` as its first parameter, followed by the optional second parameter `options` to specify in detail how the translations should be performed. Please consult the [i18next docs](http://i18next.com/translate/#overrideoptions) for a detailed list of those:

<code-listing heading="Translating using the i18n.tr function">
  <source-code lang="ES 2015">
    import {I18N} from 'aurelia-i18n';

    export class MyDemoVM {
      static inject = [I18N];
      constructor(i18n) {
        this.i18n = i18n;
        console.log(this.i18n.tr('mykey'));
      }
      ...
    }
  </source-code>
</code-listing>

### Translating via html attributes

Translation in html can be done alternatively using attributes. By default the plugin is configured to use the `t` and `i18n` attributes.
This can be configured during the plugin registration using the `TCustomAttribute.configureAliases` function and also the `attributes`
property plugin `instance.setup` function parameter.

<code-listing heading="Configuring translation attributes">
  <source-code lang="ES 2015">
    ...
    .plugin("aurelia-i18n", (instance) => {
      ...
      let aliases = ['t','i18n'];
      TCustomAttribute.configureAliases(aliases);
      ...
      instance.setup({
      ...
        attributes : aliases,
      ...
      });
      ...
    });
    ...
  </source-code>
</code-listing>

> Passing the option `skipTranslationOnMissingKey` during plugin initialization, will keep your original contents in place and instead add a warning in the console
about trying to update an element without a matching key.

Any element in your views that has one of those attributes, will be translated when the locale is changed.

<code-listing heading="Attribute based translation with the TCustomAttribute">
  <source-code lang="HTML">
    <span t="title">Title</span>
  </source-code>
</code-listing>

The plugin will use the `title` as the key when translating that element.
Other attributes, specified in the `attributes` option, may be used as well.

<code-listing heading="Attribute based translation with optionally registered I18NCustomAttribute">
  <source-code lang="HTML">
    <span i18n="home.title">Title</span>
  </source-code>
</code-listing>

Notice in the above example that the key was set to `home.title`. This will make the plugin look for a translation with nested objects in your translation json, ie:

<code-listing heading="Nested object translation">
  <source-code lang="ES 2015">
    {
      "home": {
        "title": "Title",
      }
    }
  </source-code>
</code-listing>

Use `i18n.updateTranslations()` to update all translations within the children of the element that is passed to it.

#### Specifying attributes

By default the plugin will set the `textContent` property of an element.

```HTML
//translation
{
  "title": "Title <b>bold</b>"
}

//markup
<span t="title">Title</span>
```

So in above example the html tags will be escaped and the output will be `&lt;b&gt;bold&lt;/b&gt;`.
To allow html-markup to be used, the `[html]` attribute needs to be added before the translation key.

```HTML
<span t="[html]title">Title</span>
```

This will set the `innerHTML` of the element instead of the `textContent` property, so html-markup won't be escaped.
There are 4 special attributes including the shown `[html]`:

* `[text]`:  Sets the `textContent` property (default)
* `[html]`:  Sets the `innerHTML` property
* `[append]`:  appends the translation to the current content already present in the element (allows html).
* `[prepend]`: prepends the translation to the current content already present in the element (allows html).

If the element is a custom element and the value relates to a bindable property of that, then the properties value itself will
be updated.

```HTML
// Custom Element ViewModel
export class Foo {
  @bindable() mybindable = "abc";
}

// Custom Element View
<template>
  <span>${'mybindable'}</span>
</template>

// Rendering the Custom Element and passing validations to custom bindable properties
<foo t="[mybindable]bar"></foo>

// Result
<foo>
  <span>[TRANSLATED VALUE OF BAR KEY]</span>
</foo>
```

Any other values will be used as actual attributes on the element itself.
The following example will not change the content of the element, but will set its `alt` attribute to the translated value of `title` when the locale changes.

```HTML
<span t="[alt]title">Title</span>
```

#### Specifying multiple attributes

Multiple attributes for the same key can be specified by separating them with a comma.

```HTML
<input t="[placeholder,aria-placeholder]placeholder">
```

When the locale changes it will set the `placeholder` and the `aria-placeholder` of the input element to the translated value of `placeholder`.

Multiple attributes for different keys can be specified by separating them with a semicolon.

```HTML
<span t="[html]title;[class]title-class">Title</span>
```

When the locale changes it will set the `innerHTML` to the translated value of `title` due to the `[html]` attribute and the `class` property to the translated value of `title-class`.

#### Using nested and combined translations

In order to combine two or more translations, just include them with the `$t(yourkey)` markup

```HTML
<span t="$t(title) $t(subtitle)">Title subtitle</span>
```

Nested keys may also be referenced and will be properly translated:

<code-listing heading="Nested combined translations">
  <source-code lang="ES 2015">
    {
      "translation": {
        "title": "Title",
        "nested_referencing": 'The $t(title) is the header',
        ...
      }
    }
  </source-code>
</code-listing>

```HTML
<span t="nested_referencing">Nested text</span>
```

#### Translating images

Images can be translated as well, for when a different image needs to be displayed in another language.

```HTML
<img t="home.image" />
```

The plugin will automatically change the `src` attribute of the image when the locale changes.

You may specify a default value for images as well. In order to do so just define an attribute called `data-src` with the default value.

```HTML
<img data-src="path/to/image.jpg" t="home.image" />
```

This will be picked up by the CLI when translations are extracted from the source files. (see the section on [CLI Integration](#cli-integration))

#### Passing parameters to the attribute

In order to use parameters for replaceable parts in your translation key, you can provide an additional `t-params` attribute and bind it to the object containing the replacement values.
Also note that for whatever attribute you registered, the corresponding \*-params attribute will get registered as well automatically.

```JavaScript
// Translation file
{
  "paramstest": "Some text with <strong>{{content}}</strong>"
}
```

<code-listing heading="View for parameter passing to attributes">
  <source-code lang="HTML">
    <!-- View -->
    <span t="[html]paramstest" t-params.bind="params"></span>
  </source-code>
</code-listing>

```JavaScript
// ViewModel
class MyVM {
  params = { content: 'ABC' }

  [...]
}
```

> Info
> The object passed to `t-params` is a complex object explained [in the next section](/doc/article/aurelia/i18n/latest/i18n-with-aurelia/5). To use it via code, see [Complex objects for variables via code](/doc/article/en-US/i18n-with-aurelia.md#complex-objects-for-variables).

### Translating with the TValueConverter

In order to do translations in a more declarative way from within your HTML markup you can use a custom ValueConverter named `t`. It takes exactly the same `options` as the code translation method `tr` but of course provides the key automatically.

You will find below a few examples of the available [i18next features](http://i18next.com/translate/)

<code-listing heading="Declarative translation using the TValueConverter">
  <source-code lang="HTML">
    <template>
      <section>
        <div class="row">
          <div class="col-md-3">
            <h3>ValueConverter Examples</h3>
            <ul class="list-group">
              <li class="list-group-item">
                Translation with Variables: <br />
                ${ 'score' | t: {'score': userScore}}
              </li>

              <li class="list-group-item">
                Translation singular: <br />
                ${ 'lives' | t: { 'count': 1 } }
              </li>

              <li class="list-group-item">
                Translation plural: <br />
                ${ 'lives' | t: { 'count': 2 } }
              </li>

              <li class="list-group-item">
                Translation without/with context: <br />
                ${ 'friend' | t } <br />
                ${ 'friend' | t: { context: 'male' } } <br />
                ${ 'friend' | t: { context: 'female' } }
              </li>
            </ul>
          </div>
        </div>
      </section>
    </template>
  </source-code>
</code-listing>

### Translating with the TBindingBehavior

The TValueConverter is pretty useful if you prefer a declarative way to enhance DOM elements with i18n support. But it has a lack when it comes to automatically updating itself when changes happen outside, like locale switches. This is what the TBindingBehavior can do. Essentially you do the same thing like with the TValueConverter but use the `&` sign instead of `|` to indicate usage of the binding behavior.

<code-listing heading="TBindingBehavior Example">
  <source-code lang="HTML">
  <li class="list-group-item">
    Translation with Variables: <br />
    ${ 'score' & t: {'score': userScore}}
  </li>
  </source-code>
</code-listing>

Now aurelia-i18n will automatically emit signals when internal changes happen and you can do so as well by emiting a `aurelia-translation-signal`. The following example depicts how this is done internally when the current locale changes. First you need to get hold of the `BindingSignaler` exported by the `aurelia-templating-resources` module and inject it either in your constructor or via the static `$inject` property. Next when you want to trigger the signal just use the signalers `signal` method and pass it the predefined string.

<code-listing heading="Signaling a change to the TBindingBehavior">
  <source-code lang="ES 2015">
    import {BindingSignaler} from 'aurelia-templating-resources';
    // inject signaler to constructor ...
    ...

    setLocale(locale) {
      return new Promise( resolve => {
        let oldLocale = this.getLocale();
        this.i18next.setLng(locale, tr => {
          this.ea.publish('i18n:locale:changed', { oldValue: oldLocale, newValue: locale });
          this.signaler.signal('aurelia-translation-signal');
          resolve(tr);
        });
      });
    }
  </source-code>
</code-listing>

> Info
> If you want to only update your relative time binding behaviors, you may use the signal `aurelia-relativetime-signal` which will only trigger those and safe unnecessary update roundtrips

### Complex objects for variables via code

In some cases it might be useful to define variables via complex objects. Let's take a look at below example. It shows a validation message to hint the user that a given field should be in range of min and max.
Now we could easily pass min and max as separate variables but on the other hand that involves more work you'd have to do manually if the source is a object.

<code-listing heading="Complex objects for variables">
  <source-code lang="ES 2015">
    var resources = {
      en: {
        translation: {
          "complex": '{{field}} should be between {{threshold.min}} and {{threshold.max}}'
        }
      }
    };
  </source-code>
</code-listing>

So in order to avoid that you may simply pass in the object as a whole and the library will pickup all the necessary information and create the proper options object.
You can also mix and match it with simple variables.

<code-listing heading="Mixing complex and simple variables">
  <source-code lang="ES 2015">
    var options = {
      'threshold': {
        'min': 1,
        'max': 10
      },
      'field': 'Age'
    };

    i18n.tr('complex', options);
    // --> Age should be between 1 and 10
  </source-code>
</code-listing>

### Formatting numbers via code

For displaying numbers in different formats, this plugin makes use of the [Internationalization API NumberFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat). It leverages the same locales used for the translation methods mentioned in the install process of the plugin.

The API provides access to the `Intl NumberFormat` with the method `NumberFormat`. This function takes the an options object representing the formatting options as the first and the locale as the second parameter.

Below is an example how to access the NumberFormat via code:

<code-listing heading="Number formatting via Code">
  <source-code lang="ES 2015">
    import {I18N} from 'aurelia-i18n';

    export class MyDemoVM {
      static inject = [I18N];
      constructor(i18n) {
        this.i18n = i18n;

        // create a NumberFormat with German locale
        var nf = this.i18n.nf(undefined, 'de');
        var result = nf.format(123456.123);

        console.log(result);
        // output => 123.456,123


        // create a NumberFormat with currency options
        var nf = this.i18n.NumberFormat({ style: 'currency', currency: 'EUR' }, 'de');

        var result = nf.format(123456.123);

        console.log(result);
        // output => 123.456,123 €
      }
      ...
    }
  </source-code>
</code-listing>


### Formatting numbers with NfValueConverter

A more declarative way is to use the `nf` ValueConverter from within your HTML markup. It essentially works the same way as the code version. Take a look at the following example:

<code-listing heading="Declarative formatting of numbers with the NfValueConverter">
  <source-code lang="HTML">
    <div class="col-md-3">
      <h3>ValueConverter Number Examples</h3>
      <ul class="list-group">
        <li class="list-group-item">
          Numberformat with default locale/format:
          ${ 1234567.890 | nf : undefined : selectedLocale}
        </li>
        <li class="list-group-item">
          Numberformat with different locale default format:
          ${ 1234567.890 | nf : undefined : 'de'}
        </li>
        <li class="list-group-item">
          Numberformat with different locale/format:
          ${ 1234567.890 | nf : { style: 'currency', currency: 'EUR' } : 'de'}
        </li>
      </ul>
    </div>
  </source-code>
</code-listing>

> Info
> If you provide the active locale as a bound VM property, the ValueConverter will be re-evaluated as soon as the property value changes, resulting in automatic re-formatting of your number.


### Formatting dates via code

The Intl. API provides means to [format DateTimes](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat) as well. Use the method `df` to access that feature with the same arguments used for NumberFormat
Below you'll find an example how to use those via code:

<code-listing heading="Formatting Dates via Code">
  <source-code lang="ES 2015">
    import {I18N} from 'aurelia-i18n';

    export class MyDemoVM {
      static inject = [I18N];
      constructor(i18n) {
        this.i18n = i18n;

        // create a DateTimeFormat with German locale
        var df = this.i18n.df(undefined, 'de');
        var result = df.format(new Date(2000, 0, 1, 0,0,1))

        console.log(result);
        // output => 1.1.2000


        // create a DateTime with time and 2-digit display
        var options = {
          year: 'numeric', month: '2-digit', day: '2-digit',
          hour: '2-digit', minute: '2-digit', second: '2-digit',
          hour12: false
        };
        var df = this.i18n.df(options, 'de');

        var result = df.format(new Date(2000, 0, 1, 0,0,1));

        console.log(result);
        // output => 01.01.2000 00:00:01
      }
      ...
    }
  </source-code>
</code-listing>

> Info
> Remember that if you pass in `undefined` for the options parameter you'll get the default formatting options

### Formatting dates with DfValueConverter

A more declarative way is to use the `df` ValueConverter from within your HTML markup. It essentially works the same way as the code version. Take a look at the following example, which defines a VM property myDate:

<code-listing heading="Declarative formatting of dates with the DfValueConverter">
  <source-code lang="HTML">
    <div class="col-md-3">
      <h3>ValueConverter Date Examples</h3>
      <ul class="list-group">
        <li class="list-group-item">
          DateFormat with default locale/format:
          ${ myDate | df : undefined : selectedLocale}
        </li>
        <li class="list-group-item">
          DateFormat with different locale default format:
          ${ myDate | df : undefined : 'de'}
        </li>
        <li class="list-group-item">
          DateFormat with different locale/format:
          ${ myDate | df : { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' } : 'de'}
        </li>
      </ul>
    </div>
  </source-code>
</code-listing>

### Rendering relative time

In order to create a representation of relative time like `x days ago` or `in x days` you can leverage the Service relativeTime. This exposes a method `getRelativeTime` which accepts a valid JS date.
To use it via code get hold of the service via injection and call the method as needed:

<code-listing heading="Rendering relative time via Code">
  <source-code lang="ES 2015">
    import {RelativeTime} from 'aurelia-i18n';

    export class MyDemoVM {
      static inject = [RelativeTime];
      constructor(relativeTime) {
        this.rt = relativeTime;

        var myDate = new Date();
        myDate.setHours(myDate.getHours() - 2);

        var result = relativeTime.getRelativeTime(myDate);

        console.log(result);
        // output => 2 hours ago
      }
      ...
    }
  </source-code>
</code-listing>

This is also tied in to the currentLocale of the library so changing that one will also translate relative time messages. Take a look at the file `src/defaultTranslations/relative.time.js` for available
translations. If you're missing yours, I welcome you to provide a PR so everybody can benefit from it.

A more declarative approach is to use the RtValueConverter directly in your HTML markup. It doesn't take any additional parameters, so just drop it in and you're good to go:

<code-listing heading="Declarative relative time using the RtValueConverter">
  <source-code lang="HTML">
    <div class="col-md-3">
      <h3>ValueConverter Relative Time Examples</h3>
      <ul class="list-group">
        <li class="list-group-item">
          2 hours ago:
          ${ myDate | rt }
        </li>
      </ul>
    </div>
  </source-code>
</code-listing>

## Bundle Translation Files

When bundling is used, the built-in backend will read the translations from the bundle with the aurelia loader. Make sure that the `translation.json` files are packed in the bundle using the text module, by changing the `aurelia.json` and adding `.json` as an extension for the text plugin:

```JSON
"loader": {
	"type": "require",
	"configTarget": "vendor-bundle.js",
	"includeBundleMetadataInConfig": "auto",
	"plugins": [
		{
			"name": "text",
			"extensions": [
				".html",
				".css",
				".json"
			],
			"stub": true
		}
	]
},
```

### Using JSPM

If you're using JSPM as your module loader, the bundle configuration might look like.

```JSON
"bundles": {
  "dist/app-build": {
   "includes": [
     "[*.js]",
     "*.html!text",
     "*.css!text",
     "*.json!text"
   ],
```

### Using Aurelia CLI

Same would apply to the Aurelia CLI. In order to tell it to process the locales you can create a new task `process-locales.js`, located in the `aurelia_project/tasks` folder with the contents:

<code-listing heading="Processing locales with a custom task">
  <source-code lang="ES 2015">
    import gulp from 'gulp';
    import changedInPlace from 'gulp-changed-in-place';
    import project from '../aurelia.json';
    import {build} from 'aurelia-cli';

    export default function processLocales() {
      return gulp.src(project.localesProcessor.source)
        .pipe(changedInPlace({firstPass: true}))
        .pipe(build.bundle());
    }
  </source-code>
</code-listing>

With that in place, edit your `aurelia_project/aurelia.json` file and add the task right after the `markupProcessor` configuration, where the source property should reflect your previously setup locales folder:

```JSON
...
"localesProcessor": {
  "id": "none",
  "displayName": "None",
  "fileExtension": ".json",
  "source": "src/locales/**/*.json"
},
...
```

Also, edit your `aurelia_project/tasks/watch.js` (or .ts if you're using Typescript) file and add a new watch object to the `watches` array:

<code-listing heading="Add a locales watch task the watch function">
  <source-code lang="ES 2015">
    import processLocales from './process-locales'; //<-- add this line
    // ...
    let watches = [
      { name: 'transpile', callback: transpile, source: project.transpiler.source },
      { name: 'markup', callback: processMarkup, source: project.markupProcessor.source },
      { name: 'CSS', callback: processCSS, source: project.cssProcessor.source }
      { name: 'locales', callback: processLocales, source: project.localesProcessor.source } //<-- and this line
    ];
  </source-code>
</code-listing>

On older versions of the CLI (before 1.x.x), you would need to edit your `aurelia_project/tasks/run.js` (or .ts if you're using Typescript) file instead, and add a new gulp.watch task to the `watch` function:

<code-listing heading="Add a locales watch task the watch function">
  <source-code lang="ES 2015">
    let watch = function() {
      gulp.watch(project.transpiler.source, refresh).on('change', onChange);
      gulp.watch(project.markupProcessor.source, refresh).on('change', onChange);
      gulp.watch(project.cssProcessor.source, refresh).on('change', onChange);
      gulp.watch(project.localesProcessor.source, refresh).on('change', onChange); //<-- add this line
    };
  </source-code>
</code-listing>

By doing this, if you're running your project with the --watch flag, your bundles will be rebuilt whenever your change something in your translation files. In the same way that happens with your .html, .js and .css files.

Last but not least search for the build/bundles/source section for the app-bundle and update the configuration to include json files.

```JSON
...
"bundles": [
  {
    "name": "app-bundle.js",
    "source": [
      "[**/*.js]",
      "**/*.{css,html,json}"
    ]
  }
  ...
]
```

and include the json extension in the loader plugin part build/loader/plugins/extensions

```JSON
...
"plugins": [
  {
    "name": "text",
    "extensions": [
      ".html",
      ".css",
      ".json"
    ],
    "stub": true
  }
]
...
```
### Using Webpack

As an easy starting point for getting aurelia-i18n to work with webpack or if you don't want your translations files split into several chunks, you might refrain from lazy loading them and instead bundle them all with your app.bundle. With all locales included into the app.bundle, it's size and upfront loading time will increase, but for a small amount of translations, that might not be much of an issue. To do this, you can use the [i18next-resource-store-loader](https://github.com/atroo/i18next-resource-store-loader).

First install the module from within the root folder of your project:

```Shell
npm install i18next-resource-store-loader --save
```

Given following file structure:

```
└── src
      ├── assets
      │   └── i18n
      │       ├── index.js // Add an empty index.js as root pointer here.
      │       ├── de
      │       │   └── translation.json
      │       └── en
      │           └── translation.json
      └── main.js/ts
```

Configure aurelia-i18n as follows:

<code-listing heading="Configuring the i18next-resource-store-loader">
  <source-code lang="ES 2015">   
    import {PLATFORM} from 'aurelia-pal';
    // Pass the path to the root pointer relative to main to the loader
    import resBundle from 'i18next-resource-store-loader!./assets/i18n/index.js';

    export function configure(aurelia) {
      aurelia.use
        .standardConfiguration()
        .developmentLogging()
        .plugin(PLATFORM.moduleName('aurelia-i18n'), instance => {
          return instance.setup({
            resources  : resBundle, //<-- configure aurelia-i18n to use your bundled translations
            lng        : 'de',
            attributes : ['t'],
            fallbackLng: 'en',
            debug      : false,
          });
        });
      
      aurelia.start().then(() => aurelia.setRoot(PLATFORM.moduleName('app')));
    }
  </source-code>
</code-listing>

For additional information about the resource store loader please take a look at the [official repos information](https://github.com/atroo/i18next-resource-store-loader).

## Internationalization API Polyfill

The plugin leverages the JavaScript Internationalization API to perform certain tasks. Since not all browsers do fully support it ([compatibility table](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Browser_compatibility)) the aurelia-i18n conditionally loads the Polyfill if needed.

In case of bundling your app you should thus keep in mind that it will not be automatically included into the bundle. That means you have to manually adjust the bundle config to include the polyfill as well, if you intend to have your application run on browsers without full support. [related GitHub issue](https://github.com/aurelia/i18n/issues/61#issuecomment-178801842)

### Bundling the Intl Polyfill with Aurelia CLI

Aurelia-I18N uses a polyfill to provide Intl.API support for browsers currently not implementing the feature.
Amongst those are Safari for Mac and iOS. The Polyfill will be lazy loaded in dev mode as needed but won't be included
automatically as part of the bundle. The reason is the decent file size of ~50k in minified mode, as such you as the developer
have to opt-in and bundle the Polyfill manually.

To do so first install the Polyfill as part of your project using npm:

```Shell
npm install intl --save
```

After that open up the *aurelia_project/aurelia.json* file and add the following dependency:

```JSON
{
  "name": "intl",
  "path": "../node_modules/intl/dist",
  "main": "Intl.min"  // or Intl.complete
},
````

> Info
> Instead of referencing the min file you could also reference `Intl.complete` to include the Unicode CLDR locale data. For
more info about that consult the [official Polyfill docs](https://github.com/andyearnshaw/Intl.js/#locale-data).


### Use Internationalization API Polyfill with Webpack

In order to use the Polyfill with Webpack, you will have to adapt your `bootstrap` function.

<code-listing heading="Using the polyfill with Webpack">
  <source-code lang="ES 2015">
    bootstrap(aurelia => {
      if (!global.Intl) {
        console.log('Intl not present')
        require.ensure([
          'intl',
          'intl/locale-data/jsonp/en.js'
        ], function (require) {
          require('intl');
          require('intl/locale-data/jsonp/en.js');
          boot(aurelia);
        });
      } else {
        boot(aurelia);
      }
    });

    function boot(aurelia) {
      aurelia.use
        .standardConfiguration()
        .developmentLogging()
        .plugin('aurelia-i18n', (instance) => {
            // code to setup aurelia-i18n
        });

      aurelia.start().then(() => aurelia.setRoot('app', document.body));
    }
  </source-code>
</code-listing>

When using WebPack, one needs to add `PLATFORM.modulename` to enable [aurelia-webpack-plugin](https://www.npmjs.com/package/aurelia-webpack-plugin)
to properly recognize the plugins. With that change, the example code looks like so:

<code-listing heading="Using the polyfill with Webpack">
  <source-code lang="ES 2015">
    bootstrap(aurelia => {
      if (!global.Intl) {
        console.log('Intl not present')
        require.ensure([
          'intl',
          'intl/locale-data/jsonp/en.js'
        ], function (require) {
          require('intl');
          require('intl/locale-data/jsonp/en.js');
          boot(aurelia);
        });
      } else {
        boot(aurelia);
      }
    });

    function boot(aurelia) {
      aurelia.use
        .standardConfiguration()
        .developmentLogging()
        .plugin(PLATFORM.modulename('aurelia-i18n'), (instance) => {
            // code to setup aurelia-i18n
        });

      aurelia.start().then(() => aurelia.setRoot(PLATFORM.modulename('app'), document.body));
    }
  </source-code>
</code-listing>


Or using TypeScript

<code-listing heading="Using the polyfill with Webpack">
  <source-code lang="TypeScript">
 aurelia.use
        .standardConfiguration()
        .developmentLogging()
        .plugin(PLATFORM.moduleName('aurelia-i18n'), (instance: I18N) =>
            // code to setup aurelia-i18n
        });

      await aurelia.start();
      await aurelia.setRoot('app', document.body);
    }
      </source-code>
</code-listing>


More information [in the README of the Intl.js polyfill](https://github.com/andyearnshaw/Intl.js/#intljs-and-browserifywebpack).

On top of that if you need the Intl polyfill included you have to manually require and bundle it. To do so add the following import statement at the begin of your `main.js/ts` file:

<code-listing heading="Manually requiring the Intl Polyfill">
  <source-code lang="ES 2015">
    //main.js
    import 'intl';
  </source-code>
</code-listing>

For additional information about Intl.js and Webpack please take a look at this [official repos information](https://github.com/andyearnshaw/Intl.js#intljs-and-browserifywebpack)
