# 入门教程

**欢迎来到 Aurelia！** 这篇教程将会指导你使用 Aurelia 创建一个简单的应用并简要说明它的主要概念。我们假设你已经熟悉 JavaScript、HTML 和 CSS。如果你想快速有个大致印象，我们建议你直接跳到 “搭建 HTML 页面” 的部分，这样你就能看到如何使用 Aurelia 了。等你准备好真正去做一个东西的时候，再回过头来阅读 “配置环境” 和 “设置项目结构及组建”。如果你想阅读本教程的完整文档，请查看我们的 [navigation skeleton project](https://github.com/aurelia/skeleton-navigation/releases)。

> **注：** 想阅读本教程的其他语言版本？请看我们的 [documentation repo](https://github.com/aurelia/documentation)。

## 配置环境

现代 JavaScript 应用需要一系列很优秀的工具来搭建，我们就从配置这些工具开始吧。我们所有的工具都是建立在 [Node.js](http://nodejs.org/) 上的。如果你已经安装了 Node.js ，那很好！如果没有，你应该到 [Node.js 官网](http://nodejs.org/) 下载安装。我们需要的其他所有工具都将通过 Node 的包管理器（[npm](https://docs.npmjs.com/getting-started/what-is-npm)）来安装。

首先，我们从安装 [Gulp](http://gulpjs.com/) 开始，我们将用它来进行组建自动化（build automation）。如果你还没有安装 Gulp，你可以使用 npm 来安装，像下面这样（你可能需要用到 `sudo`）：

  ```shell
  npm install -g gulp
  ```
  
其次，我们需要安装 [jspm](http://jspm.io/) 作为我们客户端的包管理器。你可以这样做：

  ```shell
  npm install -g jspm
  ```

> **Note:** 跟 Bower 和 Yeoman 一样，jspm 也基于 [git](http://git-scm.com/)，所以如果你没有 git 的话，你需要安装它。另外，jspm 通过查询 GitHub 来安装包，而 GitHub 对匿名的 API 请求做了速度限制。所以我们建议你在自己的 jspm 配置里设置好 GitHub，以免出现问题。你可以执行命令 `jspm endpoint config github` 然后按照提示进行操作。不想使用 jspm？没问题，所有 Aurelia 相关的包也都可以通过 [Bower](http://bower.io/) 安装。

## 设置项目结构及组建

安装好所需的工具之后，我们就可以集中精力来搭建应用的基本结构了。从下载 [the navigation skeleton](https://github.com/aurelia/skeleton-navigation/releases) 开始。下载源码，解压缩并把它重命名为 _navigation-app_。这个目录里有所有你需要的东西，包括一个基本组建，包配置文件，样式表等等。我们希望教你如何去搭建你自己的应用，所以请删除 _src_ 目录和 _index.html_ 文件。你很快就会学习如何创建它们。

一切就绪，让我们运行一些命令。

1. 打开一个命令行界面，切换目录到 _navigation-app_ 下。
2. 执行下面的命令，安装 package.json 里 _devDependencies_ 部分列出的 Gulp 插件。

  ```shell
  npm install
  ```
3. 下一步，执行以下命令安装 Aurelia、bootstrap 和 font-awesome，它们列在 package.json 里 _jspm.dependencies_ 部分。

  ```shell
  jspm install -y
  ```

目前为止我们所做的所有事情都是标准的 Node.js 组建以及包管理流程。跟 Aurelia 本身没有什么关系。我们只是带你走了一遍从零开始搭建一个现代 JavaScript 项目和组建配置的流程。你可能已经很熟悉上面这些内容了，不过假如你不熟悉的话，我们欢迎你来到这个新奇的世界！

> **注：** Aurelia **不**依赖于 Bootstrap 和 Font-Awesome。 我们之所以使用它们作为本教程的一部分，仅仅是为了让我们的应用有一个快速可用的漂亮外观。

## 搭建 HTML 页面

如果你跟着我们走到这里了，那你现在已经有所有需要的类库了，也已经有创建 Aurelia 应用的组建配置文件和工具了。我们下一步要做的事情是在我们项目的根目录下创建 _index.html_ 文件。新建这个文件，并使用下面的内容。

### index.html
```markup
<!doctype html>
<html>
  <head>
    <link rel="stylesheet" type="text/css" href="jspm_packages/github/twbs/bootstrap@3.3.2/css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="jspm_packages/npm/font-awesome@4.2.0/css/font-awesome.min.css">
    <link rel="stylesheet" type="text/css" href="styles/styles.css">
  </head>
  <body aurelia-app>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
      System.baseUrl = 'dist';
      System.import('aurelia-bootstrapper');
    </script>
  </body>
</html>
```

是的，就是这样。这是我们这个应用里唯一的 HTML 页面。这个 HTML 文档的 head 很直观：我们引用了 bootstrap、font-awsome 和你自己的样式表。有趣的是文档的 body。

> **注：** 确保你本地的 bootstrap 和 font-aswome 目录名与文档中 link 的 href 所指的路径保持一致。因为有可能本教程写成之后这些类库的版本更新了。

我们从 script 标签开始。首先我们有 _system.js_，它是我们基于ES6 标准的模块加载器，我们用它来加载 Aurelia 类库和你自己的代码。其次我们有 _config.js_，它包含加载器的各项配置。你在任何时候执行 jspm 命令，都会自动生成 _config.js_。jspm 是我们推荐的客户端包管理器。它提供了令人惊艳的与 ES6 兼容的客户端包管理开发体验。

> **注：**  Aurelia 并没有与 jspm 或 SystemJS 绑定。我们同样直接提供 require 风格的 API，像 RequireJS 或 Dojo Loader。你甚至还可以以任何你喜欢的方式实现你自己的加载器和包管理器。但我们认为 jspm/SystemJS 是现今最好的面向 ES6 的解决方案，我们真诚推荐它们。

有了模块加载器和相应的配置文件以后，我们把 `baseUrl` 指向我们编译后的代码所在位置，然后我们调用 `System.import` 加载 `aurelia-bootstrapper`。

`aurelia-bootstrapper` 在加载的时候会检阅 HTML 文档查找 _aurelia_ 属性。在本例中，它会看到文档的 body 有一个 `aurelia-app` 属性。这告诉它去加载 _app.js_ 和 _app.html_ ，前者是 _app_ 的 view-model，后者是 _app_ 的 view。然后 `aurelia-bootstrapper` 会使用它们生成  Aurelia 应用的 DOM。

等等……我们没有 _app_ 的 view-model 或者 view啊。呃……现在怎么办？

## 创建第一个页面

在 Aurelia 里，用户界面元素是由 _view_ 和 _view-model_ 组成的。_view_ 是渲染到 DOM 的 HTML 代码，_view-model_ 是为 _view_ 提供数据和行为的 JavaScript 代码。Aurelia 强大的 _databinding_ 把两者联系起来使得数据的变动将会影响到 _view_， _view_ 的变动也会影响到数据。

我们来看看这是怎么做到的……

在 _src_ 目录下，创建一个 _app.html_ 文件和一个 _app.js_  文件。这就是 `aurelia-bootstrapper` 要查找的 view 和 view-model。我们开始先创建一个简单的类（class），这个类有  _firstName_ 和 _lastName_ 属性。接着我们为这个类添加一个计算属性 _fullName_ 以及一个“欢迎”方法。这是 _app.js_ 看起来的样子：

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

  welcome(){
    alert(`Welcome, ${this.fullName}!`);
  }
}
```

什么……这是 JavaScript 吗？

是的，是 JavaScript。实际上这是 ECMAScript 6 (ES6)，是引入了很多新特性的下一代 JavaScript。幸运的是在上面的步骤中你下载的 gulpfile.js 已经帮你设置好 [Babel](https://babeljs.io/) 了。Babel 是一个强大的 ES6 编译器，允许你在今天的浏览器上运行未来的 JavaScript 代码。现在你可以使用模块、类、lambda函数、字符串替换等等等等了。太好了！那么，怎么创建一个 _view-model_?新建一个类，然后 _export_ 给 Aurelia。小。菜。一。碟。

> **注：** 你并非一定要使用 Babel 后者 ES6 才能写 Aurelia 应用。你还可以使用 AtScript、TypeScript、CoffeeScript...或者今天的浏览器所使用的语言: ES5。你只需按照所选语言的标准模式去创造类和其他所有东西就好了。不过我们认为 ES6 很优秀，希望你能优先考虑它。我们建议你阅读 [Luke Hoban's feature repo](https://github.com/lukehoban/es6features#readme) 来学习更多有关 ES6 的知识，包括模块导出和类等等。

好了，现在我们有了一个具有一些基本的数据和行为的 _view-model_ ，接下来我们看看它的伙伴—— _view_。

### app.html
```markup
<template>
  <section>
    <h2>${heading}</h2>

    <form role="form" submit.delegate="welcome()">
      <div class="form-group">
        <label for="fn">First Name</label>
        <input type="text" value.bind="firstName" class="form-control" id="fn" placeholder="first name">
      </div>
      <div class="form-group">
        <label for="ln">Last Name</label>
        <input type="text" value.bind="lastName" class="form-control" id="ln" placeholder="last name">
      </div>
      <div class="form-group">
        <label>Full Name</label>
        <p class="help-block">${fullName}</p>
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>
  </section>
</template>
```

所有的 _view_ 都包含在一个 `template` 标签里。上面这个 _view_ 是一个使用了 bootstrap 类的基本的输入表单。你看到 `value.bind="firstName"` 了吗？这把输入框的 _value_ 和 view-model 的 _firstName_ 属性绑定在一起。任何时候 view-model 的属性值发生了变化，输入框的值也变为这个新值；任何时候你更新了输入框的值，Aurelia 就会把新值放到 view-model 里。就是这么简单。

这个例子还有一些更有趣的东西。在最后一个 form-group 里，你可以看到这种语法：`${fullName}`。这叫做字符串替换，是一种从 view-model 到 view 的单向绑定，它会自动转化为字符串并替换到文档中。最后，我们看一下这个表单元素本身，你应该注意到这句了：`submit.delegate="welcome()"`。这是一个事件绑定，使用事件委托（event delegation）来绑定表单的 _submit_ 事件，当表单提交的时候，就会执行 _welcome_ 方法。

> **注：** 如果你没听说过事件委托，它是一种更有效的处理浏览器事件的技术，通过把单个事件句柄（event handler）绑定到文档级别来处理同一类型的所有事件，而不是把每个事件句柄单独绑定到每个节点上。

让我们运行 app 看看效果。在你的命令行界面中，执行下面的命令启动服务器。

```shell
gulp watch
```

现在你可以浏览 [http://localhost:9000/](http://localhost:9000/) 看你的应用了。在输入框里输入一些东西，注意 fullName 属性随着输入值的变化所作的更新。点击按钮查看你的哪些方法被调用了。

> **注：** Aurelia 有一个独一无二的、强大的数据绑定引擎，使用自适应技术来选择最佳的方式来监视（observe）每个属性的更新。例如，如果你使用了支持 [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) 的浏览器，那么 _firstName_ 和 _lastName_ 都会使用这种监视策略。否则，Aurelia 会生成 getter 和 setter 来把更动批处理到 Micro Task Queue，准确无误地模仿 Object.observe 的行为。因为计算属性 _fullName_ 无法通过上述两种方式来监视，我们使用 dirty checking 来监视它。我们会根据现实情况来使用最佳技术，你甚至可以使用插件来“教会” Aurelia 如何监视一些特定的模式，使用你自己的策略。我们认为这很酷。
> 
`.bind` 命令对所有的属性都默认使用单向绑定，除了表单控制元素，它们默认使用双向绑定。你随时可以使用特定的绑定命令  `.one-way`、`.two-way` 和 `.one-time` 来覆盖默认行为。类似地，你可以使用 `.delegate` 来做事件委托，同时你也可以使用 `.trigger` 把事件句柄直接绑定到目标元素上。

## 添加导航

我们似乎应该再添加一些页面并设置一个客户端的路由表，毕竟我们这是一个导航应用，你说呢？我们先把 _app.js_ 和 _app.html_ 分别重命名为 _welcome.js_ 和 _welcome.html_。这将会是我们的第一个页面。现在，让我们新建一个 _app.js_ 和 _app.html_ 作为我们的 “layout” 或者 “master page”。我们的 _view_ 将包含的导航 UI 以及当前页面的内容占位符， _view-model_ 将包含配置了我们的路由的 router 实例。为了让你明白如何设置路由表， 我们从 _view-model_ 开始：

### app.js

```javascript
import {Router} from 'aurelia-router';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['','welcome'], moduleId: 'welcome', nav: true, title:'Welcome' }
      ]);
    });
  }
}
```

好了，这下我们看到一些有趣的新东西了。我们想用路由表，所以我们在文件开头引入（import）了 _aurelia-router_。这又是 ES6 的强大之处。然后我们创建 _App_ 类为我们的应用保存数据和行为。看一下 constructor 函数，在 App 类创建的时候，constructor 函数在等待接收一个 _router_ 实例作为参数。这是怎么来的？

Aurelia 是按需创建 UI 组件来渲染页面的。这是通过使用一个能够为 constructor 提供依赖的[依赖注入](http://en.wikipedia.org/wiki/Dependency_injection)（Dependency Injection）容器实现的。依赖注入系统是怎么知道要提供什么的呢？只需要添加一个名为 _inject_ 的静态方法就行了，这个方法返回一个数组，包含所有要提供的类实例的 type。在上面的例子中，我们需要一个 router 实例，所以我们就把 `Router` 类加到这个数组里。

> **注：** 如果你恰好使用了 AtScript，我们很高兴告诉你 Aurelia 能够理解 AtScript 的类型注解（type annotations）并在依赖注入中使用它们。至于其他主流的采用类型或者注解元数据的 JavaScript 编译器，我们将会继续强化我们的平台使之能够理解你所选择的语言的格式。

我们需要把路由表设置为 App 类的一个共有属性，这个属性必须叫做 _router_。这非常重要，不要异想天开地把它命名为 _taco_ 或者之类的东西，明白吗？它是个 router……所以把它命名为 _router_ ，这样皆大欢喜。我们提醒过你 **必须** 把它命名为 _router_ 了， 对吧？

好了，是时候去配置路由表了。这很简单，你可以设置一个 title，生成文档标题的时候会用到它。然后你可以设置路由了，每个路由都有以下属性：

* `route`：这是当前路由的模式。当模式匹配成功的时候，路由表会导航到这个路由。你可以像上面那样使用静态路由，不过你也可以使用参数，像这样：`customer/:id`。我们同样支持通配符路由和查询字符串参数。`route` 属性可以是单个字符串模式，也可以是含有多个模式的数组。

* `moduleId`：这是相对于当前 view-model 的路径，指定你想为这个路由渲染的的 view/view-model 对。

* `title`：这个属性是可选的，当提供的时候，会被用于生成文档的标题。

* `nav`：如果你想为当前路由生成一个 UI，也就是说， _navigation model_ 里应当包含当前路由，那么就把这个属性设为 true（或者一个表明优先级的数字）。

### app.html

```markup
<template>
  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
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
遵循我们简单的应用组建约定，`App` 类将会绑定到 _app.html_ 这个 view 里。这段代码的很大一部分都在处理主要的导航结构。你已经看过基本的绑定和字符串替换了，因此我们将注意力集中在新知识上。看一下有着 navbar-nav 类的 `ul` 元素，它的 `li` 子元素表明了如何使用表达式 `repeat.for="row of router.navigation"` 来实现迭代器。这将会为 `router.navigation` 数组里的每一个项目创建一个 `li` 元素。_row_ 是一个本地变量，你可以看到 `li`  及其子元素都能使用它。

