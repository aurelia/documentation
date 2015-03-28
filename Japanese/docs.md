# ドキュメント

私たちは、Aureliaプロジェクトに豊富なドキュメントを作成する計画があります。残念なことに、まだそれは終わっていません。ともかく、この初期のプレビュー期間の間は、我々はこのドキュメントにあなたが実行したいと思う最も一般的なタスクの例を含めて提供します。もし質問があれば、我々の[gitter channel](https://gitter.im/aurelia/discuss)に参加してください。

> **注:** このガイドの翻訳を探していますか?我々の[ドキュメントリポジトリ](https://github.com/aurelia/documentation)を見てください。

<h2 id="browser-support"><a href="#browser-support">対応ブラウザ</a></h2>

Aureliaは、常時更新のあるブラウザ向けに設計されています。それには、Chrome、Firefox、IE11そしてSafari 8が含まれます。設定なしにはIE11未満のどのブラウザでも動作しません。

あなたがIE11未満でAureliaを動作させる必要がある場合は、 [AureliaスケルトンのNav IE Polyfillテスト](https://github.com/devmondo/skeleton-navigation-IE-Polyfill-Test )があり、ES6Shimを使うことでそれが実現できることを示しています。この実験プロジェクトは、コミュニティから提供されたものですが、目立った問題もなくIE10とIE9で動作するフレームワークとなっているように見えます。将来的には、この問題を徹底的に調査して、公式の解決策を考え出すことができればと思っています。是非それを試してみて、古いブラウザをサポートできるよう応援していただきたいと思います。

<h2 id="startup-and-configuration"><a href="#startup-and-configuration">起動と設定</a></h2>

ほとんどのプラットフォームでは、"main"やコード実行の開始点があります。Aureliaでも同じです。あなたがすでに [Get Started](/get-started.html) を読んでいれば、 `aurelia-app` 属性について知っていることでしょう。単にHTML要素にこの属性を記載すれば、Aureliaのブートストラッパーは、 _app.js_ と _app.html_ をロードし、データバインドを行い、その属性があるDOM要素にそれを注入します。もしこの規約を使用したくない場合は、単純にロードするビューモデルを示す属性に値をセットすれば良いです。例えば、 `<body aurelia-app="todo">` は _todo.js_ と _todo.html_ をappの代わりにロードします。

`aurelia-app` 属性は、とりあえず始めるためには便利ですが、多くの場合、あなたはユーザーに何かを表示する前に、フレームワークを設定したり、なんらかののコードを実行したいと望むことでしょう。そのためには、あなたのプロジェクトが進むにつれ、`aurelia-main` を使うようにしていくことです。

>**注:** もしあなたがAtScriptを使っているなら、`atscript`属性をあなたのアプリケーションのDOM要素に追加してください。もしES6ではなくES5を使っているのであれば、`es5`属性を追加してください。こうすることで、それらの言語を便利に使えるような機能を"有効にする"ことができます。

**違いは何か?**

`aurelia-app` はAureliaアプリケーションのインスタンスを作成し、フレームワークのデフォルトのオプションセットで事前設定を行った上で、アプリケーションのビューモデルをロードします。`aurelia-main` はデフォルトでカスタム設定のモジュールである _main.js_ をロードし、`configure` 関数にAureliaオブジェクトを渡して起動し、フレームワークの設定を可能とします。これにより自分で何をいつUI上のどこに表示するかを自分で設定することが可能になります。 _main.js_ ファイルの例です:

```javascript
import {LogManager} from 'aurelia-framework';
import {ConsoleAppender} from 'aurelia-logging-console';

LogManager.addAppender(new ConsoleAppender());
LogManager.setLevel(LogManager.levels.debug);

export function configure(aurelia) {
  aurelia.use
    .defaultBindingLanguage()
    .defaultResources()
    .router()
    .eventAggregator()
    .plugin('./path/to/plugin');

  aurelia.start().then(a => a.setRoot('app', document.body));
}
```

カスタムプラグインを除けば、このコードは通常 `aurelia-app` が行う処理と本質的には一緒です。あなたが `aurelia-main` に切り替える際、これらの処理は自分自身で設定する必要があります。しかし、カスタムプラグインをインストールしたり、ビューテンプレートで共通に使われるサービスのDIを設定することも、同時にできるようになるのです。

>**注:** AtScriptを有効にする場合は、手動で設定する必要があります。 `aurelia.use.atscript()` を呼び出してください。同様に、ES5を有効にする場合は、 `aurelia.use.es5()` を呼び出してください。

<h3 id="logging"><a href="#logging">ロギング</a></h3>

Aureliaはフレームワーク自身の用途として、シンプルなログ抽象化を持っています。デフォルトではこれは何もしません。上記の設定例では、アペンダーをインストールして、ログをコンソールに出力する方法を示しています。ログレベルの設定方法も同様に理解できるでしょう。ログレベルのオプションは: `none` , `error` , `warn` , `info` そして `debug` があります。

自分でアペンダーを作成することも容易にできます。アペンダーのインターフェースに合致するようなクラスを作成するだけです。どうすればよいかを知るためには、我々の [コンソールログアペンダーのソースコード](https://github.com/aurelia/logging-console/blob/master/src/index.js) を見てください。

<h3 id="plugins"><a href="#plugins">プラグイン</a></h3>

_plugin_ はexportされた `install` 関数をもつモジュールに過ぎません。起動時にAureliaはプラグインモジュールをロードして、Aureliaインスタンスを引数としてそれらの `install` 関数を呼び出します。それによりプラグインはフレームワークを適切に設定することができます。プラグインは非同期の設定タスクを実施できるように、 `install` 関数の返値として `Promise` を返すこともできますプラグインを書く際には、以下の手順に従ってください:

1. 平坦なディレクトリ構造とすること。ビヘイビアやビューをサブディレクトリに配置してはいけません。
2. ファイル名とビヘイビア名は一致すること。
3. カスタムエレメントのビューストラテジーを含め、すべてのメタデータを明示的に提供すること。

> **注:** #2と#3に関連して: プラグイン中では名前付け規約に頼ってはいけません。あなたには、あなたのプラグインの利用者がAureliaの規約をどのように変更するかは知るすべがありません。サードパーティのプラグインは様々なコンテキストにおいて、正しく動作することを保証するため、明示的に指定しておかなければなりません。

<h4 id="promises"><a href="#promises">Promise</a></h4>

デフォルトでは、AureliaはES6ネイティブ実装のPromise、もしくはポリフィルのPromiseを使います。一方、この挙動を置き換えて、素晴らしい [Bluebird](https://github.com/petkaantonov/bluebird) Promiseライブラリを使うようにすることもできます。ページで他のスクリプトを読み込む前にこのライブラリを読み込むだけです。このライブラリは独自の標準準拠のPromise実装を持ち、現在のところ言語ネイティブの実装より高速で、デバッグサポートも良いものです。それにくわえて、Babelトランスパイラと組み合わせて使う際には、 [コルーチン](https://babeljs.io/docs/usage/transformers/#bluebird-coroutines) を非同期処理の改善に利用できます。

<h3 id="the-aurelia-object"><a href="#the-aurelia-object">Aureliaオブジェクト</a></h3>

カスタム _main_ モジュールやプラグインの両方がAureliaオブジェクトとの相互作用することによって処理を行うため、以下のコードで、そのAPIを簡単に説明します。

```javascript
export class Aurelia {
  loader:Loader; //モジュールローダー
  container:Container; //アプリケーションレベルのDIコンテナ
  use:Plugins; //プラグインAPI

  withInstance(type, instance):Aurelia; //DI ヘルパーメソッド(コンテナに渡される)
  withSingleton(type, implementation):Aurelia; //DI ヘルパーメソッド(コンテナに渡される)
  withResources(resources):Aurelia; //リソースヘルパーメソッド

  start():Promise; //フレームワークの開始、プラグインのインストールとリソースのロード
  setRoot(root, applicationHost):Promise; //"root" または "app" ビューモデルをセットし、表示する 
}
```

<h2 id="views-and-view-models"><a href="#views-and-view-models">ビューとビューモデル</a></h2>

Aureliaでは、ユーザーインターフェースコンポーネントは _ビュー_ と _ビューモデル_ の組で構成されます。 _ビュー_ はHTMLで書かれDOMにレンダリングされます。 _ビューモデル_ はJavaScriptで書かれて _ビュー_ にデータや振る舞いを提供します。テンプレートエンジンと(もしくは)DIがビューとビューモデルの組を生成する責務を持ち、プロセスで予測可能なライフサイクルを実施します。インスタンス化された後、Aureliaの強力な _データバインディング_ が二つを結びつけ、データの変更をビューに通知したり、その逆を行ったりします。

<h3 id="dependency-injection"><a href="#dependency-injection">依存性注入 (DI)</a></h3>

ビューモデルとテンプレートコントローラ、付与ビヘイビアなどの他のインターフェース要素は、DIコンテナを使用して、フレームワークによってインスタンス化されるクラスとして作成されます。このスタイルで書かれたコードはモジュール化やテストが容易です。大きなクラスを作成するより、協力して目的を達成する小さいオブジェクトに分割するようにできます。DIが実行時にそれらのオブジェクトを統合してくれます。

DIを活用するためにやることは、あなたのクラスに若干のメタデータを追加して、フレームワークにそれをコンストラクタに渡すことを知らせるだけです。これはAureliaのHttpClientを使うビューモデルの例です。

```javascript
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }
}
```

`inject` という名前のスタティックメソッドを追加し、注入したいものの配列を返すだけです。

> **注:** TypeScriptやCoffeeScriptで書くときは、メソッドの代わりにスタティック配列プロパティを使うことができます。ES5の場合、コンストラクタ自身にプロパティを追加することができます。同じことはES6でもできますが、我々はスタティックメソッドオプションを有効にしました。それは通常のJSコードで、コンストラクタの近くに書くことができるためです。もしあなたがAtScriptを使っているなら、コンストラクタを次のように定義することで、型アノテーションの恩恵を受けることができます。:  `constructor(http:HttpClient)` 。 (これを実行する前に、アプリケーションをホストしているエレメントに `atscript` 属性をつけるか、 `aurelia.use.atscript()` を手動で呼び出すかする必要があります。)

injectの配列に入れるものはコンストラクタに必要な型だけではありません。 `リゾルバ` インスタンスでも良いです。例として、下記を見てください:

```javascript
import {Lazy} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static inject() { return [Lazy.of(HttpClient)]; }
    constructor(getHTTP){
        this.getHTTP = getHTTP;
    }
}
```

`Lazy` リゾルバは実際には `HttpClient` のインスタンスを提供しません。その代わり、呼び出された時にHttpClientのインスタンスを返す、関数を提供しています。すぐに使えるいくつかのリゾルバもありますし、 `Resolver` クラスを継承して独自のリゾルバを作成することも可能です。Aureliaが提供しているリゾルバのリストです:

* `Lazy` - 依存対象を遅延評価する関数を注入します。
    * 例. `Lazy.of(HttpClient)`
* `All` - 指定されたキーで登録されているサービスすべての配列を注入する。
    * 例. `All.of(Plugin)`
* `Optional` - すでにコンテナ中にクラスのインスタンスがある場合に、そのインスタンスを注入する。なければnull。
    * 例. `Optional.of(LoggedInUser)`
* `Parent` - 現在のDIコンテナをバイパスして、親のコンテナにあるインスタンスを注入する。
    * 例. `Parent.of(Router)`

これらのリゾルバに加え、 `レジストレーション` アノテーションを使って、デフォルトの(コンテナへの)登録や、インスタンスの生存期間を指定することができます。デフォルトでは、DIコンテナはすべてがシングルトンインスタンスであることを想定しています。コンテナにつき、一つのインスタンスです。けれども、レジストレーションアノテーションを使って、この動作を変えることもできます。下記に例を示します:

```javascript
import {Metadata} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static metadata(){ return Metadata.transient(); }
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }
}
```

この例では、DIコンテナが `CustomerDetail` のインスタンスを要求されるたびに、コンテナはシングルトンではなく、新しいインスタンスを生成して返します。 `Singleton` や `Transient` はフレームワークが提供する、すぐに使えるレジストレーションです。 `Registration` を継承して独自のレジストレーションを作成することも可能です。

> **注:** 最後の例ではフレームワークにコンテキスト情報を提供するために、 _metadata_ を使っています。メタデータについては、ビヘイビアについてお話するときに再度目にすることになるでしょう。

<h2 id="templating"><a href="#templating">テンプレート</a></h2>

Aureliaのテンプレートエンジンは、あなたのビューやそれがインポートするリソースをロードして、HTMLが最適になるようにコンパイルを行い、画面にUIをレンダリングします。ビューを作成するには、 `HTMLテンプレート` の中にHTMLを書くだけで良いです。単純なビューの例です:

```markup
<template>
    <div>Hello World!</div>
</template>
```

`template` タグの内側にあるものはすべてAureliaによって管理されます。しかし、AureliaはビューのロードにHTMLインポート技術を使っているので、リンクを含めることもできますし、相対パスでリソースを指定しても正しくロードすることができます。言い換えれば、次のようなことができるということです:

```markup
<link rel="stylesheet" type="text/css" href="./hello.css">

<template>
    <div class="hello">Hello World!</div>
</template>
```

これによってビューごとのスタイルシートや、Webコンポーネントを動的に、その場でロードすることを可能となります。

Aureliaに固有のリソース、Aureliaの _カスタムエレメント_ や、 _付与ビヘイビア_ 、 _テンプレートコントローラー_ 、 _バリューコンバーター_ などをインポートするときは、リンクではなく、ビューの中で `import` 要素を使う必要があります。下記に例を示します:

```markup
<template>
  <import from='./nav-bar'></import>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

この例では、 `nav-bar` が読み込もうとしているAureliaの _カスタムエレメント_ です。Aureliaの `import` エレメントを使うことで、インポート対象をフレームワークのリソースパイプラインに処理させることになりますが、これには次のような利点があります:

* 冗長でない - リソースはアプリケーションで1回ダウンロードされるだけで済みます。他のビューで同じ要素をインポートしていても、再度ダウンロードされることはありません。
* ワンタイムコンパイル - この方法でインポートされたカスタムエレメントのテンプレートはアプリケーションを通じて一回のみコンパイルされます。
* 局所スコープ - インポートされたリソースはインポートしたビューの中でのみ可視となり、名前衝突の可能性を減らします。
* リネーム - 同じビューで二つのサードパーティ製リソースを同時に使うときに、名前を変更することが可能です。
    - 例. `<import from="./nav-bar" as="foo-bar"></import>` - `nav-bar` 要素の代わりに、 `foo-bar` として使うことができます。(これはES6にもとづいています。ES6では、リネームはエイリアスの代替として考えられており、それはエイリアスが厳密に型の名前を変えてしまうからです。)
* パッケージ - インポートは、一つのビューにインポートされることになる複数リソースからなるモジュールを指すこともできます。
* 拡張性 - あなたは新しいタイプのリソースを定義することも可能です。このように読み込まれた時に、独自のローディング(非同期に一度だけ)や、登録(ビューごとに)を行うようにできます。
* ES6 - コードはHTMLインポートではなく、ES6ローダーによりロードされ、ローダーの機能、拡張性のすべてを利用できます。

ビューではデータバインディングとともに、上記のような様々な種類のリソースを活用することになるでしょう。

>**注:** あなたはおそらく、それぞれのビューでimportを書かなければいけないなんてうんざりだと思っていることでしょう。ブートストラップ時に、すべてのビューでグローバルなリソースを使えるように、Aureliaを設定できることを思い出してください。

<h3 id="databinding"><a href="#databinding">データバインディング</a></h3>

データバインディングは、JavaScriptオブジェクトの状態と振る舞いをHTMLビューと結びつけます。一旦ビューとの結びつきができると、結びつけたプロパティは双方向に同期します。JavaScriptオブジェクトの変更はビューに反映されますし、ビューの変更はJavaScriptオブジェクトに通知されます。この結びつきを確立するためには、HTML中で"バインディングコマンド"を使います。バインディングコマンドはその "." をバインディング演算子として使ううため、識別が容易です。HTML属性が"."を含む場合は常に、コンパイラが属性名と値をバインディング言語としてインタプリタに渡します。その結果として、ビューが作成されたときに結びつきを確立させる一つ以上のバインディング式ができます。

あなたは、独自のバインディングコマンドを作成して、システムを拡張することができます。しかし、Aureliaはほとんどの典型的なユースケースをカバーするバインディングコマンドのコレクションを提供しています。

<h4 id="binding-modes"><a href="#binding-modes">バインド、一方向、双方向、一回のみ</a></h4>

最もありふれたバインディングコマンドは `.bind` です。これはフォーム要素の値に対して双方向のバインディングを行い、それ以外のすべての属性に対して一方向のバインディングを行うものです。

_これは何を意味するのでしょうか?_

一方向バインディングとは、あなたのJavaScriptビューモデルの変更をビューに反映させますが、その逆、ビューからビューモデルへの反映は行わないことを意味しています。双方向バインディングとは両方起点の変更が他方に反映されることを意味しています。 `.bind` は、もしあなたがフォーム要素の値にバインドするような場合は、フォームの変更をビューモデルに反映させたいと思っているだろうという、実用的な仮定を用いています。それ以外の場合では、一方向バインディングを使います。特に、ほとんどの場合は、フォーム要素以外に双方向バインディングを行うことは道理にかなわないからです。これは `.bind` を使ったバインディングの小さい例です:

```markup
<input type="text" value.bind="firstName">
<a href.bind="url">Aurelia</a>
```

上記の例で、 `input` はその `value` をビューモデルの `firstName` プロパティにバインドします。 `firstName` プロパティの変更は `input.value` を更新し、 `input.value` の変更は `firstName` プロパティを更新します。他方、 `a` タグはその `href` を、ビューモデルの `url` プロパティとバインドします。 `url` プロパティの変更のみが `a` タグの `href` に伝達され、その逆は行いません。

しかし、あなたはいつでもこれを明示するために、 `.one-way` や `.two-way` を `.bind` の代わりに使うことができます。これが必要となる典型的なケースは、入力コントロールのように動作するWebコンポーネントとともに使う場合です。次のようなことを想像してください:

```markup
<markdown-editor value.two-way="markdown"></markdown-editor>
```

パフォーマンスを最適化して、CPUやメモリの消費を最小とするために、ビューモデルからビューへ `1回だけ` データを反映させる、 `one-time` バインディングコマンドを活用することができます。これは初回のバインディング時に行われ、それ以降の同期はありません。

<h4 id="event-modes"><a href="#event-modes">移譲、トリガー&呼び出し</a></h4>

バインディングコマンドはプロパティと属性を結びつけるだけではありません。トリガーのような振る舞いをさせることにも使えます。例えば、ボタンをおした時にビューモデルのメソッドを起動したいような場合、 `trigger` コマンドを次のように使います:

```markup
<button click.trigger="sayHello()">Say Hello</button>
```

ボタンが押されると、ビューモデルの `sayHello` メソッドが起動されます。このように個々の要素にイベントハンドラを追加するのは効率的でないと言われているので、多くの場合、イベントデリゲーションを使いたいと思うでしょう。そのためには、 `.delegate` コマンドを使います。これは同じ例ですが、代わりにイベントデリゲーションを使ったものです:

```markup
<button click.delegate="sayHello()">Say Hello</button>
```

> **注:** イベントデリゲーションについて詳しくない方のために。これはDOMのイベントが親に向かって湧き上がるように伝播する性質を用いたテクニックです。 `.delegate` を一つのイベントハンドラに対して使うとき、それは個々の要素ではなく、ドキュメントに対し接続されます。その要素のイベントが発火すると、それはDOMツリーを泡のように上にたどっていき、最終的にそれが処理されるドキュメントに到達します。これによってイベント処理時のメモリ効率が良くなります。そして、この方式をあなたの標準の処理方式とすることが推奨されます。

これら全てはDOMイベントに対して、様々な方法で動作を行うものでした。まれに、後で手動で呼び出せるように、直接関数を参照するような、カスタムのAurelia挙動を使うことがあります。関数リファレンスを渡すためには、 `.call` バインディングを使います。(それをあとで _呼び出す_ からです):

```markup
<div touch.call="sayHello()">Say Hello</button>
```

これで、この接続を行った振る舞い(touch)は、 `sayHello()` を実行する関数を手に入れました。

もしイベントオブジェクトが必要な場合、 `$event` プロパティを引数として移譲された関数に渡すことができます。

```markup
<button click.delegate="sayHello($event)">Say Hello</button>
```

<h4 id="string-interpolation"><a href="#string-interpolation">文字列補完</a></h4>

プロパティを直接ドキュメントの中身としたり、属性の値として綴じ込む必要が出てくることもあるでしょう。このために、文字列補完を使うことができ、その書式は `${expression}` です。文字列補完は片方向のバインディングで、その出力は文字列に変換されます。下記に例を示します:

```markup
<span>${fullName}</span>
```

`fullName` プロパティは直接spanの中身として挿入されます。これを下記のように、cssクラスのバインディングに使うこともできます:

```markup
<div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
```

上記のスニペットで、"dot" は常に指定されるクラスで、"green"は `isHappy` が真だったときのみ指定され、偽のときは"red"が指定されます。それに加え、 `color` の値が、どんな値であれ...クラスに追加されます。

> **注:** バインディング注で単純な式を使うことができます。あまり凝ったことをしようとしないようにしてください。ビューにコードを含めたいと思わないでしょう? ビューとビューモデルの間の接続を確立することが、あなたが望むことですから。

<h4 id="ref"><a href="#ref">ref</a></h4>

コマンドと文字列補完に加え、バインディング言語は特別な属性: `ref` を解釈します。 `ref` を使うことで、エレメントにローカルの別名をつけて、他のバインディング式で利用することができます。ビューモデルのプロパティとしても使えるようになるため、コード中すべてからアクセス可能になります。 `ref` を使った例を示します:

```markup
<input type="text" ref="name"> ${name.value}
```

特別な `.view-model` バインディングを `ref` と組み合わせて使うことで、Aureliaカスタムエレメントの後ろのビューモデルのインスタンスを取得することができます。このテクニックを使うことで、異なるコンポーネントを相互に接続することができます。次の例をみてください:

```markup
<i-produce-a-value ref.view-model="producer"></i-produce-a-value>
<i-consume-a-value input.bind="producer.output"></i-consume-a-value>
```

<h3 id="behaviors"><a href="#behaviors">ビヘイビア</a></h3>

データバインディングに加え、ビューの中でAureliaビヘイビアの能力を使うことができます。すぐ使えるビヘイビアとして、次の三つがあります。:

* カスタムエレメント - HTMLを新しいタグで拡張します。あなたのカスタムエレメントは独自のビューを持つことができます (その中ではデータバインディングや、他のカスタムエレメントも使えます)。また、 [ShadowDOM](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/) も活用できます。(それがサポートしていないブラウザであってもです)
* 付加ビヘイビア - 新しい属性を既存のエレメント、もしくはカスタムエレメントに追加して、HTMLを拡張できます。このような属性はエレメントにビヘイビアを"付与"します。
* テンプレートコントローラー - テンプレートをレンダリングする新しい機構を作ります。テンプレートコントローラーはUIを動的に作成して、DOMに挿入できるものです。

もちろん、これらの全てはデータバインディングと一体となって動作します。Aureliaが提供するビヘイビアを見てみましょう。すべてのビューで使えます。

<h4 id="show"><a href="#show">show</a></h4>

`show` 付加ビヘイビアは、条件によってHTMLを表示することができます。showの値が `true` の場合、エレメントを表示し、そうでなければ非表示とします。このビヘイビアはDOMを追加/削除するものではありません。単に表示を切り替えるだけです。下記に例を示します:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

`isSaving` プロパティがtrueのとき、 `div` は表示されますが、そうでなければ非表示になります。

<h4 id="if"><a href="#if">if</a></h4>

`if` テンプレートコントローラーは条件によってHTMLエレメントを追加/削除することができます。値がtrueの時、エレメントはDOM中に現れますが、そうでなければ、DOMにも存在しません。

```markup
<div if.bind="isSaving" class="spinner"></div>
```

この例は上で出てきた `show` に似ていますね。違いは、バインディング式の値がfalseだったときに、 `div` は非表示になるだけではなく、DOMからも削除されてしまうところです。

複数のエレメントを条件によって追加/削除したいが、親要素がなく `if` ビヘイビアを配置できないような場合は、複数のエレメントをtemplateタグで囲んで、それに `if` ビヘイビアを付与します。下記のようになります:

```markup
<template if.bind="hasErrors">
    <i class="icon error"></i>
    ${errorMessage}
</template>
```

<h4 id="repeat"><a href="#repeat">repeat</a></h4>

`repeat` テンプレートコントローラーは、配列内の要素ごとに、テンプレートを複数回表示することができます。 顧客名のリストを表示するサンプルです:

```markup
<ul>
    <li repeat.for="customer of customers">${customer.fullName}</li>
</ul>
```

注意しなければいけない点として、repeatビヘイビアは `for` バインディングコマンドと一緒に使う必要があります。このバインディングコマンドは、”item of array" の特殊な表記を解釈します。ここで、"item" はテンプレート中で利用するローカル変数名で、"array" は配列を返すバインディング式です。

> **注:**: `if` ビヘイビア同様、 `template` タグを使って、親要素のない複数のエレメントをまとめることができます。実際、これはすべてのテンプレートコントローラーで利用できます。テンプレートコントローラーを要素に付与すると、コンパイル時にHTMLテンプレートに変換されます。そのため、必要があれば、templateを明示的に指定することもできるのです。

<h4 id="compose"><a href="#compose">compose</a></h4>

`compose` カスタムエレメントは動的にUIをDOMにレンダリングすることができます。配列に異なる種類の要素が入っていることを想像してください。要素は要素の型を返すtypeプロパティを持つものとします。その場合、このように書くことができます:

```markup
<template repeat.for="item of items">
    <compose
      model.bind="item"
      view-model="widgets/${item.type}">
    </compose>
</template>
```

これで、要素の _type_ に応じて、 `compose` 要素は異なるビューモデル(とビュー)をロードし、DOMにレンダリングします。もしビューモデルに `activate` メソッドがあれば、 `compose` エレメントはそれを呼び出し、 `model` を引数として渡します。 `activate` メソッドは `Promise` を返して、実際にデータバインドやDOMへのレンダリングが始まる前に、なんらかの非同期処理を走らせてからcompose処理を行うようにすることができます。 

もしあなたが標準のビュー/ビューモデルの規約とは異なるビューを使いたい場合、 `compose` 要素に `view-model` と同じように、 `view` 属性を指定することもできます。

データに応じてビューを切り替えたい場合はどうすればよいでしょうか?または、実行時に判断してビューを切り替えたい場合は?そのような場合は、ビューモデルに `getViewStrategy()` メソッドを実装します。そこでビューへの相対パスか、独自のビューのロード時処理を実装した、 `ViewStrategy` を返します。素晴らしい点は、このメソッドは `activate` の後に実行されるので、ビューを決定する際にモデルのデータを参照することができることです。

<h4 id="selected-item"><a href="#selected-item">selected-item</a></h4>

HTMLのSelectエレメントは面白いヤツです。通常は、select要素と、値をバインドしたoption要素の `repeat` を下記のように組み合わせます:

```markup
<select value.bind="favoriteNumber">
    <option>Select A Number</option>
    <option repeat.for="number of numbers" value.bind="number">${number}</option>
</select>
```

また、プリミティブ値ではなく、オブジェクトインスタンスを選択したいということがあるかもしれません。そのために、 `selected-item`  付加ビヘイビアを使うことができます。下記は架空の従業員リストに対して使った例です:

```markup
<select selected-item.bind="employeeOfTheMonth">
  <option>Select An Employee</option>
  <option repeat.for="employee of employees" value.bind="employee.id" model.bind="employee">${employee.fullName}</option>
</select>
```

最初に、 `selected-item` に対して `.bind` コマンドをを指定しています。その後、repeatを同じように使い、 `value` にプリミティブをバインドします。ここで、もう一つのプロパティ `model` を追加しています。こうすることで、 `selected-item` ビヘイビアが選択に関連するオブジェクトインスタンスを使うようになります。言い換えると、オプションが選択されたとき、 `employeeOfTheMonth` プロパティに、 `model` プロパティの値がセットサれるということです。ビューモデルで `employeeOfTheMonth` プロパティがセットされると、ビューでは対応する `model` に結びついているオプションが選択されます。 

> **注:** 以前フォームエレメントのみ双方向バインドすると説明しました。しかし、このケースで、カスタム属性 `selected-item` はデフォルトで双方向バインドします。どうやっているのでしょうか?Aureliaのビヘイビアを定義する際には、デフォルトのバインディングモードを指定することも可能だというだけです。

<h4 id="global-behavior"><a href="#global-behavior">global-behavior</a></h4>

これは直接利用できる付加ビヘイビアではありません。これは、カスタムバインディングコマンドと組み合わせて、jQuery APIやそれに類するものの宣言的な利用を動的に有効にします。このアイディアを理解するために、例を見てみましょう:

```markup
<div jquery.modal="show: true; keyboard.bind: allowKeyboard">...</div>
```

このサンプルは [Bootstrap modal widget](http://getbootstrap.com/javascript/#modals) をベースにしています。この例は、 `modal` jQueryヴィジェットがdiv要素に付与され、その `show` オプションを `true` にセットし、 `keyboard` オプションをビューモデルの `allowKeyboard` プロパティの値にセットします。このビューとの結びつきが解除されると、jQueryヴィジェットは破棄されます。

`global-behavior` 付加ビヘイビアとカスタムシンタックスを組み合わせ、このような動的な挙動を可能にしています。この例のように、プロパティと値を上記のように区切るシンタックスは `style` 属性のシンタックスをベースにしたものです。バインディングコマンドも利用できることに注目してください。 `.bind` を使ってビューモデルのデータを直接プラグインに渡したり、 `.call` を使ってコールバック関数をプラグインに渡すこともできます。

どのように動作するかというと:

バインディングシステムが認識できないバインディングコマンドを見つけると、それを動的に解釈しようとします。属性名がグローバルバインディングハンドラに渡され、そこでバインディングコマンドが解釈されます。ハンドラはその値をプラグインに渡す、プラグインのオプションのオブジェクトを作成するために利用できます。ビューのバインドが解除されたときは、ハンドラはその後にクリーンナップ処理を実行できます。この例では、jQueryハンドラはプラグインを初期化するパターンと、 `destroy` メソッドを使ってクリーンナップ処理を実行することを知っています。

> **注:** `global-behavior` はハンドラリストを必ず設定しなければいけません。デフォルトでは、jQueryのみ設定されています。これらすべてをオフにすることもできますが、基本的なjQueryプラグインが何もしなくとも利用できるという利点があります。

<h2 id="routing"><a href="#routing">ルーティング</a></h2>

作成時に呼び出せるアプリケーションのスタイルが数多くあります。ナビゲーションアプリから、ダッシュボードから、もしくはMDIインターフェースから。Aureliaはそれらすべてを扱うことができます。そのような場合の多くにおいて、アーキテクチャの主要なコンポーネントは、クライアント側のルーター、すなわちURLからアプリケーションの状態への変換になります。

あなたがすでに [Get Started](/get-started.html) を読んでいれば、ルーティングには二つの部品があることを知っていると思います。一つ目はビューモデル中の `Router` です。これにはルート情報が設定されており、画面遷移をコントロールします。２つ目は、ビューの中にある `router-view` で、ルーターが持つ現在の情報を表示する役割を持ちます。

設定の例をみてみましょう。

```javascript
import {Router} from 'aurelia-router';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['', 'home'],               moduleId: './home/index' },
        { route: 'users',                    moduleId: './users/index',                      nav: true },
        { route: 'users/:id/detail',         moduleId: './users/detail' },
        { route: 'files*path',               moduleId: './files/index',     href:'#files',   nav: true }
      ]);
    });
  }
}
```

まず `Router` が挿入されるようにする必要があります。その後、そのインスタンスをビューモデルの `router` プロパティに設定します。 _プロパティ名は **router** としなければなりません_ 。次に `configure` APIを呼びます。 `configure` APIには関数を渡し、APIは設定オブジェクトを渡します。

ドキュメントのタイトルに使う `title` プロパティを設定することもできます。ただ、一番重要なのはルートの設定の部分です。ルーターの `map` メソッドは、あなたのルート情報を表す、シンプルなJSONデータを引数にとります。最も重要なプロパティは二つで、ルートのパターンを定義する `route` (文字列、もしくは文字列の配列) と、ビューモデルの相対パスを持つ `moduleId` です。他にも、ドキュメントのタイトルを設定する `title` プロパティ、ナビゲーションモデルに含めるかどうかを指定する(この指定は順番を意味する数字でも良い) `nav` プロパティ、 _ナビゲーションモデル_ とバインドする `href` プロパティを設定することもできます。

>**注:** 指定しなかったプロパティは、与えられた情報を踏まえて、フレームワークが規約にもとづいて決めます。

ルートの指定の仕方にはどのうなものがあるのでしょうか?

* 固定ルート
    - 例 'home' - 文字列に完全に一致。
* パラメータを使ったルート
    - 例  'users/:id/detail' - 文字列の一致を確認した後、 `id` パラメーターを解釈する。ビューモデルの `activate` コールバックには、このURLから取り出した値で `id` プロパティをセットしたオブジェクトが渡されます。
* ワイルドカードルート
    - 例 'files*path' - 文字列と、それに続く任意の文字とマッチする。ビューモデルの `activate` コールバックには、このワイルドカードの値を `path` プロパティにセットしたオブジェクトが渡されます。

`nav` プロパティが真の値を持つルートを集めて、 `navigation` 配列が作られます。これはメニュー構造を生成するようなデータバインディングにとても便利です。他にも、バインディングにとって重要なプロパティに、 `isNavigating` プロパティがあります。いままで説明した様々なビューモデル組み合わせた、簡単なマークアップの例を示します:

```markup
<template>
  <ul>
    <li class="loader" if.bind="router.isNavigating">
      <i class="fa fa-spinner fa-spin fa-2x"></i>
    </li>
    <li repeat.for="item of router.navigation">
      <a href.bind="item.href">${item.title}</a>
    </li>
  </ul>

  <router-view></router-view>
