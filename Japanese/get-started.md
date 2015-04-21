# はじめに

**Aureliaにようこそ!** このチュートリアルではAureliaを使って単純なアプリケーションを作成し、それを通じてAureliaの中心となる概念を説明します。読者はJavaScript、HTML、そしてCSSに慣れていることを想定しています。さっと概要を把握するために、"HTMLページを準備する" セクションを先にみることをおすすめします。 Aureliaを使うことがいかに直感的かわかるでしょう。次に、実際にものを作る準備ができたなら、戻って "環境を設定する"、"プロジェクト構成とビルドの準備" を読むと良いでしょう。このチュートリアルの完全な結果を見るためには、我々の[ナビゲーションスケルトンプロジェクト](https://github.com/aurelia/skeleton-navigation/releases)を見てください。

> **注:** このガイドの翻訳を探していますか? 我々の [ドキュメントリポジトリ](https://github.com/aurelia/documentation) を見てください。

## 環境を設定する

まずはモダンなJavaScriptアプリケーションをビルドするための素晴らしいツール群を準備することから始めましょう。我々のすべてのツールは [Node.js](http://nodejs.org/) 上で作られています。すでにNode.jsをインストール済み? 素晴らしい! インストールしていなければ、 [公式サイト](http://nodejs.org/) へ行き、ダウンロードしてインストールしてください。それ以外に必要なものはすべてNodeのパッケージマネージャー ([npm](https://docs.npmjs.com/getting-started/what-is-npm)) 経由でインストールされます。もしすでにnpmをインストール済みの場合でも、その他のツールとのトラブルを避けるためにも、  [最新バージョン](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) であることを確認してください。

最初に、ビルド自動化ツールの [Gulp](http://gulpjs.com/) をインストールすることから始めます。もしまだインストールしていなければ、npmを使って次のようにセットアップしてください (`sudo` を使う必要があるかもしれません):

  ```shell
  npm install -g gulp
  ```

次に、 [jspm](http://jspm.io/) をインストールします。これはクライアントサイドのパッケージマネージャーです。次のようにしてください:

  ```shell
  npm install -g jspm
  ```

> **注:** jspmはBowerやYeomanのように、 [git](http://git-scm.com/) を活用します。gitをまだインストールしていなければ、インストールしておく必要があります。また、jspmはインストールするパッケージをGitHubに問い合わせますが、GitHubは匿名のAPIアクセスに上限があります。この問題を回避するために、あなたのGitHub認証情報を使うようにjspmを設定することが推奨されます。これを行うために `jspm registry config github` を実行して、以降のプロンプトに従ってください。jspmを使いたくない? 問題ありません。すべてのAureliaパッケージは [Bower](http://bower.io/) でも取得可能です。

## プロジェクト構成とビルドの準備

ツールがインストールできたら、アプリケーションの基礎構造の準備にとりかかりましょう。まずは、 [ナビゲーションスケルトンをダウンロードします](https://github.com/aurelia/skeleton-navigation/releases) 。これをunzipして、フォルダ名を _navigation-app_ に変更します。

> **注:** 別の方法として、[Yeoman](http://yeoman.io) を使い、プロジェクトのスケルトンを「生成」することもできます。次のようにします:
>
>  ```
>  npm install -g yo generator-aurelia
>  yo aurelia
>  ```

このフォルダの中に必要なものが全て入っています。基本的なビルド、パッケージ設定、スタイルなどです。

あらかじめ含まれている _index.html_ と _src_ フォルダ中のファイルを見ていくのも良いですが、チュートリアルを開始する前にこの二つを削除するのをおすすめします。それによりAureliaアプリケーションをスクラッチから作る方法をより効果的に学ぶことができます。

これらの準備ができたら、いくつかのコマンドを実行します。

1. コンソールを開いて _navigation-app_ ディレクトリに移動する。

2. 次のコマンドを実行し、パッケージマニフェストの _devDependencies_ セクションに記載してあるGulpプラグインをインストールする。

  ```shell
  npm install
  ```
3. 次のコマンドを実行し、パッケージマニフェストの _jspm.dependencies_ セクションに記載してあるAureliaライブラリと、bootstrap、font-awesomeをインストールする。

  ```shell
  jspm install -y
  ```

ここまでに実行したすべてのことは標準的なNode.jsのビルドとパッケージ管理プロセスです。Aureliaに固有のものは何一つありません。モダンなJavaScriptプロジェクトの準備とビルド設定を一から通してみただけです。もしかしたらあなたもすでに慣れ親しんでいるプロセスかもしれませんが、もしそうでなければ、新しく、エキサイティングな世界へようこそ!

> **注:** AureliaはBootstrapとFont-Awesomeに依存して **いません** 。これらをこのチュートリアルで使っているのは、細かい設定なしにまともな見た目をさっと作ることができるからです。

## HTMLページを準備する

今までの手順を実行していれば、あなたはすべてのライブラリ、ビルド設定など、AureliaですごいJavaScriptアプリケーションを作るために必要なすべてのツールを揃えたことになります。次にやることはプロジェクトフォルダのルートに _index.html_ ファイルを作成することです。下記はサンプルですが、新規のAureliaアプリケーションの良いテンプレートになるでしょう。

### index.html
```markup
<!doctype html>
<html>
  <head>
    <title>Aurelia</title>
    <link rel="stylesheet" type="text/css" href="jspm_packages/npm/font-awesome@4.3.0/css/font-awesome.min.css">
    <link rel="stylesheet" type="text/css" href="styles/styles.css">
  </head>
  <body aurelia-app>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
     System.config({
       "paths": {
         "*": "dist/*.js"
       }
     });
   </script>
    <script>
      System.import('aurelia-bootstrapper');
    </script>
  </body>
</html>
```

はい、これだけです。これが今作っているアプリケーションに必要な唯一のHTMLページです。ドキュメントのヘッダはすぐに分かると思います: font-awesome、カスタムスタイルシートへのリンクです。重要なのはbodyです。

> **注:** ローカルに保存されているbootstrapやfont-awesomeのフォルダ名が、href属性に記載のリンク先と一致しているか必ず確認してください。このドキュメントを書いた後に、それぞれのライブラリのバージョンが上がっている可能性があります。

scriptタグから解説しましょう。最初に読み込んでいるのは _system.js_ 、ES6標準ベースのモジュールローダーです。これはAureliaライブラリと一緒にあなたが書くコードを読み込むものです。次に _config.js_ を読み込みます。これにはローダーの設定が含まれています。これはあなたがjspmコマンドを実行したときに自動的に生成されます。jspmは我々が推奨するクライアントサイドのパッケージマネージャーです。クライアントサイドのパッケージ管理とES6互換のモジュールローダーを統合することで、素晴らしい開発を体感することができます。その直下では `System.config` を呼び出しています。ここでコンパイルされたJavascriptコードの出力先を指定しています。

> **注:** AureliaフレームワークはjspmやSystemJSに縛られてはいません。我々はrequireスタイルのAPIである、RequireJSやDojoローダーも最初から使えるようにしています。また、自分で自分の望むようなパッケージ管理を実装することもできます。とはいえ、我々はjspm/SystemJSが現在のES6指向のソリューションの中ではベストだと考えているので、推奨方法としています。

モジュールローダーとその設定を読み込んだら、 `baseUrl`  をコンパイル済みソースコードの場所を指すように設定します。その後、 `System.import` で `aurelia-bootstrapper` モジュールを読み込みます。

bootstrapperが読み込まれると、HTMLドキュメント中から _aurelia_ 属性を探します。この例の場合はbodyの `aurelia-app` 属性が見つかります。これはbootstrapperに _app_ ビューモデルとビューを読み込むように指示するもので、これは規約で _app.js_ と _app.html_ という名前のファイルになっています。その後、これらをDOM中にAureliaアプリケーションとして組み込みます。

ちょっと待って下さい。まだ _app_ ビューモデルやビューがないですね。さて...え、これから作るんですか!?

## 最初の画面を作る

Aureliaでは、ユーザーインターフェースコンポーネントは _ビュー_ と _ビューモデル_ の組で構成されます。 _ビュー_ はHTMLで書かれDOMにレンダリングされます。 _ビューモデル_ はJavaScriptで書かれて_ビュー_にデータや振る舞いを提供します。Aureliaの強力な _データバインディング_ がこの二つを結びつけ、データの変更を _ビュー_ に反映したり、その逆を行ったりします。

どのように動作するのか見てみましょう...

_src_ フォルダに _app.html_ ファイルと _app.js_ ファイルを作成します。これはbootstrapperが探していた、appのビューとビューモデルになります。最初に _ビューモデル_ から始めましょう。 _firstName_ と _lastName_ を持つ簡単なクラスを作ります。そこから _fullName_ を作るプロパティを足して、歓迎メッセージを返すメソッドを追加します。下記のようになります:

### app.js
```javascript
export class Welcome{
  heading = 'Welcome to the Aurelia Navigation App!';
  firstName = 'John';
  lastName = 'Doe';

  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }

  welcome(){
    alert(`Welcome, ${this.fullName}!`);
  }
}
```

何ですか...これJavaScriptですか?

はい。その通りです。これはECMAScript 7 (ES7)、多くの言語拡張が追加されたJavaScriptの次の次のバージョンです。幸いなことに、今までの手順でダウンロードしたGulpファイル中で、 [Babel](https://babeljs.io/) というとても素晴らしいトランスパイラが含まれています。これは、現在のブラウザ上で将来のJavaScriptを書き、動作できるようにするものです。これを使ってモジュールや、クラスや、ラムダ式や、文字列の補完や、その他の機能を使うことができます。すごい! それで、どうやって _ビューモデル_ を作ればいいのでしょうか? 普通のクラスを作ってフレームワークに _export_ するだけです。楽勝です。

> **注:** Aureliaアプリケーションを書くのにBabelを使わなければいけないわけではありません。ES7すら使わなくても大丈夫です。TypeScriptやCoffeeScript...もしくは現在のブラウザで動作する言語、すなわちES5を使うことができます。あなたがやらなければいけないことは、利用する言語の標準的なやり方でクラスを作成することです。それでちゃんと動作します。しかし、我々はES7が素晴らしいと思っていますし、まずはそれを使うことをまずは考えて欲しいです。モジュールのexportやクラスといった最新のJavaScriptの機能についてより深く学ぶには、[The Babel Learning Guide](http://babeljs.io/docs/learn-es6/) を読むことをおすすめします。

オーケー。これで基本的なデータと振る舞いのある _ビューモデル_ が用意できました。次にいわば共犯者の... _ビュー_ について見てみます。

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

すべてのビューは `template` タグ中に含まれています。このビューはbootstrapで修飾された単純な入力フォームです。入力コントロールを見てください。 `value.bind="firstName"` が見えますか? これは入力の _value_ とビューモデルの _firstName_ プロパティをバインドするものです。ビューモデルのプロパティが変更されるとすぐ、新しい値が入力コントロールに反映されます。入力コントロールの値を変更すると、Aureliaが新しい値をビューモデルに渡します。とても簡単です。

この例にはもっと面白いことがあります。最後のform groupにはHTMLコンテンツ中に `${fullName}` と書いてあるのがわかると思います。これが文字列補完です。これはビューモデルからビューへの一方向バインディングで、自動的に文字列に変換されドキュメントに埋め込まれます。最後に、formエレメントそのものを見てください。 `submit.delegate="welcome()"` と書いてあるのに気がつくと思います。これがイベントバインディングです。これはイベントデリゲーションを使って _submit_ イベントをバインドし、フォームがsubmitされたら _welcome_ メソッドが実行されるようにしています。

> **注:** イベントデリゲーションを初めて聞いたかもしれません。これはイベントハンドラを個々のノードに割り当てるのではなく、ドキュメントレベルで一個のイベントハンドラを割り当て、すべての種類のイベントに対応することで、より効率的にイベントを扱えるようにするテクニックです。

実際に動かしてみましょう。コンソールで次のコマンドを入力し、ビルドしてWebサーバを起動します。

```shell
gulp watch
```

ブラウザから [http://localhost:9000/](http://localhost:9000/) を開き、アプリケーションを確認します。フォームの入力コントロールに何か入力すると、Full Nameが計算されて表示されます。ボタンを押すとメソッドが呼ばれるのがわかります。

> **注:** もし動作しないようなら、npmを最新に [アップデート](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) してみてください。

> **注:** Aureliaは適応的なテクニックを使い、個々のプロパティの変更を監視するベストな方法を選択します。例えば、もしあなたが [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) が利用できるブラウザを使っている場合、 _firstName_ と _lastName_ の両方をこれを使って監視します。そうでない場合、マイクロタスクキューに変更を蓄積するようなgetterとsetterを生成して、Object.observeの振る舞いをエミュレートします。計算されるプロパティの _fullName_ はこのいずれの方法でも監視できないため、ダーティチェックを行います。状況に応じて最適な手法を使いますが、特殊なモデルの型に応じた監視方法をフレームワークに"教える"ために、あなたが固有の方法を組み込むこともできます。とてもクールだと思っています。

> `.bind` コマンドはすべてのプロパティに対してデフォルトのバインディングを適用します。デフォルトはフォームコントロールに対して双方向の、それ以外の全てに対して一方向のバインディングを行います。この振る舞いは明示的に `.one-way` や `.two-way` 、 `.one-time` コマンドを使って上書きすることが可能です。同様に、 `.delegate` をイベントデリゲーションに使いますが、 `.trigger` を使ってメソッドを直接エレメントに結びつけることもできます。

## ナビゲーションの追加

これはナビゲーションを持ったアプリケーションのチュートリアルですから、幾つかの画面を追加して、クライアントサイドのルーターを設定した方がいいと思いますよね? ではまず _app.js_ と _app.html_ を _welcome.js_ と _welcome.html_ にそれぞれファイル名を変えてください。これがこのアプリケーションの最初の画面になります。さて、"レイアウト"と"マスターページ"を提供する、新しい _app.js_ と _app.html_ を作成しましょう。ビューにはナビゲーションのUIと、現在のページのコンテンツを配置するプレースホルダを含めます。ビューモデルにはルーターのインスタンスと、ルートの設定を含めます。ルーターの設定の方法がわかるように、ビューモデルから始めましょう。

### app.js

```javascript
import {inject} from 'aurelia-framework';
import {Router} from 'aurelia-router';
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

@inject(Router)
export class App {
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['','welcome'],  moduleId: './welcome',      nav: true, title:'Welcome' }
      ]);
    });
  }
}
```

オーケー。ここでも興味ぶかい、新しい要素があります。我々はルーターを使うので、ファイルの先頭でルーターをimportしています。これは前にも出てきたES6/ES7の能力です。次に主となるアプリケーションのレイアウトのデータと振る舞いを保持する _App_ クラスを作成します。コンストラクタを見てください。そこでは、Appクラスが作成されるときに、 _router_ インスタンスに何か入っていることを想定していますね。どこから与えられるのでしょうか?

Aureliaはアプリケーションのレンダリングに必要なUIコンポーネントを作成します。それは上記の例のように、コンストラクタを通じて依存しているオブジェクトを渡す、 [Dependency Injection](http://en.wikipedia.org/wiki/Dependency_injection) コンテナの能力を使って成し遂げられます。どうやってDIシステムは提供しなければいけないものを知るのでしょうか? そのために必要なことは `inject` ES7 デコレータを追加し、必要なインスタンスの型のリストをパラメータとして渡すだけです。コンストラクタ引数につき一つのパラメータが必要です。上記の例では、一つのルーターインスタンスが必要ですから、 `Router` 型をパラメータとして渡しています。

> **注:** もしデコレータを使いたくない場合は、static `inject` メソッドもしくはプロパティをクラスに追加し、injectする型の配列を返すことで代用できます。

ルータはクラスの公開プロパティとして設定する必要があります。プロパティ名は _router_ としなければなりません。これは重要です。この名前を _taco_ にしようとか、そんな妙な考えをおこしてはいけません。いいですか? これはルーターなので、名前は _router_ 。それでみんな幸せになれます。重要なのでもう一度いいます。プロパティ名は _router_ に **しなければなりません** 。

> **注:** 将来のアップデートでこの少し奇妙な制限は解除される予定です。

わかりました。ルーターを設定しましょう。簡単です。ドキュメントのタイトルを決めて、それからルートを設定します。それぞれのルートは次のプロパティを持ちます:

* `route`: これはマッチしたときにこのルートに遷移するパターンを指定します。上記のように固定のルートも設定できますし、 `customer/:id` のようにパラメーターを含んでいてもよいです。ワイルドカードや、クエリ文字列も利用できます。routeは単一のパターンか、パターンの配列です。
* `moduleId`: このルートでレンダリングしたいビュー/ビューモデルの組を指定する、現在のビューモデルからの相対パスです。
* `title`: ドキュメントのタイトルを指定することができます。オプションです。
* `nav`: このルートを _ナビゲーションモデル_ に含めて、対応するUIを生成したい場合、これをtrue（または順番を指定する数値）にします。

> **注:** ES6のimportを使ってbootstrapのJavascriptとCSSを読み込むやり方に気づいていましたか?

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
我々のシンプルなアプリケーション構築の規約に従えば、 `App` クラスは上のビュー、 _app.html_ のデータ境界となります。このマークアップの大部分は主となるナビゲーション構造の構築を行うものです。すでに基本的なバインディングと文字列補完は見てきましたから、ここで出てきた新しい要素に注目しましょう。navbar-navの `ul` エレメントを見てください。ここの `li` では、 `repeat.for="row of router.navigation"` という表現を使った繰り返しを用いています。これは `router.navigation` 配列の個々の要素に対して一つの `li` 要素を作成します。ローカル変数は _row_ で、これを `li` とその子エレメントで利用しています。

> **注:** ルーターの `navigation` プロパティは、ルートの設定で `nav:true` としたルートがすべて含まれる配列です。Aureliaは `repeat.for` シンタックスをES6の新標準 `for..of` ループに似せて作っています。これらから、遷移可能なルートの配列それぞれに対してUIを生成する処理を行っていることがわかると思います。

同じ `li` 上で、文字列補完をつかった動的なクラスの追加/削除の例を見ることができます。それより下に二つ目の `ul` があります。その唯一の子要素 `li` のバインディング `if.bind="router.isNavigating"` が見えますか? これはバインドされた式の値によって `li` を追加/削除しています。都合がいいことに、ルーターは `isNavigating` プロパティを更新しています。いつでも...遷移しているときに。

最後に確認しておきたいのは、ビューの最後の近くにある `router-view` エレメントです。ビューモデルに `Router` を持つときは常に、ビューに `router-view` がなければいけません。この二つは同志で、 `Router` がどんなルートを返したとしても、現在地が `router-view` に表示されるように自動で接続されます。

準備ができたら、先に進んで `gulp watch` コマンドでサーバを起動しましょう。ブラウザを開いて見てみます。メインナビゲーションに一つの選択されたタブ、"welcome"ルートが見えるはずです。 _welcome_ ビューがメインコンテンツの領域に表示され、以前のサンプル同様に動作しています。ブラウザのデバッグツールを開いて、DOMを見てください。 _welcome_ ビューのコンテンツが `route-view` の内部に表示されているのがわかると思います。

> **注:** この間gulp watchタスクを立ち上げたままにしていたなら、あなたが何かしら変更をしたらブラウザが自動的にリフレッシュしていたのに気がついたかもしれません。これは `browser-sync` のおかげで、これは我々が便利なようにとデフォルトのgulp設定に含めておいたものです。

## 2つ目のページを追加する

よし、技術的にはナビゲーションアプリケーションと言ってよいものになりました...しかし1ページしかないのでは面白くありません。2ページ目を追加しましょう。どうすればいいかわかりますか? わかると思いますよ...

Flickrの画像を幾つか表示してみましょう。そのために、最初に(まだ存在していないですが)ページへのルートを設定しましょう。

### app.js (更新版)

```javascript
import {inject} from 'aurelia-framework';
import {Router} from 'aurelia-router';
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

@inject(Router)
export class App {
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['','welcome'],  moduleId: './welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',        moduleId: './flickr',       nav: true }
      ]);
    });
  }
}
```

_flickr.js_ と _flickr.html_ を作る必要があると思っていましたか? 正解です。以下がそのソースです:

### flickr.js

```javascript
import {inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@inject(HttpClient)
export class Flickr{
  heading = 'Flickr';
  images = [];
  url = 'http://api.flickr.com/services/feeds/photos_public.gne?tags=rainier&tagmode=any&format=json';

  constructor(http){
    this.http = http;
  }

  activate(){
    return this.http.jsonp(this.url).then(response => {
      this.images = response.content.items;
    });
  }

  canDeactivate(){
    return confirm('Are you sure you want to leave?');
  }
}
```

ここにはクールなものがたくさんあります。それでは最初から見ていきましょう。Aureliaから `HttpClient` をimportしています。これにより、非常に簡単な方法でHTTPリクエストを行うことができます。しかし、これはデフォルトのAureliaの設定には含まれていないため、パッケージをインストールする必要があります。このために、コンソールで次のコマンドを実行します:

```shell
jspm install aurelia-http-client
```

統合されたパッケージマネージャとローダーの能力をわかっていただけたのではないでしょうか。jspmのパッケージをインストールした後、まったく同じ識別子を使用してコードでそれをimportすればよいのです。この方法でGitHubか、NPMからなんでもインストールすることができます。

前と同じように、DIを利用して、ビューモデルに `HttpClient` インスタンスを持つようにしています。しかし、 _activate_ メソッドまでは、それを使いません。え、それがどんな意味をもつのかって? 今から説明します...

Aureliaのルータは、ルートが変更されるたびにビュー·モデルのライフサイクルを実施します。これは、"画面初期化ライフサイクル"と呼ばれるものです。ビュー·モデルは、ルートに入ったり出たりする際のコントロールのために、ライフサイクルのさまざまな部分にフックを設定することができます。ルートをアクティブにする準備ができたら、ルーターは、もし存在していれば、 `activate` フックを呼び出します。上記のコードでは、 FlickrのAPIを呼び出して、いくつかの画像を得るために、このフックを使用しています。 `activate` メソッドがのHTTPクライアントのリクエスト結果を返すことに注意してください。すべての `HttpClient`  APIは、 `Promise` を返します。ルータは `Promise` を検出し、それが解消するまで遷移を完了するのを待機します。この方法を使って、必要に応じて、データが入ってくるまでページを表示遅らせることをルータに強制できます。

`canDeactivate` :ここに第二のライフサイクルのフックの例があります。ルートから遷移して他に移る際にルーターはこれを呼び出します。ここで真偽値を返すことでそのまま遷移するか、しないかを制御することができます。また、返値として `Promise` を返すことができます。完全なライフサイクルは `canActivate`  、 `activate` 、 `canDeactivate` と `deactivate` フックが含まれています。

> **注:** [Promise](http://www.html5rocks.com/en/tutorials/es6/promises/) に詳しくない方へ。これらは非同期プログラミングを改善するために設計されたES6の新機能です。 `Promise` は将来の結果を表すオブジェクトです。基本的には、それが将来のある時点で作業を完了して、何らかのデータを提供することを"約束"していることを表しています。

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

この画面のビューはかなり単純です。非常にタブーとされているインラインスタイルの素晴らしい使用を含め、前に見たことがないものは何もありません。（他の方法でみても...何もないです。）

すべてが揃ったら、アプリケーションを実行します。これで、ナビゲーションバーで2つの項目を表示し、それらの間を行ったり来たり切り替えることができるはずです。バンザイ!

今までの手順を要約します。ページを追加するには:

1. _app.js_ ルーターにルート設定を追加する
2. ビューモデルを追加する
3. 同じ名前(拡張子は.html)でビューを追加する
4. お祝いする

## ボーナス: カスタムエレメントを作成する

見てください。すごい成果ですよ!この際もっと素晴らしい機能を学んで見たいと思っていることでしょう。ここでは、カスタムHTMLエレメントを作ってみることにしましょう。カスタムHTMLエレメントの候補は先ほどのnavbarが良さそうです。これは _app.html_ ファイルのほとんどのHTMLです。カスタム `<nav-bar>` を作って、もっと宣言的にしてみることにしましょう。最終的には以下のように書きたいと思っています:

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

このコードでは、"./nav-bar" から `nav-bar` 要素をインポートし、それがビューで使用可能になると、カスタムプロパティへのデータバインディングを含め、他の要素と同じように使用することができます。( _router_ のように)それで、どのようにしたらこの最終状態に持っていけるでしょうか?

どうすればいいと思いますか?私たちの単純なビューモデル/ビューの規約は、カスタム要素にも適用されます。(実際には、あなたが今まで作ってきたのは、私たちが"匿名"カスタムエレメントと呼ぶこともあるものです。最初から...意識していなかっただけで。)  _nav-bar.js_ と _nav-bar.html_ を作成しましょう。まずビューモデルのコードをお見せします:

### nav-bar.js

```javascript
import {bindable} from 'aurelia-framework';

export class NavBar {
  @bindable router = null;
}
```

カスタムエレメントを作るために、クラスを作ってexportします。このクラスはHTML内で要素として使用されるので、フレームワークにどのプロパティがエレメントの属性として表れるのかを伝える必要があります。そのためには、 _bindable_ デコレータを使います。以前の _inject_ の例のように、 _bindable_ はAureliaフレームワークにクラスの情報を提供する手段です。Aureliaは賢く、多くのことを推察してくれますが、それができないとき、またはあなたがフレームワークの規約とは異なることをしたいときは、デコレータを通じて追加の情報を提供します。 `bindable` デコレータは、このクラスの `router` プロパティをHTML中で属性として表したいということをフレームワークに伝えます。このようにしておくことで、ビューの中でバインドすることができます。

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

これは元の _app.html_ とほぼ同じに見えます。基本的に元のファイルから抜き出してこのテンプレートに入れただけです。 _app.js_ とバインドする代わりに、これは _nav-bar.js_ とバインドします。

これはとても単純なカスタムエレメントで、振る舞いは何もありませんが、上で見るように、完全で利用可能なものです。あなたの便宜のため、再度マークアップを掲載します。

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

要約すると: 最初に要素を `import` します。Aureliaはカスタムエレメントを `from` 属性の場所にある関連するソースからロードします。 Aureliaの単純な規約に従っているので、フレームワークはどうやって _nav-bar.js_ と _nav-bar.html_ ファイルをロードすればよいかわかっています。このようなやり方でビューにimportしたものはすべて、ビューにローカルなものになります。そのため、名前の衝突を気にする必要はありません。2番目のポイントは、 `App` クラスのルーターをデータバインドするような要素の実際の使い方です。 `App` クラスにあるルーターインスタンスを `NavBar` の対応する属性を通じて引き渡し、内部でレンダリングに使っています。すごい!

> **注:** 利便性のため、アプリケーション全体に適用される要素やその他の振る舞いを読み込むことができます。そのため、すべてのビューで共通のリソースをインポートする必要はありません。

あなたはAureliaがどうやってカスタムエレメントの名前を決めているか、不思議に思うかもしれません。規約では、クラス名を小文字にしてハイフンでつなげたものを使います。けれども、常に明示的に指定することもできます。明示的に指定するには、 `Behavior` に `.customElement('nav-bar')` のような呼び出しをチェインして、メタデータを追加します。カスタムエレメントが全部コードで実装されていて、ビューテンプレートを持たない場合はどうすればいいですか?問題無いです。 `.noView()` をチェインしてください。カスタムエレメントにShadowDOMが使いたい場合は?プロのように `.useShadowDOM()` をチェインしてください。ブラウザがサポートするかどうかを気にする必要はないです。我々は効率的で、忠実なShadowDOMのフォールバック実装を持っています。

カスタムエレメントを作ることに加え、既存のエレメントに新しい振る舞いを追加する、単独の属性を作ることができます。我々はこれらを _付加ビヘイビア_ と呼んでいます。以前使った `repeat` や `for` のように、ビューに対して動的にDOMの追加・削除を行うようなとき、 _テンプレートコントローラー_ を作成する必要があるでしょう。あなたができることはこれだけではありません。Aureliaのテンプレートエンジンは、強力でかつ拡張性に富んでいます。

## ボーナス: 子ルーターを活用する

まだまだやれる?よろしい、あなたのためにご馳走をお持ちしました。3番目のページをアプリケーションに追加しましょう...そのページ固有のルーターもつけて!このルーターを子ルーターと呼びます。子ルータは、独自のルートの設定をもち、親ルータに対して相対的に遷移します。こんがらがらないように備えましょう...

はじめに、 _app.js_ を新しい設定で更新します。このようになります:

### app.js (再更新版)

```javascript
import {inject} from 'aurelia-framework';
import {Router} from 'aurelia-router';
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

@inject(Router)
export class App {
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['','welcome'],  moduleId: './welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',        moduleId: './flickr',       nav: true },
        { route: 'child-router',  moduleId: './child-router', nav: true, title:'Child Router' }
      ]);
    });
  }
}
```

ここには何も新しいことはありません。面白いのは _child-router.js_ の中にあります。

### child-router.js

```javascript
import {inject} from 'aurelia-framework';
import {Router} from 'aurelia-router';

@inject(Router)
export class ChildRouter{
  heading = 'Child Router';

  constructor(router){
    this.router = router;
    router.configure(config => {
      config.map([
        { route: ['','welcome'],  moduleId: './welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',        moduleId: './flickr',       nav: true },
        { route: 'child-router',  moduleId: './child-router', nav: true, title:'Child Router' }
      ]);
    });
  }
}
```

ええっ!?これは実質 `App` と同じ? 何? なぜ? うーん...あなたはおそらく実際にこのようなことをすることはありません...しかし、これはかなりクールです。再帰的なルーターで、こんなこともできるという例です。

仕上げに、これがビューです:

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

アプリケーションを動かして、魔法を見ましょう...そして世界が爆発しないことを祈りましょう。

## まとめ

Aureliaは開発者のエクスペリエンスを重視しているため、素晴らしいアプリケーションを作れるだけでなく、開発の過程を楽しめるようになっています。我々はシンプルな規約を念頭においてAureliaを設計しました。それにより、あなたは頭の固い、もしくは制限の多いフレームワークのために、大量の設定や定型コードを書く必要はありません。あなたはまた、Aureliaが邪魔に感じることもないでしょう。Aureliaはプラガブルで、カスタマイズできるように注意深く設計されています。

このガイドを読むために時間をとっていただき、ありがとうございます。私たちは、あなたがドキュメントを探索し、素晴らしいものを構築いただければ幸いです。私たちは、あなたが作るものを楽しみにしています。