> **注：** 路由器的 `navigation` 属性是由路由的配置里所有标记为 `nav:true` 的路由所生成的一个数组。

注意 `li` 元素也表明了如何使用字符串替换来动态增删元素的类。继续往 view 下面看，那还有第二个 `ul` 元素。看到它唯一的 `li` 元素的绑定了吗？语句 `if.bind="router.isNavigating"` 根据表达式的值添加或删除这个 `li` 元素。路由器随时会更新它的  `isNavigating` 属性当它……“导航”的时候。非常方便。

最后我们来看一下 view 底部的 `router-view` 元素。只要 view-model 里有 `Router`，你就要在 view 里添加一个  `router-view` 。两者会自动互相联系，不管当前的 `Router` 是什么，都会在 `router-view` 里显示出来。

以上这些准备好之后，执行 `gulp watch` 启动开发服务器，打开浏览器看看。你现在应该能看到一个主导航了，只有一个选中的 tab，指向我们的 “welcome” 路径。_welcome_ 这个 view 会像之前那样在主内容区显示。打开浏览器的 debug 工具看看生成的 DOM，你会看到 _welcome_ 的内容在 `router-view` 里面。

> **注：** 如果你让 `gulp watch` 任务在此期间一直运行的话，那你可能已经注意到不管什么时候你对代码做了改动，浏览器都会自动刷新。这要归功于 `browser-sync`，在我们这个应用里，我们默认帮你设好了它。