</template>
```

<h3 id="the-screen-activation-lifecycle"><a href="#the-screen-activation-lifecycle">画面初期化ライフサイクル</a></h3>

ルーターが画面遷移を処理する際には、遷移元と遷移先のビューモデルに対して、厳格なライフサイクルを適用します。ライフサイクルには4つのステージがあります。ビューモデルのクラスに適切なメソッドを定義することで、任意のステージに対する処理を記述できます。以下にライフサイクルコールバックの一覧を示します:

* `canActivate(params, queryString, routeConfig)` - もしビューモデルに対して _遷移可能か_ どうかを判断させたいときは、このフックを実装します。返値は真偽値、真偽値を返すPromise、またはナビゲーションコマンドです。
* `activate(params, queryString, routeConfig)` - ビューオデルが描画する直前に処理を実行したい場合は、このフックを実装します。Promiseを返して、ルーターにビューとの接続とデータバインドを処理が終わるまで待たせることもできます。
* `canDeactivate()` - ビューモデルから_遷移して良いか_を制御したい場合は、このフックを実装します。返値は真偽値、真偽値を返すPromise、またはナビゲーションコマンドです。
* `deactivate()` - ビューモデルで、他の画面への遷移時に処理を行いたい場合は、このフックを実装します。Promiseを返して、ルーターを処理が終わるまで待たせることもできます。

`params` オブジェクトは個々のルートのパラメーターを持ち、 `queryString` はクエリ文字列とその値を持ちます。そして、 `routeConfig` はルート設定オブジェクトそのものです。

> **注:** _ナビゲーションコマンド_ は `navigate(router)` メソッドを持つ、任意のオブジェクトです。ナビゲーションコマンドを見つけると、現在の遷移はキャンセルされ、ナビゲーションコマンドに制御は移譲されます。すぐに使えるナビゲーションコマンドは、 `Redirect` です。

<h3 id="child-routers"><a href="#child-routers">子ルーター</a></h3>

もしまだ"Get Started"ガイドを読んでいなければ、是非この機会に読んでください。特に、"Bonus: Leveraging Child Routers"の章を見てください。

ルートをビューモデルに対応づけるとき、そのビューモデルは更に独自のルータを持つこともできるのです...そして、そのルーターに対してルートを設定するとき...更にそれと対応付けられたビューモデルは独自のルーターを持つことができ...と、このように入れ子にできます。パスのパターンは親ルーターに対する相対パスとなり、モジュールとビューのidはビューモデルに対して相対となります。これにより、機能や子アプリケーションをカプセル化し、複雑で階層的な状態を扱うことが容易となります。

子ルーターは他のルーターと何もかわりません。そのため、今まで説明してきたことはすべて子ルーターにも適用できます。子ルーターを追加するには、 `Router` を挿入するようにして、子ルートをそれに設定するだけです。すでに説明した画面初期化ライフサイクルは、子ルーターにも当てはまります。ライフサイクルの個々のフェーズは、次のフェーズに移る前にルーター階層全てに対して実行されます。activateフックはトップダウンに実行され、deactivateフックはボトムアップに実行されます。

<h3 id="conventional-routing"><a href="#conventional-routing">規約によるルーティング</a></h3>

Aureliaの全てで、我々は規約による開発を強力にサポートしています。事前にすべてのルートを設定しておくのではなく、動的にルートを選択することも可能です。以下は動的にルートを選択する例です:

```javascript
router.configure(config => {
  config.mapUnknownRoutes(instruction => {
    //check instruction.fragment
    //set instruction.config.moduleId
  });
});
```

やらなければならないことは、 `config.moduleId` プロパティをセットするだけです。それで、準備は完了です。また、 `mapUnknownRoutes` でPromiseを返すことで、遷移先を非同期に決定することもできます。

>**注:** 規約によるルーティングに関係があるわけではないですが、ルーターを非同期に設定する必要に駆られることがあるかもしれません例えば、ルートの設定の前に、ユーザーの権限を取得するWebサービスを呼び出さなければいけない、などです。これを実現するためには、ルーターのビューモデルで、 `configureRouter` コールバックを呼び出してください。このコールバックでは、ルーターを設定することも、必要があればPromiseを返すこともできます。

<h3 id="customizing-the-navigation-pipeline"><a href="#customizing-the-navigation-pipeline">ナビゲーションパイプラインのカスタマイズ</a></h3>

ルーターのパイプラインは順番に実行される個々のステップから構成されています。個々のステップでは、ルーティングの最中に実施されることの変更もできますし、同時にルーティングを止めることもできます。パイプラインにはあなたが独自のステップを追加できる幾つかの拡張ポイントがあります。それは、 `authorize` と `bindmodel` です。 `authorize` は `bindmodel` の前に実行されます。これらの拡張はルートフィルターと呼ばれます。

下記はアプリケーションに認証を追加する例です:

```javascript
import {Router, Redirect} from 'aurelia-router';
import {Container} from 'aurelia-dependency-injection';
import bootstrap from 'bootstrap';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.addPipelineStep('authorize', AuthorizeStep); // ルートフィルタをauthorize拡張ポイントに追加する
      config.map([
        { route: ['welcome'],     moduleId: 'welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',        moduleId: 'flickr',       nav: true, auth: true },
        { route: 'child-router',  moduleId: 'child-router', nav: true, title:'Child Router' },
        { route: '',              redirect: 'welcome' }
      ]);
    });
  }
}

