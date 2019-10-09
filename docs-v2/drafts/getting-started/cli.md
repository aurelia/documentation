# Aurelia CLI

The Aurelia CLI is the fastest way possible to get an Aurelia application up and running. At present, the CLI supports scaffolding an Aurelia application using either JSPM/System.js or RequireJS. You will be guided through a series of helpful prompts asking you questions about your setup, if you want testing and so on.

Because the CLI is powered by Node.js, you will need to ensure that you have Node.js and Npm installed on your machine before you proceed.

## Installing the CLI

It could not be any easier. To install the CLI, run the following Npm install command to install the Aurelia CLI globally.

```
$ npm install -g aurelia-cli
```

Depending on your connection speed and machine, this could take a while. After you have installed the CLI, to make sure that it is installed, run the following command and you should see a version number displayed:

```
$ au -v
```

## Generating an Aurelia application

You have the CLI installed, now we are going to be creating an Aurelia application without writing a single line of code through the use of the `au new` command.

By default, if you just run the `au new` command by itself you will be asked for the name of your project. This name value will also be used to create a folder of the same name. If you were to call your application `my-cool-app` it would create a folder of the same name wherever you call the `au new` command.

You can skip the name step entirely by supplying it to the `au new` command like this:

```
$ au new my-cool-app
```

The behavior remains the same. The only difference is you won't be asked for the name of your app, as you have already provided it.

What if you already have an existing folder you want to create an Aurelia application in, like an existing application? The CLI has you covered with the `--here` flag which tells the CLI not to create a new folder and place the files in the current location.

**Assuming you already have a folder called `my-cool-app`:**

```
$ cd my-cool-app
$ au new --here
```

### Which module loader do I choose?

Regardless of how you run `au new` the first screen you will be presented with is the module loader selection screen. Currently, there are two options to pick from: RequireJS (the default) and SystemJS. What you choose here is entirely up to you and at your discretion.

From here on in, you will be presented with a series of prompts asking you what code editor you use, if you want Javascript or TypeScript and whether you want to configure your application for testing. Please ensure that you read each option carefully if you're not familiar.

## Build Environments

Out-of-the-box, the CLI supports three different environments; development, staging and production. By default, the environment is set as `development`. Environments can be used with the `build` and `run` CLI commands in the form of `--env <environment>`.

dev
: Development environment. Example: `au run --env dev`
stage
: Staging environment. Example: `au run --env stage`
prod
: Production environment. Example: `au run --env prod`

## Running a CLI application

The CLI is an all-in-one package that builds, scaffolds and runs your Aurelia application. When you are building out your application, you'll want to run the Aurelia CLI development server which recompiles as you change your code.

The most common command you'll want to run is the `run` task with `watch` flag that tells the development server to recompile your code and refresh the page when something is added or changed.

```
$ au run
```

If you just want to run your application without the auto refresh feature, omit the `watch` flag and it'll just run your application as is.

```
$ au run
```

Don't forget about environments, by default the run task will run as development but for example, if you wanted to serve your site as production, you would run the above task with the following:

```
$ au run --env prod
```

## Building and deploying your app

The CLI provides a build command which works very similarly to the `run` command. You can run it by itself and a development version of your site is created.

```
$ au build
```

And just like the `run` command, you can provide environment values to build your application for different environments. If you are going into production, then all you need to do is run:

```
$ au build --env prod
```

### Working with a CI server or build system?

The `build` command will trace your application dependencies produce bundles inside of the `scripts` directory and update your `index.html` to point to the generated script bundles. If you're working with a CI server like Travis CI or deployment service like TeamCity, then you will want to run the `build` command and copy the `index.html` file and `scripts` folder to your desired location.

## Unit Testing

If you opted to configure your Aurelia application for unit testing, then you can use the `test` command to run your tests.

```
$ au test
```

If you want to adopt a TDD (Test Drive Development) approach to your application, you can supply the `--watch` flag to the `test` command to watch your code/tests and re-run them anytime something changes (like the `build` command).

```
$ au test --watch
```

## Installing Npm Modules

The CLI utilises a project file format in the form of `aurelia.json` which contains all of the dependencies that exist inside of your Aurelia application so that they can be traced.

## Scaffolding application components

The CLI is not just for starting new Aurelia projects, it has a plethora of commands that allow you to scaffold new components very quickly. This is achieved through the `au generate` command being run from within your Aurelia application.

**What generate can do:**

- attribute: Creates a custom attribute and places it into the project resources directory
- binding-behavior: Creates a binding behavior and places it into the project resources directory
- component: Create a custom component comprised of a view-model/view pair and places them into the project source directory
- element: Creates a custom element (view-model/view pair) and places them into the project resources directory
- generator: Creates a generator class and places it into the project generators directory
- task: Creates a task and places it into the project tasks directory
- value-converter: Creates a value converter and places it into the project resources directory

A common scenario will be generating custom components, which are view-model/view pairs that live in your project source directory (in most cases `src`). Much like the `au new` command, we can either provided the name or if we call the command by itself, the CLI will ask for it. Either of the following is valid.

```
$ au generate component
$ au generate component my-component-name
```

If you want to generate custom attributes, elements and anything else you just provide the generator name to `au generate <generator-name>` to run it. As the CLI is continually updated, you should run `au generate` to see a list of valid generators.