## 添加第二个页面

呃，从技术上来说，我们现在已经有了一个导航应用了……但是它并不吸引人，因为只有一个页面。让我们来添加第二个页面。你能猜到怎么添加吗？你肯定能猜到……

我们来显示一些 Flickr 上的图片吧。首先我们为这个假想的页面添加路由：

### app.js (更新版)

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
        { route: 'flickr',        moduleId: 'flickr',       nav: true }
      ]);
    });
  }
}
```
如果你猜到了我们需要创建一个  _flickr.js_ 和 _flickr.html_ 文件，你猜对了。代码如下：

### flickr.js

```javascript
import {HttpClient} from 'aurelia-http-client';

var url = 'http://api.flickr.com/services/feeds/photos_public.gne?tags=rainier&tagmode=any&format=json';

export class Flickr{
  static inject() { return [HttpClient]; }
  constructor(http){
    this.heading = 'Flickr';
    this.images = [];
    this.http = http;
  }

  activate(){
    return this.http.jsonp(url).then(response => {
      this.images = response.content.items;
    });
  }

  canDeactivate(){
    return confirm('Are you sure you want to leave?');
  }
}
```

这里有很多很酷的东西，我们从第一行开始说起：我们从 Aurelia 引入（import）`HttpClient`，它让我们能够以非常简单的方式发送 HTTP 请求。`HttpClient` 没有包含在 Aurelia 的默认配置里，因此你需要安装这个包。在命令行界面执行下述命令即可：

```shell
jspm install aurelia-http-client
```

现在我希望你能认识到这种集成的包管理器和加载器的强大了。你用 jspm 安装一个包，然后你就可以在代码里通过相同的标识符引入这个包，多简单。你可以使用这种方式从 GitHub 或者 NPM 上安装任何东西。

跟之前一样，我们使用依赖注入把 `HttpClient` 实例提供给 view-model。不过，直到代码执行到 _activate_ 方法，我们才真正地使用了依赖注入。这有什么意义？我马上就讲到了……

只要当前路径发生了变化，Aurelia 的路由器就会强制 view-model 进行一次生命周期（lifecycle）。这叫做 "Screen Activation Lifecycle"。view-model 可以钩住生命周期的不同部分，控制流程进出路由。如果存在 `activate` 钩子，那么路由器就会在你的路由准备好的时候调用这个钩子。在上面的代码中，我们使用 `activate` 钩子调用 Flickr 的 api 获取一些图片。注意我们从 `activate` 方法返回了 http 请求的结果。`HttpClient` 的所有 API 都返回一个 `Promise`。路由器检测到 `Promise` 的时候就一直等到  `Promise` resolve 之后才完成导航。所以，通过这种方式，你可以强制路由器延迟显示页面，直到有数据生成才显示。

这里还有第二个生命周期钩子：`canDeactivate`。路由器在导航离开一个路由之前调用这个钩子。通过返回一个布尔值，`canDeactivate` 钩子给你一个机会决定是否允许导航继续下去。你也可以返回一个  `Promise`。完整的生命周期包括 `canActivate`、`activate`、`canDeactivate` 和 `deactivate` 钩子。

> **注：**  如果你不熟悉 [Promise](http://www.html5rocks.com/en/tutorials/es6/promises/)，它是 ES6 用来改善异步编程的新特性。一个 `Promise` 代表了一个将来的结果。本质上来讲，它相当于一个在未来某个时间点完成某项工作或者提供某些数据的“承诺”（promise）。
 
### flickr.html

```markup
<template>
    <section>
        <h2>${heading}</h2>
        <div class="row">
        <div class="col-sm-6 col-md-3" repeat.for="image of images">
          <a class="thumbnail">
            <img style="width: 260px; height: 180px;" src.bind="image.media.m"/>
          </a>
        </div>
        </div>
    </section>
