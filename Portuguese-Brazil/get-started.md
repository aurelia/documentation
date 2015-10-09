# Iniciando
**Bem-vindo ao Aurelia!** Este tutorial irá guia-lo através da criação de um aplicativo simples usando Aurelia e explicar brevemente os seus principais conceitos. Estamos supondo que você já esteja familiarizado com JavaScript, HTML e CSS. Para ter uma visão geral, recomendamos que você vá até a seção "Configuração da página HTML" para que você possa ver como usar Aurelia imediatamente. Então, quando você estiver pronto para realmente construir algo, volte e leia a seção "Configurando Seu Ambiente" e "Configurando a Estrutura do Projeto". Para ver os resultados completos de este tutorial, por favor, dê uma olhada no [nosso projeto esqueleto de navegação] (https://github.com/aurelia/skeleton-navigation/releases).

> **Note:** Looking for this guide in another language? Have a look in our [documentation repo](https://github.com/aurelia/documentation).

## Configurando Seu Ambiente
Vamos começar configurando um grande conjunto de ferramentas que você pode usar para construir aplicações modernas em JavaScript. Todas as ferramentas que usaremos são construídas sobre [Node.js] (http://nodejs.org/). Se você já o tiver instalado, ótimo! Se não, você deve ir ao [site oficial] (http://nodejs.org/), faça o download e instale-o. Todo o resto que iremos precisar será instalado via gerenciador de pacotes do Node ([npm] (https://docs.npmjs.com/getting-started/what-is-npm)). Se você já tiver o npm instalado, certifique-se que você tem a [última versão] (https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) para evitar quaisquer problemas com as outras ferramentas.

Primeiro, vamos começar instalando o [Gulp] (http://gulpjs.com/) que iremos usar para automação de compilação. Se você ainda não o tem, vamos utilizar o npm para instala-lo, usando os comandos abaixo (pode ser que você precise usar `sudo`):

  ```shell
  npm install -g gulp
  ```

Em seguida, é preciso instalar o [jspm] (http://jspm.io/). Ele irá servir como o nosso gerenciador de pacotes client-side. Você pode instala-lo desta forma:
  ```shell
  npm install -g jspm
  ```

> **Nota:** jspm, assim como Bower e Yeoman, funcionam via [git] (http://git-scm.com/), isso quer dizer você deve instala-lo caso ainda não o tenha. Além disso, o jspm consulta o GitHub para instalar pacotes, mas o GitHub tem um limite de pedidos anônimos na API. É aconselhável que você configure o jspm utilizando suas credenciais GitHub a fim de evitar problemas. Você pode fazer isso executando o comando `jspm registry config github` e então seguir as instruções. Você também pode autorizar o jspm através de um token, caso não queira utilizar suas credenciais GitHub. Para isso, no GitHub, navegue até `Settings > Personal Access Tokens`, e crie um token com o escopo `public_repo`.
>
> **Nota:** Não quer usar o jspm? Sem problemas! Todos os pacotes do Aurelia estão disponíveis através do [Bower] (http://bower.io/) também.

## Configurando a Estrutura do Projeto

Com o conjunto de ferramentas instalada, podemos voltar a nossa atenção para a criação de uma estrutura básica para seu aplicativo. Comece [baixando o esqueleto de navegação] (https://github.com/aurelia/skeleton-navigation/releases). Descompacte-o e mude o nome da pasta para _navigation-app_.

> **Nota:** Como alternativa, você pode usar o [Yeoman] (http://yeoman.io) para "gerar" o projeto esqueleto de navegação na pasta de destino, desta forma:
>
> ```
> npm install -g yo generator-aurelia
> yo aurelia
> ```
>
> Isto também irá executar os comandos npm e jspm listados abaixo.

Dentro da pasta você vai encontrar tudo que você irá precisar, incluindo uma compilação base, configuração de pacotes, estilos e muito mais.

Você pode verificar que foi lhe foi fornecido um arquivo _index.html_ e alguns outros arquivos dentro da pasta _src_ e _dist_, no entanto, recomendamos que você exclua o arquivo **index.html**, as pastas **src** e **dist**, para que então possa avançar com este tutorial. Dessa forma, você aprenderá de uma forma eficaz como construir um aplicativo do zero usando o Aurelia.

Com tudo o que precisamos pronto, vamos executar alguns comandos.

1. Abra um console e navegue até o diretório da pasta _navigation-app_ que criamos recentemente.
2. Execute o seguinte comando para instalar os plugins Gulp que estão listados na seção _devDependencies_ do arquivo de manifesto do pacote.

  ```shell
  npm install
  ```

3. Em seguida, execute o seguinte comando para instalar a biblioteca do Aurelia, do bootstrap e  do font-awesome, listados na seção _jspm.dependencies_ de arquivo de manifesto do pacote.

  ```shell
  jspm install -y
  ```

Tudo o que fizemos até agora é puro Node.js, procedimentos de construlçao e de gerenciamento de pacotes. Ele não tem nada específico para com o Aurelia. Estamos apenas exibindo os passos da criação de um projeto moderno em JavaScript, a partir do zero. Você pode já estar familiarizado com isso, mas caso ainda não esteja, seja bem-vindo a este mundo novo e excitante!

> **Nota:** Bootstrap e Font-Awesome **não** são dependências do Aurelia. Nós só iremos aproveitá-los neste tutorial para poder produzir rapidamente um visual decente para aplicação.


## Construindo a Página HTML

Se você chegou até aqui, você tem todas as bibliotecas e ferramentas de construção que iremos utilizar para criar um incrível aplicativo em JavaScript com Aurelia. A próxima coisa a ser feita é criar o arquivo _index.html_ na **pasta raiz do projeto**. O exemplo abaixo fornece um bom modelo para os aplicativos baseados em Aurelia.

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

Sim, é isso! Esta é a única página HTML em nossa aplicação. O cabeçalho é bastante simples. Agora, é o corpo da página que é interessante.

Vamos começar com as tags script. Primeiro temos o _system.js_, nosso carregador de módulos baseado em padrões do ES6. É ele quem carrega a biblioteca do Aurelia, bem como o seu próprio código. Em seguida, temos o _config.js_. Ele contém a configuração para o carregador. É gerado automaticamente sempre que você executar um comando jspm. O jspm é o gerenciador de pacotes client-side, é recomendável que você o utilize porque ele fornece uma experiência de desenvolvimento incrível através da integração de gerenciamento de pacotes client-side com um módulo compatível com o carregador do ES6.

> **Nota:** O Aurelia Framework não está vinculado diretamente ao jspm ou SystemJS. Ele também oferece suporte a APIs require-style como RequireJS e Dojo Loader. Além disso, você pode implementar seu próprio carregador e lidar com o gerenciamento de pacotes do jeito que quiser. No entanto, achamos que o jspm / SystemJS é a melhor solução orientada a ES6 disponível hoje, e é a nossa abordagem recomendada.

Uma vez que temos o nosso carregador de módulos e sua configuração, nós carregamos o módulo `aurelia-bootstrapper` com uma chamada através da função `System.import`.

Quando o bootstrapper é carregado ele inspeciona o documento HTML procurando por atributos _aurelia-app_. Neste caso, ele vai identificar que o body tem um atributo `aurelia-app`. Isso informa ao bootstrapper para carregar nossa view-model e sua view, convencionalmente localizado nos arquivos _app.js_ e _app.html_ e depois utilizá-los como uma aplicação Aurelia no DOM.

Espere um minuto .... Nós não temos um _app_ view-model ou view. Hmmm ...  E AGORA !?

## Criando Sua Primeira Tela

No Aurelia, elementos de interface do usuário são compostos por pares de _view_ e _view-model_. A _view_ é escrita em HTML e é processada no DOM. A _view-model_ é escrita em JavaScript e fornece dados e comportamento para a _view_. O Poderoso _databinding_ do Aurélia liga os dois pedaços, permitindo que as mudanças em seus dados sejam refletidas na _view_ e vice-versa. Esta separação de interesses é de grande importância para a colaboração desenvolvedor / projetista, manutenção, flexibilidade de arquitetura e até mesmo do código-fonte.

Vamos dar uma olhada em como isso funciona...

Na raiz do project, crie uma pasta chamada _src_, dentro dela crie um arquivo _app.html_ e um _app.js_. Esta é a view e view-model que o bootstrapper estava procurando. Vamos começar com a _view-model_ criando uma classe simples para manter um _firstName_ e _lastName_. Nós também vamos adicionar uma propriedade calculada para _fullName_ e um método de "submit", que irá enviar a pessoa. Nossa view-model ficará da seguinte forma:

### app.js

```javascript
export class Welcome{
  heading = 'Welcome to the Aurelia Navigation App!';
  firstName = 'John';
  lastName = 'Doe';

  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }

  submit(){
    alert(`Welcome, ${this.fullName}!`);
  }
}
```

O que ... isso aí é JavaScript mesmo?

Sim, isso é JavaScript. Na verdade, é ECMAScript 2016 (formalmente ES7), a próxima versão do JavaScript que introduz muitos recursos novos para a linguagem. Felizmente o arquivo do Gulp que você baixou está configurado com o [Babel] (https://babeljs.io/), um transpilador incrível que lhe permite escrever usando o JavaScript do futuro e executá-lo em navegadores atuais. Agora você pode usar módulos, classes, lambdas, interpolação de string e muito mais. Muito massa não é mesmo!? Então, como você cria uma _view-model_? Você cria uma simples classe usando _export_ para que fique acessível ao framework. Mel na chupeta!

> **Nota:** Você não tem que necessariamente usar o Babel ou mesmo ES2016 para desenvolver um aplicativo usando o Aurelia. Você pode usar linguagens como TypeSript e CoffeeScript ... ou a linguagem dos navegadores de hoje: ES5 e ES2015. Tudo que você tem a fazer é seguir o padrão da linguagem para a criação de classes e tudo irá funcionar. Nós achamos o ES7 incrível, e esperamos que você o considere como primeira opção. Para saber mais sobre esta nova versão do JavaScript incluindo recursos como exportações e classes, recomendamos a leitura do seguinte artigo: [The Babel Learning Guide] (http://babeljs.io/docs/learn-es6/).
Ok. Agora que temos nossa _view-model_ com alguns dados e comportamentos básicos, vamos dar uma olhada na sua parceira de crimes... a _view_.

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

Todas as views estão contidas dentro da tag `template`, seguindo a especificação da W3C para Web Components. Esta view é um formulário básico. Dê uma olhada nos elementos do formulário. Você reparou no `value.bind="firstName"`? Este comando faz um bind (vínculo) entre o _value_ do elemento input e a propriedade _firstName_ da nossa view-model. Toda vez que o valor da propriedade da view-model for atualizado, o elemento em que foi vinculado também será atualizado, e vice-versa. Fácil, não?

Existem outras coisas mais interessantes neste exemplo. No último grupo do formulário você pode ver esta sintaxe no conteúdo HTML: `${fullname}`. Essa é uma interpolação de string. É um "one-way binding" da view-model para a view, que é automaticamente convertido para uma string e interpolada no documento. Finalmente, dê uma olhada no elemento form do formulário. Você deve ter observado o: `submit.delegate="submit()"`. Isto é um binding de evento. Ele utiliza uma técnica chamada "event delegation" para vincular o evento _submit_ para que este execute o método _welcome_ sempre que o formulário é enviado.

> **Nota:** Se você ainda não ouviu falar de "event delegation" (delegação de eventos), é uma técnica utilizada para lidar com eventos de forma mais eficiente, anexando um único manipulador de eventos ao um nível pai, ao invés de anexar um manipulador para cada elemento filho.

Bora executar e ver tudo isso em ação. No console do seu computador, use o seguinte comando para realizar um build e inicializar o servidor.

```shell
gulp watch
```

Agora navegue em [http://localhost:9000/] (http: //localhost:9000/) para ver a nossa aplicação. Preencha o formulário e observe que o FullName é atualizado sempre que os outros campos são alterados. Clique no botão Submit e veja que o nosso método será disparado.

> **Nota:** Se não estiver funcionando, tente [atualizar] (https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) para a última versão do NPM.
>
> **Nota:** O Aurelia tem uma engine de databinding única e poderosa, que utiliza técnicas adaptativas para escolher a melhor maneira de observar as mudanças em cada propriedade. Por exemplo, se você estiver usando um navegador com suporte ao [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe), o _firstName_  e o _lastName_ serão observados com essa estratégia. Se não, nós iremos gerar getters e setters que loteiam mudanças para o Micro Task Queue, emulando corretamente comportamento do Object.observe. Uma vez que a propriedade _fullName_ é calculada, ela não pode ser observada com qualquer uma destas técnicas, neste caso nós usamos uma técnica chamada "dirty checking". Mas, você pode, opcionalmente, declarar suas dependências, a fim de permitir-nos a observá-la corretamente. Nós usaremos a melhor técnica dependendo da situação e você pode até mesmo criar técnicas personalizadas, a fim de "ensinar" o framework como observar tipos especiais de padrões de modelagem. Nós achamos este recurso muito legal :)
>
> **Nota:** O comando `.bind` usa o comportamento de bind padrão para qualquer propriedade. O padrão é "one-way binding" (model para view) para tudo, exceto os controles de formulário, que padrão é "two-way binding". Você sempre poderá substituir isso usando os comandos de bind explícitos `.one-way`, `.two-way` e `.one-time`. Da mesma forma, você pode usar `.delegate` para delegação evento, mas você também pode usar `.trigger` para anexar diretamente para o elemento desejado.


## Adicionando Navegação

Como este é um aplicativo de navegação, que provavelmente deve adicionar mais algumas telas e configurar um client-side router, não acha? Vamos começar por renomear o nosso _app.js_ e _app.html_ para _welcome.js_ e _welcome.html_ respectivamente. Esta será a primeira página de nosso aplicativo. Agora, vamos criar um novo _app.js_ e _app.html_ que servirá como nosso "layout" ou "master-page". A view irá conter nossa UI de navegação e um espaço reservado de conteúdo para a página atual e a view-model terá uma instância do router, configurado com nossas rotas. Vamos começar com a view-model para que você possa ver como configurar o router:

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

Opa! Há algumas coisas realmente interessantes aqui. Primeiro, nós vamos usar alguns componentes de navegação do bootstrap na próxima etapa, então devemos usar o sistema de módulos do ES6 para importar o bootstrap, logo, ele estará disponível para ser utilizado. Em seguida, queremos usar o router, por isso, começamos por criar e exportar a nossa classe _App_ implementando callback do `configureRouter`. Este callback será chamado com um objeto de configuração. Com ele você pode definir um título para página, e então mapear suas rotas. Cada rota tem as seguintes propriedades:

* `route`: Este é um padrão que, quando combinado, fará com que o router navegue para essa rota. Você pode usar rotas estáticas como acima, mas você também pode usar parâmetros como: `customer/:id`. Há também suporte para rotas curingas e parâmetros query string. A rota pode ser um padrão de string única ou uma série de padrões.
* `name`: Este é um nome a ser usado no código ao gerar URLs para a rota.
* `moduleId`: Este é um caminho relativo para a view-model atual que especifica o par de view/view-model que você deseja processar para essa rota.
* `title`: Opcionalmente, é possível fornecer um título para ser usado na geração de título da página.
* `nav`: Se essa rota deve ser inclusa no menu de navegação, defina este valor como "true" (ou um número que indica ordem de aparição);

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

Seguindo nossa simples convenção de construção do aplicativo, a classe `App` será a ligação de dados para a view acima em _app.html_. Uma grande parte dessa marcação lida com a configuração da estrutura de navegação principal. Mas antes de avançarmos, você viu os elementos `require` no topo da view? Da mesma maneira que você pode usar instruções `import` no ES2015, o Aurelia permite que você use elementos `require` no HTML. O elemento `require` ativa o carregamento e inclusão de funcionalidades, como elementos customizados e comportamentos, neste caso, CSS. Aqui, como nossa aplicação irá usar alguns estilos do bootstrap e icons do font-awesome, nós "requerimos" estes arquivos CSSs. Com isto no lugar, podemos utilizar o boostrap para estilizar nossa estrutura de navegação, conforme abaixo. Mas esta não é a parte interessante desta view. O que nós realmente queremos focar é nos bindings e elementos customizados...

Levando em conta que você já viu a interpolação de string, vamos focar nas coisas novas. De uma olhada no element ul navbar-nav. Seu li demonstra como usar um repetidor utilizando a expressão `repeat.for="row of router.navigation"`. Isto irá criar um li para cada item no array `router.navigation`. A variável local é row e você pode ver que usamos por todo li e seus elementos filhos.

> **Nota:** A propriedade `navigation` no router é um array preenchido com todas as rotas marcadas como `nav:true` na sua configuração. A sintaxe `repeat.for` do Aurelia funciona como o `for..of` do padrão ES2015. Assim, você pode pensar em um loop sobre a array de rotas, gerando o código necessário para cada uma delas.

Ainda sobre os `li` você pode ver uma demonstração de como usar interpolação de string para adicionar/remover dinamicamente as classes CSSs. Mais abaixo, há uma segunda `ul`. Viu o binding em sua única filha `li`? `if.bind="router.isNavigating"` Isto condicionalmente adiciona/remove o `li` com base no valor da expressão. 
Convenientemente, o roteador irá atualizar sua propriedade `isNavigating` sempre que estiver no processo de carregamento da rota.

A última que queremos falar é o elemento `router-view` perto da parte inferior da view. Este representa o local no DOM onde a "página" atual será processada, com base no estado do router configurado.

Com tudo isso em mente, vá em frente e inicie o servidor com o comando `gulp watch`. Abra o navegador e dê uma olhada. Você deve ver agora uma navegação principal com uma única guia selecionada para a nossa rota "welcome". A view _welcome_ é exibida na área de conteúdo e funciona da mesma forma como antes. Abra as ferramentas de debug do navegador e dê uma olhada no DOM atual. Você vai ver que a view _welcome_ é exibida dentro do `router-view`.

> **Nota:** Se você deixou seu o gulp watch task em execução, você pode ter notado que o seu navegador atualiza automaticamente sempre que você realiza alguma alteração. Isso acontece graças ao `browser-sync`, que convenientemente configuramos para você como parte da configuração do gulp.


## Adicionando Uma Segunda Página

Bom, tecnicamente já temos uma aplicação de navegação agora..., mas ela não é tão interessante porque há apenas uma página. Vamos adicionar uma segunda página. Você consegue adivinhar como iremos fazer? Eu aposto que sim!

Vamos exibir alguns usuários do GitHub. Para fazer isso, primeiro iremos configurar nosso router para a suposta página:

### app.js (updated)

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

Se você pensou que precisaríamos criar um arquivo _users.js_ e _users.html_, você acertou! Aqui está a código-fonte que iremos usar:

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

Há um monte de coisas legais aqui. Vamos começar pelo início. Estamos importando o `HttpClient` do Aurelia. Isso nos permite fazer solicitações HTTP de uma forma muito simples. Ele não está incluído na configuração padrão do Aurelia, sendo assim, você precisa para instalar o pacote separadamente. Para fazer isso, execute o seguinte comando no console:

```shell
jspm install aurelia-fetch-client
```

Agora eu imagino que você esteja vendo o poder do gerenciador e carregador de pacotes. Você simplesmente instala um pacote com o jspm e depois o importa em seu código usando exatamente o mesmo identificador. Você pode instalar quase todas as coisas do GitHub ou NPM desta forma.

Agora, dê uma olhada no decorador `inject` do ES2016. O que isso faz? Bem, o Aurélia cria os componentes de interface do usuário conforme são necessários para processar a aplicação. Ele faz isso usando um container de [Dependency Injection] (http://en.wikipedia.org/wiki/Dependency_injection) (Injeção de Dependência) capaz de fornecer dependências do construtor, como por exemplo, o HttpClient. Como é que o sistema de DI sabe o que deve fornecer? Tudo que você tem a fazer é acrescentar o decorador do ES7 `inject` para sua classe, passando uma lista de "types" do qual o qual seu construtor necessita. Deve haver um "type" para cada parâmetro do construtor. No exemplo acima, nós precisávamos de uma instância do HttpClient, por isso, acrescentamos o type `HttpClient` no decorador `inject`, em seguida, adicionamos um parâmetro correspondente no construtor da classe.

> **Nota:** Se você não gosta de usar um decorador, neste caso, você também pode utilizar um método estático `inject` ou uma propriedade da classe que retorna um array de types que deverão ser injetados.

Se você estiver usando TypeScript >= 1.5, você pode adicionar o decorador `@autoinject` à sua classe sem precisar especificar os types necessários, mas os types deverão ser especificados na assinatura do construtor, desta forma:

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

O Router do Aurélia reinicia um ciclo de vida na view-model sempre que as rotas mudam. Isto é chamado de "Screen Activation Lifecycle". As view-models podem, opcionalmente, chamar métodos de dentro e fora da rota, controlando o fluxo de várias partes do ciclo de vida da aplicação. Quando o router estiver pronto, ele irá chamar o método `activate`, caso este esteja presente. No código acima, nós usamos este método para chamar a API do Flickr e obter algumas imagens. Observe que nós retornamos o resultado do pedido http através do método `activate`. Todos as APIs `HttpClient` retornam um `Promise`. O router irá detectar um `Promise` e esperar para concluir a navegação até que a consulta tenha retornado. Assim, desta forma você pode, opcionalmente, forçar o router a atrasar a exibição da página até que ela esteja preenchida com dados.

> **Nota:** O ciclo de vida completo inclui os métodos `canActivate`, `activate`, `canDeactivate` e `deactivate`. Os can* podem retornar um valor booleano (ou Promise booleana) para aceitar ou rejeitar a transição de uma página para a outra. 
>
> **Nota:** Se você não estiver familiarizado com [Promises] (http://www.html5rocks.com/en/tutorials/es6/promises/), este é um novo recurso do ES2015 que foi projetado para melhorar a programação assíncrona. A `Promise` é um objeto que representa um resultado futuro. Essencialmente, ele representa uma "promessa" para concluir algum trabalho ou para fornecer alguns dados em algum momento no futuro.

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

A view desta tela é bastante simples. Não há nada que você não tenha visto antes.

Assim que estiver tudo pronto, vá em frente e execute seu aplicativo novamente. Agora você deve ver dois itens na barra de navegação e ser capaz de alternar entre eles. Ihul!

Recapitulando... para adicionar uma página à sua aplicação:

1. Adicione a configuração de rota para o router no _app.js_.
2. Adicionar uma view-model.
3. Adicione uma view com o mesmo nome (mas com uma extensão .html).
4. Comemore \o/.

> **Nota:** Neste momento, vale a pena para você dar uma olhada no code esqueleto original destas telas. Você irá encontrar algumas outras coisas legais, como animações, binding de elementos, conversor de valores e atributos customizados. Você também irá ver uma forma alternativa de iniciar a framework e instalar um plugin de terceiro.

## Bonus: Criando Um Elemento Personalizado

Olhe para você, já é um vencedor por ter chegado até aqui! Vejo que você está interessado em aprender alguma coisa extra neste belo dia. Sendo assim, vamos criar um elemento HTML personalizado. Eu acho que um bom candidato para nossa barra de navegação. Ela é um monte de HTML em nosso arquivo _app.html_. Por que não criar um elemento personalizado `<nav-bar>` para tornar as coisas um pouco mais clara? Dê uma olhada no que nós queremos ser capazes de escrever no final:

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

Esse código faz a requisição um elemento `nav-bar`, do arquivo "./nav-bar". Uma vez que este estiver disponível na view, podemos usá-lo como qualquer outro elemento, incluindo bindings de dados para suas propriedades personalizadas (como _router_). Então, como é que chegaremos nesse produto final?

Já adivinhou? Nossas simples convenções view-model/view também se aplicam para os elementos personalizados. (Na verdade, neste tempo todo você foi criando o que às vezes chamamos de elementos personalizados "anônimos"... você simplesmente não percebeu.) Vamos criar um _nav-bar.js_ e um _nav-bar.html_. Primeiro, o código para a view-model:

### nav-bar.js

```javascript
import {bindable} from 'aurelia-framework';

export class NavBar {
  @bindable router = null;
}
```

Para criar um elemento personalizado, você cria e exporta uma classe. Uma vez que esta classe vai ser usada no HTML como um elemento, precisamos dizer ao framework quais propriedades da classe devem aparecer como atributos no elemento. Para fazer isso, usamos o decorador _bindable_. Como _inject_, _bindable_ é uma maneira de fornecer informações sobre a sua classe para a framework do Aurelia. O Aurelia é inteligente e pode fazer muitas coisas sozinho, mas quando ele não pode ou quando você quer fazer algo diferente de suas convenções, você tem que fornecer algumas informações adicionais através de decoradores. O decorador `bindable` diz ao framework que queremos que propriedade `router` da nossa classe seja atributo no HTML. Uma vez que a propriedade se torna um atributo, podemos utiliza-la na view.

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

Isto é praticamente idêntico ao HTML do navbar orginal que estava em nosso arquivo _app.html_. Nós basicamente extraímos o código e colocamos dentro de um "template". Ao invés de fazer o "bind" com o _app.js_, estamos fazendo no _nav-bar.js_. 

Este é um elemento personalizado muito simples com nenhum comportamento real, mas é completo e utilizável como mostrado acima. 

Espere! Eu sei o que você está pensando. Este elemento customizado é muito simples...é muito estranho ter que requerer uma classe JavaScript só para identificar uma simples propriedade router. Não podemos nos safar disso de alguma forma? Bem, a resposta é SIM!. Para elementos muitos simples, que não necessitam de comportamentos, apenas forneça a view que deve ser bindada ao conjunto de propriedades, assim poderemos omitir o arquivo JavaScript. Vamos ver como funciona.

Primeiro, delete o arquivo __nar-bar.js__. Em seguida, precisamos fazer uma mudança na __nav-bar.html__. No elemento `template`, nós podemos declarar propriedades "bindáveis" do nosso elemento, desta forma:

```markup
<template bindable="router">
  ...
</template>
```
We can have more than one property by separating them by commas. Finally, we need to update our app.html file so that the require element points to our html component. Here's what it should look like:

Nós podemos ter mais de uma propriedade, separadando elas por vírgulas. Finalmente, temos que atualizar nosso arquivo __app.html__ para que ele faça a requisição dos elementos para nosso componente HTML. Ele deverá ficar desta forma:

### app.html

```markup
<template>
  <require from="bootstrap/css/bootstrap.css"></require>
  <require from="font-awesome/css/font-awesome.css"></require>
  <require from='nav-bar.html'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

> **Nota:** Qualquer coisa requerida dentro da view com um elemento `require` é local para a view. Desta forma, você não precisa se preocupar com conflitos. Você também pode carregar elementos app-wide e outros comportamentos conforme for conveniente. Sendo assim, você não tem que requerer recursos comum em todas as views. Veja a documentação para mais informações.

Você deve estar pensando como o Aurelia determina o nome de elementos customizados. Por convenção, ele irá usar o nome da classe de exportação, com letras minúsculas e hífens. (No nosso cenário só de HTML, ele irá usar o nome do arquivo.) Entretando, você pode declarar de forma explícita. Para isso, adicione o decorador `@customElement('nav-bar')` em sua classe. E se o seu elemento customizado não possuir uma view, porque tudo está implementado no código? Sem problemas, adicione o decorador `@noView()`. Deseja usar `ShadowDOM` no seu elemento customizado? Faça como um profissional, utilizando o decorador `@useShadowDOM()`. Não se preocupe se o browser dará suporte ou não. Nós temos uma eficiente e completamente fiel implementação de um ShadowDOM fallback. Você pode ler mais sobre estes decoradores na seção "Custom Elements" da documentação.

Além da criação de elementos personalizados, você também pode criar atributos personalizados que adicionam novos comportamentos a elementos existentes. Nesta ocasião, você pode até precisar de um atributo para controlar dinamicamente os templates, adicionando e removendo DOM da view, como o `if` e `repeat` que usamos anteriormente. Você pode fazer tudo isso e muito mais com mecanismo de modelagem poderosa e extensível do Aurélia.

## Bonus: Trabalhando com Child Routers

Você ainda quer mais, não é mesmo? Bem, eu tenho uma surpresa para você. Vamos adicionar uma terceira página para a nossa aplicação ... com seu próprio router! Chamamos isso de um "child router" (roteador filho). Child Routers têm a sua própria configuração de rota e navegação em relação ao router pai. Prepare-se para ir a loucura...

Primeiro, vamos atualizar nosso __app.js__ com a nova configuração. Ele deve ficar desta forma:

### app.js (updated...again)

```javascript
import 'bootstrap';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'],  name: 'welcome',      moduleId: 'welcome',      nav: true, title:'Welcome' },
      { route: 'users',         name: 'users',        moduleId: 'users',        nav: true, title:'Github Users' },
      { route: 'child-router',  name: 'child-router', moduleId: 'child-router', nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Nada de novo até aí. A parte interessante é o __child-router.js__...

### child-router.js

```javascript
export class ChildRouter{
  heading = 'Child Router';

  configureRouter(config, router){
    config.map([
      { route: ['','welcome'],  name: 'welcome',       moduleId: 'welcome',       nav: true, title:'Welcome' },
      { route: 'users',         name: 'users',         moduleId: 'users',         nav: true, title:'Github Users' },
      { route: 'child-router',  name: 'child-router',  moduleId: 'child-router',  nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Mas o quê !? É praticamente a mesma configuração do router pai? O Quê? Por Quê? Bem ... você deve provavelmente nunca precisará fazer isso na vida real... mas o que vai acontecer aqui é algo muito legal. Isso, meus amigos, é um router recursivo, e nós estamos fazendo isso porque nós podemos.
Para completar, aqui está a view:

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

Rode a aplicação e veja a mágica... e ore para que o universo não exploda.

## Conclusão

Com sua forte ênfase na experiência do desenvolvedor, o Aurelia pode permitir que você crie não só aplicações surpreendentes, mas também aprecie o processo. Nós o projetamos com convenções simples em mente, de uma forma com que você não precise perder tempo com toneladas de configurações ou escrevendo código clichê apenas para satisfazer um framework teimoso ou restritivo. Você nunca irá atingir um obstáculo com o Aurelia. Ele foi cuidadosamente projetado para ser "pluggável" e personalizável.

Obrigado pelo seu tempo e por ler o nosso guia. Nós esperamos que você explore as documentações e construa algo incrível. Estamos ansiosos para ver o que você vai fazer.
