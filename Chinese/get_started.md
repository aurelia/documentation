# 入门教程

**欢迎来到 Aurelia！** 这篇教程将指导你使用 Aurelia 创建一个简单的应用并大致说明它的主要概念。我们假设你已经熟悉 JavaScript、HTML 和 CSS。如果只想有个快速预览，建议你直接跳到 “搭建 HTML 页面” 部分，这样你就能立刻看到如何使用 Aurelia 了。等你准备好真正去做一个东西的时候，再回过头来阅读 “配置环境” 和 “设置项目结构及组建”。如果你想阅读本教程的完整文档，请查看 [navigation skeleton project](https://github.com/aurelia/skeleton-navigation/releases)。

> **注：** 想阅读本教程的其他语言版本？请看我们的 [documentation repo](https://github.com/aurelia/documentation)。

## 配置环境

构建现代 JavaScript 应用依赖于一系列很优秀的工具，我们就从配置这些工具开始。本教程所用的工具都基于 [Node.js](http://nodejs.org/) 。如果你已经安装了 Node.js ，那很好！如果没装，到 [Node.js 官网](http://nodejs.org/) 下载安装就行了。所有其他工具都将通过 Node 的包管理器（[npm](https://docs.npmjs.com/getting-started/what-is-npm)）来安装。如果你已经安装了 npm，为了避免其他工具包的一些问题，请确保安装[最新版](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node)。

> **注：** Windows 用户的命令行操作，推荐使用 Git Bash 或者 Git Shell。

首先要安装的是 [Gulp](http://gulpjs.com/) ，用来进行组建自动化（build automation）。如果你还没装 Gulp，那你可以用 npm 安装，像下面这样（可能要用到 `sudo`）：

  ```shell
  npm install -g gulp
  ```

其次需要安装 [jspm](http://jspm.io/) 作为客户端包管理器。可以这样做：

  ```shell
  npm install -g jspm
  ```

> **注：** 跟 Bower 和 Yeoman 一样，jspm 也基于 [git](http://git-scm.com/)，所以如果你没有 git 的话，也需要安装它。另外，jspm 通过查询 GitHub 来安装包，而 GitHub 对匿名的 API 请求做了速度限制。所以我们建议在 jspm 配置里设置好 GitHub，以免出现问题。执行命令 `jspm registry config github` 然后按照提示进行操作即可。如果不想使用密码，那么可以使用 access token 来授权 jspm（查看 Github 的 `Settings > Personal Access Tokens`），选择 `public_repo` 就可以了。

> **注：**不想使用 jspm？没问题，所有 Aurelia 相关的包也都可以通过 [Bower](http://bower.io/) 安装。

## 设置项目结构及组建

安装好所需的工具之后，接下来就可以集中精力搭建应用的基本结构了。从下载 [the navigation skeleton](https://github.com/aurelia/skeleton-navigation/releases) 开始。下载源码，解压缩文件并将目录重命名为 _navigation-app_。

> **注：** 另外，可以使用 [Yeoman](http://yeoman.io/) 在目标文件夹生成项目，如下：
>
> ```
> npm install -g yo generator-aurelia
> yo aurelia
> ```
>
> 这样，同样执行了下面列出的 `npm` 和 `jspm` 命令。

这个目录下已经有所有必须的东西了，包括一个基本组建、包配置文件、样式表等等。

你或许已经看了项目提供的 _index.html_ 和 _src_ _dist_ 目录中的其余文件，然而，我们推荐在开始这篇教程之前删除这些文件。这样做，你可以更有效的学习如何从头构建一个 aurelia app。

一切就绪，让我们来运行一些命令。

1. 打开一个命令行界面，切换目录到 _navigation-app_ 下。
2. 执行下面的命令，安装 package.json 里 _devDependencies_ 部分列出的 Gulp 插件。

  ```shell
  npm install
  ```
3. 下一步，执行以下命令安装 Aurelia、bootstrap 和 font-awesome，它们列在 package.json 里 _jspm.dependencies_ 部分。

  ```shell
  jspm install -y
  ```

目前为止所有的操作都是标准的 Node.js 组建以及包管理流程。跟 Aurelia 本身没有什么关系。我们只是带你走了一遍从零开始搭建一个现代 JavaScript 项目和组建配置的流程。可能你已经很熟悉上面这些内容了，不过假如你不熟悉的话，欢迎来到这个新奇的世界！

> **注：** Aurelia **不**依赖于 Bootstrap 和 Font-Awesome。 我们之所以使用它们作为本教程的一部分，仅仅是为了一个快速可用的漂亮外观。

## 搭建 HTML 页面

如果你跟着我们走到这里了，那你现在已经有所有需要的库了，也已经有创建 Aurelia 应用的组建配置文件和工具了。下一步要做的事情是在项目的根目录下创建 _index.html_ 文件。新建这个文件，并替换为下面的内容。

### index.html
```markup
<!doctype html>
<html>
  <head>
    <title>Aurelia</title>
    <link rel="stylesheet" href="styles/styles.css">
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body aurelia-app>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
      System.import('aurelia-bootstrapper');
    </script>
  </body>
</html>
```

是的，就是这样。这是我们这个应用里唯一的 HTML 页面。这个 HTML 文档的 head 很直观：仅有一个样式表和一些 metadata。有趣的是文档的 body。

从 script 标签开始看。首先是 _system.js_，一个基于ES6 标准的模块加载器，用于加载 Aurelia 类库和你自己的代码。其次是 _config.js_，它包含加载器的各项配置。任何时候执行 jspm 命令，都会自动生成 _config.js_。jspm 是我们推荐的客户端包管理器，它提供了非常棒的与 ES6 兼容的客户端包管理开发体验。

> **注：**  Aurelia 并没有与 jspm 或 SystemJS 绑定。我们同样直接提供 require 风格的 API，像 RequireJS 或 Dojo Loader。你甚至还可以以任何你喜欢的方式实现自己的加载器和包管理器。但我们认为 jspm/SystemJS 是现今最好的面向 ES6 的解决方案，我们真诚推荐它们。

有了模块加载器和相应的配置文件以后，调用 `System.import` 加载 `aurelia-bootstrapper`。

当 bootstrapper 加载完毕后，它会检查 HTML 文档查找 _aurelia-app_ 属性。在本例中，它会看到文档的 body 有一个 `aurelia-app` 属性。这告诉 bootstrapper 去加载 _app_ 的 view-model 和 view，通常是位于 _app.js_ 和 _app.html_，然后 `aurelia-bootstrapper` 会使用它们生成应用的 DOM。

等等…… _app_ 没有 view-model 或者 view 啊。呃……现在怎么办？

## 创建第一个页面

在 Aurelia 里，用户界面元素是由 _view_ 和 _view-model_ 组成的。_view_ 是渲染到 DOM 的 HTML 代码，_view-model_ 是为 _view_ 提供数据和行为的 JavaScript 代码。Aurelia 强大的 _databinding_ 把两者联系起来使得数据的变动将会影响到 _view_， _view_ 的变动也会影响到数据。这种分离的设计概念，对于 开发/设计 协同工作、可维护性、工程灵活性甚至源码控制都是非常棒的。

来看看这是怎么做到的……

在 _src_ 目录下，创建一个 _app.html_ 文件和一个 _app.js_  文件。这就是 `aurelia-bootstrapper` 要查找的 view 和 view-model。先创建一个简单的类（class），这个类有  _firstName_ 和 _lastName_ 属性。接着为这个类添加一个计算属性 _fullName_ 以及一个“欢迎”方法。这是 _app.js_ 看起来的样子：

### app.js
```javascript
export class Welcome{
  constructor(){
    this.heading = 'Welcome to the Aurelia Navigation App!';
    this.firstName = 'John';
    this.lastName = 'Doe';
  }

  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }

  submit(){
    alert(`Welcome, ${this.fullName}!`);
  }
}
```

什么……这是 JavaScript 吗？

是的，是 JavaScript。实际上这是 ECMAScript 2016 (ES7)，是引入了很多新特性的下一代 JavaScript。好在刚才下载的 gulpfile.js 已经设置好 [Babel](https://babeljs.io/) 了。Babel 是一个强大的转义器，允许今天的浏览器运行未来的 JavaScript 代码。现在你可以使用模块(modules)、类(classes)、lambdas、字符串插值等等等等了。太好了！那么，怎么创建一个 _view-model_？新建一个类，然后 _export_ 给 Aurelia。小。菜。一。碟。

> **注：** 并非一定要使用 Babel 或者 ES2016 才能写 Aurelia 应用。还可以使用 TypeScript、CoffeeScript...或者当前浏览器兼容的语言: ES5 and ES2015。只需按照所选语言的标准模式去创造类和其他所有东西就好了。不过我们认为 ES2016 很优秀，希望你能优先考虑它。我们建议阅读 [The Babel Learning Guide](http://babeljs.io/docs/learn-es2015/) 来学习更多有关最新版本的 js 的知识，包括模块导出和类等等。

好了，现在已经有一个具有一些基本的数据和行为的 _view-model_ 了，下面来看看它的同伴—— _view_。

### app.html
```markup
<template>
  <section>
    <h2>${heading}</h2>

    <form submit.delegate="submit()">
      <div>
        <label>First Name</label>
        <input type="text" value.bind="firstName">
      </div>
      <div>
        <label>Last Name</label>
        <input type="text" value.bind="lastName">
      </div>
      <div>
        <label>Full Name</label>
        <p>${fullName}</p>
      </div>
      <button type="submit">Submit</button>
    </form>
  </section>
</template>
```

所有的 _view_ 都包含在一个 `template` 标签里，W3C 关于 Web Components 的标准。上面这个 _view_ 是一个基本的输入表单。看到 `value.bind="firstName"` 了吗？这把输入框的 _value_ 和 view-model 的 _firstName_ 属性绑定在一起。任何时候 view-model 的属性值改变了，输入框的值也变为这个新值；任何时候输入框的值有变动，Aurelia 就会把新值放到 view-model 里。就是这么简单。

这个例子还有一些更有趣的东西。在最后一个 form-group 里，可以看到这种语法：`${fullName}`。这叫做字符串插值，是一种从 view-model 到 view 的单向绑定，它会自动转化为字符串并替换到文档中。最后看一下这个表单元素本身，你应该注意到这句了：`submit.delegate="submit()"`。这是一个事件绑定，使用事件委托（event delegation）来绑定表单的 _submit_ 事件，当表单提交的时候，就会执行 _submit_ 方法。

> **注：** 如果你没听说过事件委托，它是一种更有效的处理浏览器事件的技术，通过把单个事件句柄（event handler）绑定到文档级别来处理同一类型的所有事件，而不是把每个事件句柄单独绑定到每个节点上。

让我们运行 app 看看效果。在命令行界面中，执行下面的命令启动服务器。

```shell
gulp watch
```

现在可以打开 [http://localhost:9000/](http://localhost:9000/) 浏览应用了。在输入框里输入一些东西，注意 fullName 属性随着输入值的变化所作的更新。点击按钮查看哪些方法被调用了。

> **注：** 如果不行，尝试[更新](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node)到 npm 的最新版本。

> **注：** Aurelia 有一个独一无二的、强大的数据绑定引擎，使用自适应技术选择最佳的方式来监视（observe）每个属性的更新。例如，如果使用了支持 [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) 的浏览器，那么 _firstName_ 和 _lastName_ 就会使用这种监视策略。否则，Aurelia 会生成 getter 和 setter 把更动批处理到 Micro Task Queue，准确无误地模仿 Object.observe 的行为。因为计算属性 _fullName_ 无法通过上述两种方式监视，所以使用 dirty checking 来监视它。但是，你可以通过声明它的依赖关系，使得属性被 Aurelia 正确监控。Aurelia 会根据现实情况使用最佳的技术，甚至还可以用插件“教会” Aurelia 如何监视一些特定的模式，使用自定义的策略。这真的很酷。

> **注：** `.bind` 命令对所有的属性都默认使用单向绑定，除了表单控制元素默认使用双向绑定。特定的绑定命令 `.one-way`、`.two-way` 和 `.one-time` 可以随时用来覆盖默认行为。类似地，`.delegate` 可以用于事件委托，同时也能使用 `.trigger` 把事件句柄直接绑定到目标元素上。

## 添加导航

似乎应该为应用再添加一些页面并设置一个客户端路由表，毕竟这是一个导航应用，是不是？先把 _app.js_ 和 _app.html_ 分别重命名为 _welcome.js_ 和 _welcome.html_。这将会是第一个页面。现在，让我们新建一个 _app.js_ 和 _app.html_ 作为 “layout” 、“master page” 或者 “root component”。_view_ 将包含的导航 UI 以及当前页面的内容占位符， _view-model_ 将包含配置了路由的 router 实例。为了表明如何设置路由表，我们从 _view-model_ 开始：

### app.js

```javascript
import 'bootstrap';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome', moduleId: 'welcome', nav: true, title:'Welcome' }
    ]);

    this.router = router;
  }
}
```

好了，这下能看到一些有趣的新东西了。首先，我们将要立即使用 bootstrap 组件，所以我们使用 ES6 的模块系统来加载 bootstrap 组件。其次，我们想使用 router，通过创建并 export 一个实现了 `configureRouter` 回调的叫做 _App_ 的类。这个回调在调用时接受一个配置对象作为参数。通过这个配置对象，你可以设置 document 的 title，然后配置路由。每一项路由，有如下属性：

* `route`：这是当前路由的匹配模式。当模式匹配成功的时候，路由表会导航到这个路由。可以像上面那样使用静态路由，不过也可以使用参数，像这样：`customer/:id`。Aurelia 同样支持通配符路由和查询字符串参数。该属性可以是单个字符串模式，也可以是含有多个模式的数组。

* `name`: 生成路由相应的 URLs 时会在代码层面用到，用于表明路由的唯一性。

* `moduleId`：用于指定对应路由应该渲染的组件，即当前路由要渲染的 view/view-model 对。

* `title`：这个属性是可选的，如果提供了，会用于生成文档的标题。

* `nav`：如果想为当前路由生成一个 UI，也就是说，_navigation model_ 里应当包含当前路由，那么就把这个属性设为 true（或者一个表明优先级的数字）。

> *注：* 文档不全，看代码，还有 `redirect` `viewPorts` `href` `settings`

### app.html

```markup
<template>
  <require from="bootstrap/css/bootstrap.css"></require>
  <require from="font-awesome/css/font-awesome.css"></require>

  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle Navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">
        <i class="fa fa-home"></i>
        <span>${router.title}</span>
      </a>
    </div>

    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
          <a href.bind="row.href">${row.title}</a>
        </li>
      </ul>

      <ul class="nav navbar-nav navbar-right">
        <li class="loader" if.bind="router.isNavigating">
          <i class="fa fa-spinner fa-spin fa-2x"></i>
        </li>
      </ul>
    </div>
  </nav>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

遵循 Aurelia 简单的应用组建约定，`App` 类将会绑定到 _app.html_ 这个 view 上。这段代码的很大一部分都在处理主导航结构。但是，在开始以前...你看到在 view 顶部的 `require` 元素了吗？你可以使用 ES2015 的 `import` 语句，以同样的方式，Aurelia 允许你在 HTML 中使用 `require`。require 元素能够加载、包含功能，例如自定义元素和行为，这里是 CSS。既然我们的例子要使用 bootstrap 的样式和 font-awesome 的 icons，那么我们就使用 `require` 引入它们。这样。我们就可以像上面那样，使用 bootstrap 来布局。但那并不是这个 view 的有趣的部分，我们真正想关注的点是绑定和自定义元素。

既然你已经了解过基本的绑定和字符串插值了，因此我们将注意力集中在新知识上。看一下有着 navbar-nav 类的 ul 元素，它的 li 子元素演示了如何使用表达式 `repeat.for="row of router.navigation"`来实现迭代器。这将会为 `router.navigation` 数组里的每一个项目创建一个 li 元素。row 是一个本地变量，可以看到 li 及其子元素都能使用它。

> **注：** router 的 `navigation` 属性是由路由的配置里所有标记为 `nav:true` 的路由所生成的一个数组。Aurelia 的 `repeat.for` 语法基于 ES6 的 `for..of`。所以，可以通过遍历来生成 UI。

同样，在 `li` 元素上，你也可以看到如何使用字符串插值来动态增删 class 的示例。继续下看，那还有第二个 `ul` 元素。看到它唯一的 `li` 元素的绑定了吗？语句 `if.bind="router.isNavigating"` 根据表达式的值添加或删除这个 `li` 元素。非常方便的是，每当路由改变的时候，都会更新 `isNavigating` 属性。

最后来看一下 view 底部的 `router-view` 元素。这个元素由 Aurelia 官方提供，代表当前路由的页面将要在 DOM 中渲染的位置。


以上这些准备好之后，执行 `gulp watch` 启动服务器，打开浏览器看看。现在应该能看到一个主导航了，只有一个选中的 tab，指向 “welcome” 路径。_welcome_ 这个 view 会像之前那样在主内容区显示。打开浏览器的 debug 工具看看生成的 DOM，可以看到 _welcome_ 的内容在 `router-view` 里面。

> **注：** 如果你让 `gulp watch` 任务在此期间一直运行的话，那你可能已经注意到任何时候你改了代码，浏览器都会自动刷新。这要归功于 `browser-sync`，我们在应用中默认帮你设好了。

## 添加第二个页面

呃，从技术上来说，我们现在已经有了一个导航应用了……但是它并不吸引人，因为只有一个页面。让我们来添加第二个页面。你能猜到怎么添加吗？我打赌你能……

我们来显示 Github 的一些用户吧。首先为这个假想的页面添加路由：

### app.js (更新版)

```javascript
import 'bootstrap';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'],  name: 'welcome',  moduleId: 'welcome',  nav: true, title:'Welcome' },
      { route: 'users',         name: 'users',    moduleId: 'users',    nav: true, title:'Github Users' }
    ]);

    this.router = router;
  }
}
```
如你猜到的那样，我们需要创建一个  _users.js_ 和 _users.html_ 文件。代码如下：

### users.js

```javascript
import {inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-fetch-client';
import 'fetch';

@inject(HttpClient)
export class Users{
  heading = 'Github Users';
  users = [];

  constructor(http){
    http.configure(config => {
      config
        .useStandardConfiguration()
        .withBaseUrl('https://api.github.com/');
    });

    this.http = http;
  }

  activate(){
    return this.http.fetch('users')
      .then(response => response.json())
      .then(users => this.users = users);
  }
}
```

这里有很多很酷的东西。让我们从头开始，我们引入（import） `HttpClient` 和 fetch 的 polyfill。这让我们能够基于即将到来的 Fetch 标准，以非常简单的方式发送 HTTP 请求。`HttpClient` 没有包含在 Aurelia 的默认配置里，因此需要手动安装这个包。在命令行界面执行下述命令即可：

```shell
jspm install aurelia-fetch-client
```

现在认识到这种集成的包管理器和加载器的强大了吧。用 jspm 安装一个包，然后就可以在代码里通过相同的标识符引入这个包，多简单。可以用这种方式从 GitHub 或者 NPM 上安装任何东西。

看一下 ES2016 的 `inject` 装饰器，那是做什么的？好吧，Aurelia 根据需要创建组件来呈现 app。通过使用能够提供构造函数依赖关系（像 HttpClient）的 [依赖注入(Dependency Injection)](https://en.wikipedia.org/wiki/Dependency_injection)容器，来完成这项工作。DI 系统如何知道该提供什么呢？你所要做的是给你的类添加 ES2016 的 `inject` 装饰器，通过传入一系列可以实例化的类型列表。`inject` 装饰器的参数和构造器的参数一一对应。在上面的例子中，我们需要一个 HttpClient 的实例，所以添加一个 `HttpClient` 的类型到 `inject` 装饰器，然后在构造器中添加一个相应的参数。

> *注：* 如果在这种情况下，你不想使用装饰器，你可以在 class 中 添加一个返回注入类型数组的静态方法或属性。如，`static inject() { return [HttpClient]; }`

如果正在使用 TypeScript(>=1.5)，可以添加 `@autoinject` 装饰器，但是在构造器的签名（signature）上使用。代码如下：

```javascript
import {autoinject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-fetch-client';
import 'fetch';

@autoinject
export class Users{
  ...

  constructor(public http:HttpClient){}

  ...
}
```

只要当前路径发生了变化，Aurelia 的路由就会强制 view-model 开始一个新的生命周期（lifecycle）。这叫做 "Screen Activation Lifecycle" or "Navigation Lifecycle"。view-model 可以钩住生命周期的不同部分，控制流程进出路由。如果存在 `activate` 钩子，那么路由就会在路由准备好激活的时候调用这个钩子。在上面的代码中，`activate` 钩子用来调用 Github 的 api 获取一些用户。注意 `activate` 方法返回了 http 请求的结果。`HttpClient` 的所有 API 都返回一个 `Promise`。路由检测到 `Promise` 的时候就一直等到  `Promise` resolve 之后才完成导航。所以，通过这种方式，可以强制路由延迟显示页面，直到有数据生成才显示。

> **注：** 整个的生命周期包括 `canActivate` `activate` `canDeactivate` 和 `deactivate` 钩子，can* 方法通过返回一个 boolean (or Promise of boolean) 来接受或者拒绝当前屏幕的进入或者退出。

> **注：** 如果你不熟悉 [Promise](http://www.html5rocks.com/zh/tutorials/es6/promises/)，它是 ES2015 用来改善异步编程的新特性。一个 `Promise` 代表了一个将来的结果。本质上来讲，它相当于一个在未来某个时间点完成某项工作或者提供某些数据的“承诺”（promise）。

### users.html

```markup
<template>
  <section>
      <h2>${heading}</h2>
      <div class="row au-stagger">
        <div class="col-sm-6 col-md-3 card-container" repeat.for="user of users">
            <div class="card">
                <canvas class="header-bg" width="250" height="70"></canvas>
                <div class="avatar">
                    <img src.bind="user.avatar_url" crossorigin />
                </div>
                <div class="content">
                    <p class="name">${user.login}</p>
                    <p><a target="_blank" class="btn btn-default" href.bind="user.html_url">Contact</a></p>
                </div>
            </div>
        </div>
      </div>
  </section>
</template>
```

这个页面的 view 相当直观。所有的知识点之前都介绍过。

等这一切都准备好的时候，就可以重新启动服务器了。现在应该能看到导航栏有两个项目，而且能够来回切换。哦耶！

让我们总结一下。为应用添加一个页面，只需要：

1. 在 _app.js_ 里添加路由配置。
2. 添加一个 view-model。
3. 添加一个（与 _app.js_）同名的 view（但是需要以 .html 作为文件后缀）。
4. 庆祝。

> **注：** 到这里，你可以看一下这些页面的原始代码。会发现其他一些好东西，例如：动画、元素绑定、值转换器 和 自定义属性。也会发现启动框架和安装第三方插件的替代方案。

## 彩蛋：创建一个自定义元素

瞧瞧你，真是超出了我的预期！看得出来今天你学习的兴致很高。那我们来创建一些自定义 HTML 元素吧。我觉得导航栏是个不错的选择。_app.html_ 文件里有太多 HTML 了，为什么不提取出一个自定义的 `<nav-bar>` 元素让代码更加语义化呢？这是我们最终想要的结果：

### app.html

```markup
<template>
  <require from="bootstrap/css/bootstrap.css"></require>
  <require from="font-awesome/css/font-awesome.css"></require>
  <require from='nav-bar'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```
这段代码从 "nav-bar" 引入一个 `nav-bar` 元素，一旦元素在 view 里可用，就能像其他所有元素一样使用它了，包括针对它的自定义属性（比如 _router_）进行数据绑定。那么，怎么得到这个最终产品？

你猜怎么着？Auelia 简单的 view-model/view 约定对自定义元素依然有效。让我们来创建 _nav-bar.js_ 和 _nav-bar.html_。先看 view-model 的代码：

### nav-bar.js

```javascript
import {bindable} from 'aurelia-framework';

export class NavBar {
  @bindable router = null;
}
```

要创建一个自定义元素，创建和导出一个类即可。由于这个类要在 HTML 中作为元素使用，因此我们需要告诉框架，类的哪些属性应该作为元素的属性出现。_bindable_ 装饰器 可以达到这个目的。跟 _inject_ 类似，_bindable_ 也是一种为 Aurelia 框架提供有关类的信息的方式。Aurelia 通常很智能，可以推断很多事情，不过当它无法推断，或者你想做一些不同寻常的事情时，就要通过装饰器提供一些额外的信息。`bindable` 装饰器告诉框架我们想让类的 `router` 属性成为 HTML 元素的属性。一旦 `router` 成为元素的一个属性，我们就能在 view 里对它进行数据绑定。

### nav-bar.html

```markup
<template>
  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle Navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">
        <i class="fa fa-home"></i>
        <span>${router.title}</span>
      </a>
    </div>

    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
          <a href.bind="row.href">${row.title}</a>
        </li>
      </ul>

      <ul class="nav navbar-nav navbar-right">
        <li class="loader" if.bind="router.isNavigating">
          <i class="fa fa-spinner fa-spin fa-2x"></i>
        </li>
      </ul>
    </div>
  </nav>
</template>
```
这看起来跟原先 _app.html_ 中导航栏部分的 HTML 没什么区别。我们仅仅是把那段代码提取出来放到这个模版里。不过现在它跟 _nav-bar.js_ 绑定在一起了，而不是 _app.js_。

这是一个没有实际行为的非常简单的自定义元素，但它就如上面显示的一样，是完整且可用的。

等一下！

### app.html

```markup
<template>
  <import from='./nav-bar'></import>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

总结：首先有一个 `import` 元素，用于加载自定义元素。这是通过`from` 属性指向的相对资源（relative source）实现的。`import` 遵循 Aurelia 的约定，所以知道如何加载 _nav-bar.js_ 和 _nav-bar.html_。所有通过这种方式引入到一个 view 的东西都只在这个 view 中可见。这样做的结果是，不必担心命名冲突。第二点是，绑定到 `App` 的 router 上元素的实际用处。 `App` 类上的 router 实例最后导向（pipe） `NavBar` 元素的同名属性上，用于元素内部的渲染。漂亮！

> **注：** 你也可以图方便加载应用范围的元素和其他一些行为，这样就不必在每个 view 里都引入公共资源。

你可能会好奇 Aurelia 是如何判断自定义元素的名称的。按照约定，它使用类的名称，只不过变为连接式的小写字母。不过你随时可以使用精确的写法：在 `Behavior` 上再链接一个 `.customElement('nav-bar')`  metadata 。要是自定义元素没有 view 模版怎么办？没问题，链接一个 `.noView()`。想让自定义元素使用 ShadowDOM？像专业人士那样再链接一个 `.useShadowDOM()`即可。不要担心浏览器支不支持，Aurelia 有一个高效而且精确的 ShadowDOM 回退实现（fallback implementation）。

除了能创建自定义元素，还可以在已有的元素上创建独立的属性为之增添一些新的行为。这叫做 _Attached Behaviors_。有时可能还需要创建 _Template Controllers_ 来动态增删 view 里的 DOM，比如上面用到的 `repeat` 和 `for`。Aurelia 能做的远远不止这些，其模版引擎（templating engine）在功能和拓展性上都很强大。

> **注：** AtScript 用户可以利用这门语言的类型注解。那些支持静态类属性的语言（比如 TypeScript）的用户，能够很方便地把 `metadata` 当作属性而不是方法来使用。依赖注入容器所使用的 `inject` 也可以这样。

## 彩蛋：使用子路由

还不满足是吧？那行，给你个好东西：为这个应用添加第三个页面……用它自己的路由！这叫子路由（child router）。所谓子路由，就是有自己的路由配置，并相对于父路由进行导航。准备好精神错乱吧……

### app.js (更新版...的更新版)

```javascript
import {Router} from 'aurelia-router';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['','welcome'],  moduleId: 'welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',        moduleId: 'flickr',       nav: true },
        { route: 'child-router',  moduleId: 'child-router', nav: true, title:'Child Router' }
      ]);
    });
  }
}
```

没什么新鲜东西，好玩的地方在于 _child-router.js_ 里面有什么……

### child-router.js

```javascript
import {Router} from 'aurelia-router';

export class ChildRouter{
  static inject() { return [Router]; }
  constructor(router){
    this.heading = 'Child Router';
    this.router = router;
    router.configure(config => {
      config.map([
        { route: ['','welcome'],  moduleId: 'welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',        moduleId: 'flickr',       nav: true },
        { route: 'child-router',  moduleId: 'child-router', nav: true, title:'Child Router' }
      ]);
    });
  }
}
```

什么？！和 `App` 几乎一模一样的配置？这到底是什么？为什么会这样？呃……可能永远也不该在现实中这么做……但这真的很酷。这，我的朋友，是一个递归路由（recursive router）。我们这么做是因为我们能够这么做。

为了完整起见，这是 view：

### child-router.html

```javascript
<template>
  <section>
    <h2>${heading}</h2>
    <div>
      <div class="col-md-2">
        <ul class="well nav nav-pills nav-stacked">
          <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
            <a href.bind="row.href">${row.title}</a>
          </li>
        </ul>
      </div>
      <div class="col-md-10" style="padding: 0">
        <router-view></router-view>
      </div>
    </div>
  </section>
</template>
```

运行应用，看看有什么神奇的事情发生……祈祷宇宙不会爆炸。

## 结论

Aurelia 异常关注于开发者体验，因此你不但能用它来创建精彩的应用，同时还能享受开发的过程。我们对 Aurelia 所做的设计是让你只需记住一些简单的约定就行了，而不需要仅仅为了能跑起来一个死板或者限制重重的框架而在大量的配置或者样板代码（boilerplate code）上浪费时间。经过仔细的设计，Aurelia 是完全可插（pluggable）和可定制化（customizable）的，使用过程中永远不会有任何障碍。

感谢你花时间读完了这篇入门教程，希望你能深入阅读我们的文档然后做个让人惊艳的东西出来。我们非常期待你的作品。