</template>
```

这个页面的 view 相当直观。所有的知识点你之前都了解过，包括对大家非常忌讳的行内样式（inline style）的“精彩”使用。（请无视这点……没什么好看的。）

等这一切都准备好的时候，你就可以重新启动服务器了。现在你应该能看到导航栏有两个项目，而且能够来回切换。哦耶！

让我们总结一下。为你的应用添加一个页面，只需要：

1. 在 _app.js_ 里添加路由配置。
2. 添加一个 view-model。
3. 添加一个（与 _app.js_）同名的 view（但是需要以 .html 作为文件后缀）。
4. 庆祝。

## 彩蛋：创建一个自定义元素

看看你，真是超出了我的预期！我看你今天很有兴趣学习一些额外的好东西呢。这样的话，我们来创建一些 HTMl 元素吧。我觉得我们的导航栏很适合拿来用。_app.html_ 文件里有太多 HTML 了，为什么不提取出一个自定义的 `<nav-bar>` 元素让我们的代码更加语义化呢？这是我们最终想要的结果：

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
这段代码从 "./nav-bar" 引入一个 `nav-bar` 元素，一旦元素在 view 里可用，我们就能像其他所有元素一样使用它了，包括针对它的自定义属性（比如 _router_）进行数据绑定。那么，我们怎么得到这个最终产品？

你说怎么着？我们简单的 view-model/view 约定对自定义元素依然有效。（实际上你一直都在创建我们有时称为“匿名”自定义元素的东西……只不过你没意识到而已。）让我们来创建 _nav-bar.js_ 和 _nav-bar.html_。先看 view-model 的代码：

### nav-bar.js

```javascript
import {Behavior} from 'aurelia-framework';