class AuthorizeStep {
  static inject() { return []; }
  constructor() {
  }

  run(routingContext, next) {
    // ルートが "auth" キーを持っているか確認する
    // `nextInstructions`を使う理由は、
    // それが子ルートを含むから。    
    if (routingContext.nextInstructions.some(i => i.config.auth)) {
      var isLoggedIn = /* ここを書き換えること */false;
      if (!isLoggedIn) {
        return next.cancel(new Redirect('login'));
      }

      return next();
    } else {
      return next();
    }
  }
}
```

これらの拡張ポイントはパイプラインに含まれており、またそれ自体が小さいパイプラインを持ち、複数のステップをそれぞれに追加することができます。例えば、上記の `AuthorizeStep`  (単にユーザーがログインしているかどうかをチェックしている)に加え、 `IsAdminStep` を `authorize` 拡張ポイントに追加することができます。 それらは連続して実行されます。

また、 `addPipelineStep` に渡す名前を変えることで、独自の名前付きフィルタを作ることもできます。その例を下記に示します:

```javascript
config.addPipelineStep('myname', MyFirstStep); //  "myname"パイプラインが存在しなければ作成
config.addPipelineStep('myname', MySecondStep); // ステップを追加
config.addPipelineStep('modelbind', 'myname'); // `myname`パイプライン全体を`modelbind`パイプラインの一部として実行するようにする。
```

<h3 id="configuring-push-state"><a href="#configuring-push-state">PushStateを設定する</a></h3>

URLから `#` (ハッシュ) を取り除きたいと考えるなら、アプリケーションで `pushState` を有効にする必要があります。Aureliaはそれをサポートしています!また、それが正しく動作するためには、サーバサイド側での作業も必要になります。まずはAureliaサイドの実装から見ていきましょう。

まずAureliaに `pushState` を使うことを伝えなければいけません。 `router` `config` の中で、次のように指定します:

``` javascript
this.router.configure(config => {
  config.title = 'First Impressions';
  config.options.pushState = true; // <-- これだけ指定する必要がある
  config.map([
    { route: ['','welcome'],  moduleId: './welcome',      nav: true, title:'Welcome' },
    { route: 'child-router',  moduleId: './child-router', nav: true, title:'Child Router' }
  ]);
});
```

[base タグ](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) をHTMLドキュメントの先頭に加えたくなるかもしれません。

次に、サーバサイドは、リクエストによらず `index.html` を返す必要があります。すべてのルーティングはクライアントサイドで行われるからです。ナビゲーションサンプルプロジェクトで、 `gulp watch` タスクを `browsersync` と使っている場合は、それを次のように変更します:

プロジェクトのルート・ディレクトリで、下記のコマンドを打ちます:

```shell
npm install --save connect-history-api-fallback
```

これは今まで説明したことをやってくれるミドルウェアをダウンロードして、インストールします。次に、 _build/tasks_ フォルダを開き、 _serve_ タスクのファイルを開きます。ファイルの先頭かその近くのrequire文の中に、次の文を入れてください:

``` javascript
var historyApiFallback = require('connect-history-api-fallback')
```