export class NavBar {
  static metadata(){ return Behavior.withProperty('router'); }
}
```

要创建一个自定义元素，创建和导出一个类即可。由于这个类要在 HTML 中作为元素使用，因此我们需要告诉框架，类的哪些属性应该作为元素的属性出现。我们使用 _metadata_ 达到此目的。跟 _inject_ 类似，_metadata_ 也是一种为 Aurelia 框架提供有关类的信息的方式。Aurelia 通常很智能，可以推断很多事情，不过当它无法推断，或者你想做一些不同于惯例的事情时，你就要使用某种形式的 _metadata_。要利用这个功能，你需要为类添加一个静态  `metadata` 方法，并返回一个包含 metadata 实例（其实就是普通的对象）的数组。在本例中我们使用了 Aurelia 的 `Behavior` metadata。`withProperty` 方法添加一个 `BehaviorProperty` 告诉框架我们想让类的 `router` 属性成为 HTML 元素的属性。一旦 `router` 成为元素的一个属性，我们就能在 view 里对它进行数据绑定。

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
这看起来跟我们原先 _app.html_ 中导航栏部分的 HTML 没什么区别。我们仅仅是把那段代码提取出来放到这个模版里。不过现在它跟 _nav-bar.js_ 绑定在一起了，而不是 _app.js_。

这是一个没有实际行为的非常简单的自定义元素，但它就如上面显示的一样，是完整且可用的。我们这里重复这段代码是为了方便你学习。

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

总结：首先我们有一个 `import` 元素。Aurelia 用它加载自定义的元素，这是通过它的 `from` 属性指向的相对资源（relative source）实现的。它遵循我们的约定，所以它知道如何加载  _nav-bar.js_ 和 _nav-bar.html_ 文件。所有通过这种方式导入到一个 view 的东西都只在这个 view 中可见。这样做的结果是，你不必担心命名冲突。第二个要点是，绑定到 `App` 的 router 上的元素的实际用处。我们把 `App` 类上的 router 实例导向（pipe） `NavBar` 元素的同名属性上，用于元素内部的渲染。漂亮！

> **注：** 你也可以图方便加载整个应用范围的元素和其他一些行为，这样你就不必在每个 view 里都引入公共资源。

你可能会好奇 Aurelia 是如何判断自定义元素的名称的。按照约定，它使用类的名称，只不过变为连接式的小写字母。不过你随时可以使用精确的写法：在 `Behavior` 上再链接一个 metadata  `.customElement('nav-bar')` 。如果你的自定义元素没有 view 模版怎么办？没问题，链接一个 `.noView()`。想让你的自定义元素使用 ShadowDOM？像专业人士那样再链接一个 `.useShadowDOM()`。不要担心浏览器支不支持。我们有一个高效而且精确的 ShadowDOM 回退实现（fallback implementation）。

除了能创建自定义元素，你还可以在已有的元素上创建独立的属性为之增添一些新的行为。我们把这叫做 _Attached Behaviors_。有时你还可能需要创建 _Template Controllers_ 来动态增删 view 里的 DOM，比如上面我们使用的 `repeat` 和 `for`。你能做的不并止这些。Aurelia 的模版引擎（templating engine）在功能和拓展性上都很强大。

> **注：** AtScript 用户可以利用这门语言的类型注解。那些支持静态类属性的语言，比如 TypeScript 的用户，能够很方便地把 `metadata` 当作属性而不是方法来使用。这对依赖注入容器所使用的 `inject` 名称同样有效。

## 彩蛋：使用子路由

满足不了你是吧？那行，我好好招待你一下。我们为这个应用添加第三个页面……用它自己的路由！我们把这称为子路由（child router）。所谓子路由，就是有着自己的路由配置，并相对于父路由进行导航。准备好精神错乱吧……

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

什么？！和 `App` 几乎一模一样的配置？这到底是什么？为什么会这样？呃……你可能永远也不该在实际生活中这么做……但这真的很酷。这，我的朋友，是一个递归路由（recursive router），我们这么做是因为我们能够这么做。

为了完整起见，这是我们的 view：

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

Aurelia 异常关注于开发者体验，因此你不但能够用它来创建精彩的应用，同时也能享受开发的过程。我们对 Aurelia 所做的设计是让你只记住一些简单的约定就行了，你不需要仅仅为了跑起来一个死板或者限制重重的框架而在大量的配置或者样板代码（boilerplate code）上浪费时间。使用 Aurelia 你永远也不会撞到路障，经过仔细的设计，它是完全可插（pluggable）和可定制化（customizable）的。

感谢你花时间读完了我们的指南。我们希望你能探索我们的文档然后做个令人惊艳的东西。我们期待你的作品。