下に下がって、 `serve` タスクを新しい `ミドルウェア` を使うよう修正します:

``` javascript
gulp.task('serve', ['build'], function(done) {
  browserSync({
    open: false,
    port: 9000,
    server: {
      baseDir: ['.'],
      middleware: [historyApiFallback, function (req, res, next) { // これは配列の先頭にします
        res.setHeader('Access-Control-Allow-Origin', '*');
        next();
      }]
    }
  }, done);
});
```

これで、nodeサーバはAuleriaにルーティングを任せるようになります。

もしASP.NETのような、.NETのサーバサイドフレームワークを使っている場合は、次のようにします:

* Controllerを作成して、クラス名をつけます。ここではApplicationControllerとすることにします。下記のようになります:

```csharp
public class ApplicationController : Controller {
  public ActionResult Index() {
    return View();
  }
}
```

* "index.cshtml" をViewsフォルダに作成します。

* ルーティング設定を下記のように行います:

```csharp
context.MapRoute(
  name: "AureliaRouting",
  url: "{*.}",
  defaults: new { controller = "Application", action = "Index" }
);
```
上記ではRazorビューファイルを使わなければならないことに注意してください。もし通常のHTMLファイルが使いたければ、いくつか方法があります。 [このStackOverflow記事が役立ちます](http://stackoverflow.com/questions/20871938/render-html-file-in-asp-net-mvc-view) 。

もし [Nancy FX](http://nancyfx.org) を使っているなら、設定は簡単です。 `IndexModule.cs` か、そのような適当な名前のファイルを作成し、次のように書けば良いです:

``` csharp
public class IndexModule : NancyModule {
  public IndexModule()     {
    this.Get["/robots.txt"] = p => this.Response.AsFile("robots.txt");
    this.Get["/sitemap.xml"] = p => this.Response.AsFile("sitemap.xml");
    this.Get["/"] = x => this.View["index"];
    this.Get["/{path*}"] = x => this.View["index"];
  }
}
```

同様のテクニックは他のサーバ環境でも使えます -  どんなサーバを使ってもよいですが、同じ `index.html` をどのようなリクエストに対しても返すようにするのを忘れないで下さい。どんなサーバサイドフレームワークもそれくらいはできるはずです。Aureliaは自身のルートデータにもとづき、どのページをロードするかを決定します。

<h2 id="extending-html"><a href="#extending-html">HTMLを拡張する</a></h2>

Aureliaには強力かつ拡張可能なHTMLテンプレートコンパイラがあります。コンパイラそのものは、HTMLを操作するロジックを持つ、様々な _ビヘイビアタイプ_ と相互作用するアルゴリズムです。すぐに使えるように、Aureliaには三つの中心となるビヘイビアタイプの実装があり、それで日々の作業を十分カバーできると考えています。その三つとは、 _付加ビヘイビア_ 、 _カスタムエレメント_ そして _テンプレートコントローラー_ です。
 
ビヘイビアは、デフォルトではコンパイラに見えません。組み込むためには、三つの方法があります:

* `import` 要素を使ってビヘイビアをビューに読み込む。 `from` 属性でビヘイビアのモジュールへの相対パスを指定します。 ビヘイビアはローカルに定義されます。
* ブートストラップ時に、Aureliaオブジェクトの `.withResources(resources)` を使ってビヘイビアをアプリケーショングローバルに見えるようにする。
* プラグインをインストールして、ビヘイビアをアプリケーショングローバルに見えるように登録する。

>**注:** お勧めの方法は、アプリケーション固有のビヘイビアやバリューコンバーターなどを _resources_ フォルダに入れることです。その後、それらすべてを内部的にプラグインとする _index.js_ ファイルを作成します。最後に、そのプラグインをアプリケーションのブートストラップ時にインストールします。こうすることで、リソースとその登録コードを一緒にして、既知の場所に保管することができます。また、 _main.js_ ファイルを汚すこともありません。

すべてのビヘイビアは、次のフックを実装することで、ビューのライフサイクルに組み込むことができます:

* `bind(bindingContext)` - データバインディングエンジンがビューをバインドするときに起動されます。bindingContextは、ビューがデータバインドされるインスタンスです。
* `unbind()` - データバインディングエンジンがビューをアンバインドするときに起動されます。
* `attached()` - ビヘイビアを含むビューがDOMに組み込まれるときに起動されます。
* `detached()` - ビヘイビアを含むビューがDOMから外れるときに起動されます。

>**注:** `bind` コールバックを実装するときには、初回のバインドが少し異なる流れになることに注意してください。通常、ビヘイビアプロパティはバインド時に個々に呼ばれます。しかし、 `bind` コールバックは、初期化時には呼ばれません。 `bind` コールバックは、すべてのプロパティが値とバインドされた後、一度だけ呼ばれます。これは重要で、役に立つ特徴です。特にすべての値が評価された後に"実行"したいような、複雑な振る舞いに役立ちます。

<h3 id="attached-behaviors"><a href="#attached-behaviors">付加ビヘイビア</a></h3>

付加ビヘイビアは、カスタム属性を付け加えることで、既存のHTMLエレメントに機能や振る舞いを"付加"します。付加ビヘイビアの代表的な利用例は:

* jQueryや、それに類するプラグインをラップする ( `global-behavior` では十分でないときに)。
* 共通のスタイル、クラス、属性バインディングのショートカットを作成する。
* 直接変更できないような既存のHTMLエレメント、カスタムエレメントを変更する。

付加ビヘイビアは横断的感心事を表す傾向があります。たとえば、どんなエレメントにも付加できるツールチップを作成したいことがあるでしょう。付加ビヘイビアは、このようなツールチップ機能をすべてのカスタムエレメントに作りこむより良い発想だと言えます。

Aureliaが持つ付加ビヘイビア実装の一つ、 `show` を見てみましょう。下記がそのコードです:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

`show` ビヘイビアはバインドしている値が偽の場合、クラス属性を付与します。. (この付与されるクラス属性は、付与されるとエレメントを隠します) 実装を見てみましょう:

```javascript
import {Behavior} from 'aurelia-templating';

export class Show {
  static metadata(){
    return Behavior
      .attachedBehavior('show')
      .withProperty('value', 'valueChanged', 'show');
  }

  static inject() { return [Element]; }
  constructor(element) {
    this.element = element;
  }

  valueChanged(newValue){
    if (newValue) {
      this.element.classList.remove('aurelia-hide');
    } else {
      this.element.classList.add('aurelia-hide');
    }
  }
}
```

まず注目するところは、付加ビヘイビアも、今まで見てきたパターンと何ら変わらないクラスだということです。 `メタデータ` がビヘイビアを定義する上で重要な役割を果たしているのに気がつくと思います。メタデータでやっていることを説明します:

* `.attachedBehavior('show')` - HTMLコンパイラに、このクラスを `付加ビヘイビア` としてプラグインすることを通知するメタデータインスタンスです。これで、このビヘイビアは属性に"show"が現れた時、コンパイラによって認識され呼び出されます。Aureliaでは付加ビヘイビアは常にHTMLの一つの属性に関連付けられます。これは1対1の関連性です。
* `.withProperty('value', 'valueChanged', 'show')` - `BehaviorProperty` を作成します。それにより、HTMLコンパイラに、あなたが作成したクラスにHTML属性に対応づける特別なプロパティがあることを伝えます。このメソッドの最初の引数はクラスのプロパティ名です。最後の引数は属性名で、これはプロパティ名と同じ場合は省略できます。2番目の引数は省略可能で、プロパティが変更されたときに呼び出されるコールバックを指定することができます。

オーケー。それでは、規約について説明しましょう。

* もしコールバック関数名が、 {プロパティ名}Changed の場合、省略できます。つまり、先ほどの例の2番目の引数を省略できるということです。
* プロパティ名と属性名が同じなら、省略できます。先ほどの例だと、名前が異なりますから、指定する必要がありました。
* 付加ビヘイビアは常にひとつの属性にのみ対応付けられます。これにより、このシンプルな利用法に特化した最適化が可能となります。もしプロパティ名が"value"であれば、プロパティメタデータを指定する必要はありません。ビヘイビアと同じ名前の属性を自動的に `value` プロパティと関連付けます。
* クラス名が{ビヘイビア名}AttachedPropertyの場合、付加ビヘイビアメタデータを指定する必要はありません。属性名はクラス名から"AttachedBehavior"を取り除いたものを、小文字にしてハイフンでつないだ文字が使われる。例をあげると、behavior-name

これらの規約を利用すると、先ほどの `show` ビヘイビアを次のように定義することができます:

```javascript
export class ShowAttachedBehavior {
  static inject() { return [Element]; }
  constructor(element) {
    this.element = element;
  }

  valueChanged(newValue){
    if (newValue) {
      this.element.classList.remove('aurelia-hide');
    } else {
      this.element.classList.add('aurelia-hide');
    }
  }
}
```

> **注:** では、なぜAureliaの内部ではこの規約を使っていないのだろうか?サードパーティライブラリとしてビヘイビアのライブラリを作成する場合は、これらを省略せず明示した方が良いです。利用者がAureliaの規約に手を加えていないかどうか知ることはできないため、規約に頼っていると、ライブラリが正しく動作しないことにもなります。それを防ぐためには、常に明示的にメタデータを指定することです。ただ自分のアプリケーションで利用する場合には、この規約を活用して、シンプルに開発することが可能です。

次にコンストラクタを見てみましょう。

付加ビヘイビアは、 `inject` 配列で指定することで、付加先のHTML要素に簡単にアクセスすることができます。 `show` ビヘイビアの例では、要素への参照を保存して、後で `classList` を更新することに利用しています。 

最後に、 `valueChanged` コールバックを確認します。以前、これは値が変更されたときに呼び出され、プロパティメタデータに設定されると説明しました。バインディングシステムは値が更新されると、このコールバックを呼び出します。したがって、実装するのは値に応じてクラス属性を付与/削除するコードを書くだけです。

<h4 id="options-properties"><a href="#options-properties">オプションプロパティ</a></h4>

もし複数のプロパティを持つ付加ビヘイビアが作りたかったらどうすればいいのかと思ったことでしょう。付加ビヘイビアは一つの属性と関連付けられるので、それはもっともな疑問です。このような用途では、 `オプションプロパティ` を使います。これにより、単一の属性に複数のプロパティを埋め込むことができます。ブラウザの `style` 属性のようなものです。ここに例を示します:

```javascript
import {Behavior} from 'aurelia-templating';

export class Show {
  static metadata(){
    return Behavior
      .attachedBehavior('my-behavior')
      .withOptions().and(x => {
        x.withProperty('foo');
        x.withProperty('bar');
      });
  }
}
```

これは `my-behavior` という名前で、 `foo` と `bar` という二つのプロパティをもつ付加ビヘイビアを作成します。これらプロパティは、クラスでは直接利用できますが、HTMLでは少し異なる形で設定することになります。どのようにするか見てみましょう:

```markup
<div my-behavior="foo: some literal value; bar.bind: some.expression"></div>
```

ビヘイビア自身ではバインディングコマンドを使っていないことに注目してください。その代わり、属性の値の中で、個々のプロパティにたいしてバインディングコマンドを使えます。バインディングコマンドと同様に、リテラルも利用可能です。

> **注:** `delegate` や `trigger` コマンドは、オプション属性中で利用しないでください。これらは素のDOMイベントと連携するため、常にエレメントに付加されます。ただし、 `call` は利用可能です。

<h3 id="custom-elements"><a href="#custom-elements">カスタムエレメント</a></h3>

カスタムエレメントは、HTMLマークアップに新しいタグを導入します。個々のカスタムエレメントは、Light DOMまたはShadow DOMにレンダリングされる、自身のビューテンプレートを持つことができます。また、カスタムエレメントは任意の数のプロパティを持つこともでき、それらはビューテンプレートで利用でき、HTML上はデータバインド可能な属性となります。

どのように動作するかわかるように、ここで簡単なサンプルを作成してみましょう。 `say-hello` という、誰かにhelloと挨拶するだけのエレメントを作成します。このカスタムエレメントが完成すれば、次のように利用することができます:

```markup
<template>
    <import from="./say-hello"></import>

    <input type="text" ref="name">
    <say-hello to.bind="name.value"></say-hello>
</template>
```

どうやって作りましょうか。付加ビヘイビアの時と同様に、まずはクラスを作成することからスタートします。下記がそのクラスです:

#### say-hello.js
```javascript
import {Behavior} from 'aurelia-templating';

export class SayHello {
  static metadata(){
    return Behavior
      .customElement('say-hello')
      .withProperty('to');
  }

  speak(){
    alert('Hello ${this.to}!');
  }
}
```

もしすでに付加ビヘイビアのセクションを読んでいたら、何をやっているのかはわかるとおもいます。ここにも幾つかの規約があり、もしその気になれば次のように省略することができます:

#### say-hello.js (with conventions)
```javascript
import {Behavior} from 'aurelia-templating';

export class SayHelloCustomElement {
  static metadata(){
    return Behavior.withProperty('to');
  }

  speak(){
    alert('Hello ${this.to}!');
  }
}
```

カスタムエレメントにはビューもありますね。これがビューです。

#### say-hello.html
```markup
<template>
    <button click.delegate="speak()">Say Hello To ${to}</button>
</template>
```

ご覧の通り、クラスのプロパティやメソッドにアクセスできています。ビューテンプレートで利用したいプロパティをプロパティメタデータで宣言する必要はありません。属性として使いたいプロパティだけを宣言すればよいのです。

やることはこれだけです。カスタムエレメントでも通常のビューモデル/ビューと同じ名前規約に従います。カスタムエレメント固有のメタデータもあるので、覚えておきましょう:

* `.useShadowDOM()` - これはコンポーネントのビューをLight DOMではなく、Shadow DOMにレンダリングします。この用語について詳しくなければ、 [この記事](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/) を読んでください。Shadow DOMを使う場合、 _コンテンツセレクタ_  をビューテンプレートの中で利用できます。
* `.noView()` - ビヘイビアがすべてコードで実装されていて、ビューを持たないような場合はこのオプションを指定します。
* `.useView(relativePath)` - 命名規約とは異なるビューを利用する場合は、そのビューへの相対パスを引数として、このオプションを利用します。

<h3 id="template-controllers"><a href="#template-controllers">テンプレートコントローラー</a></h3>

テンプレートコントローラーは、DOMを展開されていないHTMLテンプレートに変換するものです。コントローラーはその後、いつどこで(また、何回)テンプレートをDOM中に展開するかを決めることができます。 `if` や `repeat` がテンプレートコントローラーの例です。このビヘイビアをDOMノードに配置すれば、それはビヘイビアに制御されるテンプレートになるというわけです。

`if` ビヘイビアの実装を確認して、どのようにまとめているのか見てみましょう。下記がソースコード全体です:

```javascript
import {Behavior, BoundViewFactory, ViewSlot} from 'aurelia-templating';

export class If {
  static metadata(){
    return Behavior
      .templateController('if')
      .withProperty('value', 'valueChanged', 'if');
  }

  static inject() { return [BoundViewFactory, ViewSlot]; }
  constructor(viewFactory, viewSlot){
    this.viewFactory = viewFactory;
    this.viewSlot = viewSlot;
    this.showing = false;
  }

  valueChanged(newValue){
    if (!newValue) {
      if(this.view){
        this.viewSlot.remove(this.view);
        this.view.unbind();
      }

      this.showing = false;
      return;
    }

    if(!this.view){
      this.view = this.viewFactory.create();
    }

    if (!this.showing) {
      this.showing = true;

      if(!this.view.bound){
        this.view.bind();
      }

      this.viewSlot.add(this.view);
    }
  }
}
```

テンプレートコントローラーの固有の側面について掘り下げる前に、いままでやってきたことと似ている部分について説明しましょう。まず、単純なクラスとメタデータですね。以前の二つのビヘイビアと同様にメタデータを宣言しています。規約も同様です。従って、クラス名を `IfTemplateController` にすれば、メタデータで宣言する必要はありません。同様に、 `valueChanged` コールバックがあれば、プロパティメタデータを宣言する必要もありません。付加ビヘイビアと同じ規約ですね。

では、違いはなんでしょうか?コンストラクタを見てください。このテンプレートコントローラーには二つ挿入されているものがあります: `BoundViewFactory` と `ViewSlot` です。

`BoundViewFactory` はコントローラーが付加されているHTMLテンプレートを展開する役割を持っています。コンパイルの心配をする必要はありません。このクラスがすべて面倒をみてくれます。なぜ"Bound"ビューファクトリーという名前が付いているのでしょうか?これは、あらかじめ親のバインディングコンテキストを参照する形になっているためです。これが"bound(バインドされた)"という意味です。したがって、 `create` メソッドを呼ぶと、このコンテキストとバインドした新しいビューをテンプレートから作成するのです。これは `if` ビヘイビアで想定される挙動ですね。しかし `repeat` ビヘイビアでは異なります。この場合、 `create` を呼ぶ際には、配列内の要素にバインドされたビューが必要になります。この動作を実現するためには、ビューにバインドしたいオブジェクトを `create` メソッドに渡せば良いのです。

`ビュースロット` はテンプレートを取り除く、DOM上の位置やスロットを表します。これは通常ビューインスタンスを追加する場所でもあります。

>**注**: これまでに説明してきたビヘイビアとは違い、テンプレートコントローラーはより直接的にフレームワークの  _プリミティブ_ と連携します。ビュー、ビューファクトリー、ビュースロットはテンプレートエンジンの低レベルな構成要素です。

`valueChanged` コールバックを見てください。そこで、 `if` ビヘイビアがビューを作成して、値が真の場合にビューをスロットに追加しています。重要なポイントがいくつかあります:

* ビヘイビアはビュースロットにビューを追加する _前に_ `bind` を呼び出します。これは現在のDOMの外側で、内部的なバインディングを最初に評価します。これは性能上重要です。
* 似ていますが、DOMからビューを取り除いた _後に_ `unbind` を呼び出します。
* ビューが最初に作成されると、値が偽の場合でも、 `if` ビヘイビアはそれを保持します。インスタンスをキャッシュするということです。Aureliaはビューを再利用しますし、それを他のバインディングコンテキストに利用することも可能です。これもまた、性能にとって重要です。不要なビューの再生成を抑制するからです。

<h2 id="eventing"><a href="#eventing">イベント</a></h2>

イベントは、アプリケーション中のコンポーネントを疎結合にするときに重要となるツールです。Aureliaは標準的なDOMイベントとともに、アプリケーション固有のイベントを `EventAggregator` 経由でサポートします。

<h3 id="dom-events"><a href="#dom-events">DOMイベント</a></h3>

DOMイベントはUI固有のメッセージの伝送に使います。これはアプリケーション固有のメッセージに使うべきではありません。AureliaはUIイベントのためにDOMに機能を追加したりしていません。ビヘイビアは、関連する `エレメント` をコンストラクタに挿入することができます。その後、この `エレメント` を使って、イベントを起動することができます。DOMイベントの作成と起動について学ぶには、 [この記事を読んでください](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events) 。

<h3 id="the-event-aggregator"><a href="#the-event-aggregator">イベントアグリゲーター</a></h3>

疎結合のアプリケーションイベントが必要なら、 `イベントアグリゲーター` を使うことになるでしょう。それが持つ合理的なpub/subインターフェースは、幅広いメッセージングのシナリオに対応します。

イベントアグリゲーターはメッセージチャネルにメッセージを送ることもできますし、強い型付けされたメッセージを送ることもできます。チャネルにメッセージを送る方法を見てみましょう:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class APublisher{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    publish(){
        var payload = {}; //any object
        this.eventAggregator.publish('channel name here', payload);
    }
}
```

最初にシングルトンであるイベントアグリゲーターを、DIを通じて取得することから始まります。次にその `publish` メソッドを、チャネル名と、チャネルに送るデータペイロードを引数として呼び出します。受信側でこのメッセージを受け取るために必要な設定です:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class ASubscriber{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    subscribe(){
        this.eventAggregator.subscribe('channel name here', payload => {
            //メッセージに対して実施したい処理を書く
        });
    }
}
```

見ての通り、同じチャネル名を用いてメソッドを呼び出しますが、ペイロードの代わりにペイロードを受け取った時に起動されるコールバック関数を渡しています。

別の方法として、強く型付けされたメッセージを送受信することもできます。送信側の例です:

```javascript
export class SomeMessage{ }
```

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';
import {SomeMessage} from './some-message';

export class APublisher{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    publish(){
        this.eventAggregator.publish(new SomeMessage());
    }
}
```

この場合、特定のメッセージ型のインスタンスを送信します。受信側の例です:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';
import {SomeMessage} from './some-message';

export class ASubscriber{
    static inject(){ return [EventAggregator]; }
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    subscribe(){
        this.eventAggregator.subscribe(SomeMessage, message => {
            //メッセージを受信した際の処理
        });
    }
}
```

受信側は`SomeMessage`のインスタンスが送信されたら呼び出されます。受信はポリモーフィックに行われます。つまり、SomeMessageのサブクラスが送信されても、同様に受信側に通知されます。

>**注:** すべての `subscribe` メソッドは _廃棄関数_ を返します。 この関数を呼び出すことで、受信を取りやめ、以降のメッセージを受け取らないようにすることができます。廃棄するタイミングとしては、ビューモデルがルーターに管理されている場合は、ビューモデルの `deactivate` コールバック、それ以外なら ビューモデルの `detached` コールバックが良いでしょう。

<h2 id="http-client"><a href="#http-client">HTTPクライアント</a></h2>

Aureliaは、利用者の便宜のため、簡易な `HttpClient` クラスを用意して、ブラウザのXMLHttpRequestオブジェクトへの使いやすいインターフェースを提供しています。`HttpClient` はAureliaのブートストラップ時に読み込むモジュールには含まれていません。多くのアプリケーションは様々なデータ取得方法を利用するため、必ずしもこれを使う必要はありません。そのため、これを利用する場合には、次のコマンドを使ってインストールする必要があります:

```shell
jspm install aurelia-http-client
```

次のように利用します:

```javascript
import {HttpClient} from 'aurelia-http-client';

export class WebAPI {
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }

    getAllContacts(){
        return this.http.get('uri goes here');
    }
}

```

`HttpClient` の実装は次のようになっています:

```javascript
export class HttpClient {
  constructor(baseUrl = null, defaultRequestHeaders = new Headers()){
    this.baseUrl = baseUrl;
    this.defaultRequestHeaders = defaultRequestHeaders;
  }

  send(requestMessage, progressCallback){
    return requestMessage.send(this, progressCallback);
  }

  get(uri){
    return this.send(new HttpRequestMessage('GET', join(this.baseUrl, uri))
        .withHeaders(this.defaultRequestHeaders));
  }

  put(uri, content, replacer){
    return this.send(new HttpRequestMessage('PUT', join(this.baseUrl, uri), content, replacer || this.replacer)
        .withHeaders(this.defaultRequestHeaders));
  }

  patch(uri, content, replacer){
    return this.send(new HttpRequestMessage('PATCH', join(this.baseUrl, uri), content, replacer || this.replacer)
        .withHeaders(this.defaultRequestHeaders));
  }

  post(uri, content, replacer){
    return this.send(new HttpRequestMessage('POST', join(this.baseUrl, uri), content, replacer || this.replacer)
        .withHeaders(this.defaultRequestHeaders));
  }

  delete(uri){
    return this.send(new HttpRequestMessage('DELETE', join(this.baseUrl, uri))
        .withHeaders(this.defaultRequestHeaders));
  }

  jsonp(uri, callbackParameterName='jsoncallback'){
    return this.send(new JSONPRequestMessage(join(this.baseUrl, uri), callbackParameterName));
  }
}
```

ご覧の通り、このクラスには簡易的に利用できる `get` 、 `put` 、 `patch` 、 `post` 、 `delete` そして `jsonp` メソッドがあります。これらのメソッドは `HttpRequestMessage` を送信しますが、 `jsonp` メソッドだけは `JSONPRequestMessage` を送信します。メソッドの返値は `HttpResponseMessage` を返す `Promise` です。

`HttpResponseMessage` は次のプロパティを持っています:

* `response` - サーバからの生の結果を返します。
* `responseType` - 想定されるレスポンスタイプです。
* `content` - 生の `レスポンス` を `レスポンスタイプ` に応じて整形したものを返します。
* `headers` -  解析されたヘッダ情報を含む、 `ヘッダ` オブジェクトを返します。
* `statusCode` - 返されたステータスコードです。
* `statusText` - テキストのステータスメッセージです。
* `isSuccess` - リクエストが成功したかどうかを、ステータスコードの範囲によって判断します。
* `reviver` - 生 `レスポンス` コンテンツを変換するのに使う関数です。
* `requestMessage` - 送信したリクエストメッセージへの参照です。

> **注:** デフォルトでは `HttpClient` はJSONレスポンスタイプを想定しています。

<h2 id="customization"><a href="#customization">カスタマイズ</a></h2>

<h3 id="view-and-view-model-conventions"><a href="#view-and-view-model-conventions">ビューとビューモデル規約</a></h3>

どのようにビューとビューモデルはリンクしているのでしょうか? module idに基づいたシンプルな規約になっています。id (パスの必須部分)が './foo/bar/baz' のようなビューモデルの場合、デフォルトではビューモデルが `./foo/bar/baz.js` で、ビューが `./foo/bar/baz.html` となります。この規約を変更したい場合を考えてみましょう。仮にすべてのビューモデルが `view-models` フォルダに配置され、それに対応するビューを `views` フォルダに置くようにする場合、どのようにすればいいでしょうか?これを実現するためには、規約に基づくビューストラテジーの振る舞いを変更する必要があります。次のようにします:

```javascript
import {ConventionalView} from 'aurelia-framework';

ConventionalView.convertModuleIdToViewUrl = function(moduleId){
  return moduleId.replace('view-models', 'views') + '.html';
}
```

このコードは初期化時のロジックに含めて実行するようにし、他のビヘイビアがロードされる前に有効になるようにしなければなりません。これはカスタムエレメントも含め *すべて* に影響します。規約を変えるためには、`convertModuleIdToViewUrl` の実装を適切に変更する必要があります。

> **注:** これが、なぜサードパーティのプラグインは規約に頼ってはいけないかという例です。開発者は彼らのアプリケーションのニーズに合わせて、規約を変更するかもしれないためです。


<h3 id="reusing-an-existing-vm"><a href="#reusing-an-existing-vm">ビューモデルの再利用</a></h3>

複数のルートに対して、同じビューモデルを使いたくないケースもあるでしょう。デフォルトでは、Aureliaはこれらのルートを同一ビューモデルのエイリアスとみなすので、ビルドと付加プロセス、およびライフサイクルは一度のみ実行されます。これに満足できないこともあるでしょう。次の例を見てください:

```javascript
import {Router} from 'aurelia-router';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: 'product/a',         moduleId: 'product'      nav: true },
        { route: 'product/b',         moduleId: 'product',     nav: true },
      ]);
    });
  }
}
```

ビューモデルのライフラサイクルが呼ばれるのは一度だけですから、ユーザーがルートを `Product A` から `Product B` に変えた場合、それを認識できないという問題があります。

この問題に対処するには、 `determineActivationStrategy` メソッドを実装し、ルーターに何をすればいいかのヒントを返す必要があります。例として、ビューモデルの再構成を行う場合は、次のように書きます:

```javascript
import {REPLACE} from 'aurelia-router';

export class YourViewModel {
  determineActivationStrategy(){
    return REPLACE;
  }
}
```

もしライフサイクルのリフレッシュを行いたいなら ( `<compose>` バインディングと合わせて使うと有効です) 次のようにします:

```javascript
import {INVOKE_LIFECYCLE} from 'aurelia-router';

export class YourViewModel {
  determineActivationStrategy(){
    return INVOKE_LIFECYCLE;
  }
}
```

> **注:** リフレッシュを強制することで、Aureliaはビューモデルを再構成することに注意してください。 性能の観点からすると、データをやりとりするだけなら、 `router.currentInstruction` のシンプルなオブザーバーで十分です。
