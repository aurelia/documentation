---
name: Criando Componentes
description: Componentes são os elementos básicos de todas as aplicações Aurelia. Neste artigo, você aprenderá como construir componentes básicos usando injeção de dependência e o ciclo de vida dos componentes.
author: Rob Eisenberg (http://robeisenberg.com)
---
## Criando um Componente

No Aurelia, os componentes de interface do usuário são compostos por pares de _view_ e _view-model_. A _view_ é escrita com HTML e é renderizada no DOM. O _view-model_ é escrito com ${context.language.name} e fornece dados e comportamento para a _view_. O mecanismo de modelos (_templating_) e a injeção de dependência são responsáveis por criar esses pares e impor um ciclo de vida previsível para o componente. Uma vez instanciado, o poderoso _databinding_ do Aurelia liga as duas partes, permitindo que as alterações no seu _view-model_ sejam refletidas na _view_ e as alterações na sua _view_ sejam refletidas em seu _view-model_. Esta "Separação de Interesses" é excelente para colaboração de desenvolvedores/designers, capacidade de manutenção, flexibilidade arquitetural e até mesmo controle de versões.

Para criar um componente de UI, você só precisa criar dois arquivos, um para cada uma das partes do componente. Vamos criar um simples componente "Hello". Para isso, precisaremos de um _hello${context.language.fileExtension}_ para nosso  _view-model_ e _hello.html_ para nossa _view_. A seguir está a código para cada um:

<code-listing heading="hello${context.language.fileExtension}">
  <source-code lang="ES 2016">
    export class Hello {
      firstName = 'John';
      lastName = 'Doe';

      sayHello() {
        alert(`Hello ${this.firstName} ${this.lastName}. Nice to meet you.`);
      }
    }
  </source-code>
  <source-code lang="ES 2015">
    export class Hello {
      constructor() {
        this.firstName = 'John';
        this.lastName = 'Doe';
      }

      sayHello() {
        alert(`Hello ${this.firstName} ${this.lastName}. Nice to meet you.`);
      }
    }
  </source-code>
  <source-code lang="TypeScript">
    export class Hello {
      firstName: string = 'John';
      lastName: string = 'Doe';

      sayHello() {
        alert(`Hello ${this.firstName} ${this.lastName}. Nice to meet you.`);
      }
    }
  </source-code>
</code-listing>

<code-listing heading="hello.html">
  <source-code lang="HTML">
    <template>
      <input value.bind="firstName">
      <input value.bind="lastName">

      <button click.trigger="sayHello()">Say Hello</button>
    </template>
  </source-code>
</code-listing>

Observe que o _view-model_ é uma classe simples. Não há nada de extraordinário nele. Um dos pontos fortes do Aurelia é que você pode escrever a maior parte de seu aplicativo com Javascript puro.

Além disso, observe como a _view_ é agrupada em um _HTMLTemplateElement_ da especificação de _Web Components_. Todas as _Views_ usam modelos HTML baseados em padrões. Você também pode ver a linguagem de vinculação muito simples e fácil de lembrar. Basta anexar `.bind` a qualquer atributo HTML no DOM e o Aurelia irá vinculá-lo à propriedade correspondente em seu _view-model_.

O comando de vinculação `.bind` configura o "comportamento de vinculação padrão" para o atributo. Para a maioria dos atributos, essa é uma vinculação unidirecional (_`one-way` binding_), em que as atualizações de dados fluem apenas em uma direção: do _view-model_ para a _view_. No entanto, geralmente, o comportamento que você deseja para os controles de formulário é a vinculação bidirecional (_`two-way` binding_) para que os dados não fluam apenas do seu _view-model_ para sua _view_, mas também a entrada do usuário na _view_ flua de volta para o _view-model_.

Esses são os comportamentos padrão, mas você sempre pode ser explícito sobre a direção da vinculação usando os comandos `.one-way`,` two-way` ou `.one-time` no lugar de `.bind` (`.one-time` renderiza o valor inicial da propriedade, mas não executa a sincronização posterior, fazendo com que haja um bom ganho de memória e desempenho para os dados que você sabe que não serão alterados).

Além de vincular atributos HTML, você também pode vincular eventos. Qualquer evento, nativo ou personalizado, pode ser vinculado usando o `.trigger`, o que faz com que a expressão seja invocada quando o evento indicado é disparado.

> Info
> Você pode ler mais sobre vinculação de dados (_data binding_) nos vários artigos sobre o assunto.

Agora você sabe como construir componentes básicos. O que é ótimo sobre esse conhecimento? Ele é consistente por todo o Aurelia. O mesmo padrão acima é usado para criar o componente raiz do seu aplicativo, as telas para as quais o roteador navega, elementos personalizados, componentes de UI compostos dinamicamente, diálogos modais etc.

## Instanciação de Componentes por Injeção de Dependência (DI)

_View-models_ e outros elementos de interface, como Elementos Customizados e Atributos Personalizados, são criados como classes que são instanciadas pelo _framework_ usando um contêiner de injeção de dependência. O código escrito nesse estilo é fácil de modularizar e testar. Em vez de criar classes grandes, você pode dividir as coisas em pequenos objetos que colaboram para atingir um objetivo. A DI pode então juntar as peças para você em tempo de execução.

Para aproveitar dos revursos da DI, você simplesmente decora sua classe para dizer ao _framework_ o que deve passar para seu construtor. Aqui está um exemplo de um componente "CustomerDetail" que depende do cliente HTTP do Aurelia.

<code-listing heading="customer-detail${context.language.fileExtension}">
  <source-code lang="ES 2016">
    import {inject} from 'aurelia-framework';
    import {HttpClient} from 'aurelia-fetch-client';

    @inject(HttpClient)
    export class CustomerDetail {
      constructor(http) {
        this.http = http;
      }
    }
  </source-code>
  <source-code lang="ES 2015">
    import {HttpClient} from 'aurelia-fetch-client';

    export class CustomerDetail {
      static inject() { return [HttpClient] };

      constructor(http) {
        this.http = http;
      }
    }
  </source-code>
  <source-code lang="TypeScript">
    import {autoinject} from 'aurelia-framework';
    import {HttpClient} from 'aurelia-fetch-client';

    @autoinject
    export class CustomerDetail {
      constructor(private http: HttpClient) {}
    }
  </source-code>
</code-listing>

* Se você estiver usando o ES2016, use o decorador `inject`. Ele deve passar uma lista de tipos para fornecer as instâncias. Deve haver um argumento para cada parâmetro do construtor. No exemplo acima, precisávamos de uma instância HttpClient, então adicionamos o tipo `HttpClient` no decorador` inject` e adicionamos um parâmetro correspondente no construtor.
* Se você está seguindo o ES2015, ou não quer usar decoradores, você também pode adicionar um método `inject` estático à classe que retorna um _array_ de tipos para injetar.
* Se você estiver usando o TypeScript >= 1.5, você pode adicionar o decorador `@autoinject` à sua classe e deixar de fora os tipos na chamada do decorador, mas apenas usá-los na assinatura do construtor.

## The Ciclo de Vida dos Componentes

Todos os componentes possuem um ciclo de vida bem definido. Abaixo está uma lista de métodos que você pode implementar em seu _view-model_ para conectar-se ao ciclo de vida do componente:

1. `constructor()` - O construtor do _view-model_ é chamado primeiro.
2. `created(owningView: View, myView: View)` - Se o _view-model_ implementar a função de retorno `created`, ele será chamado a seguir. Neste momento, a _view_ também foi criada e tanto o _view-model_ quanto a _view_ estão conectados ao seu controlador. A função de retorno _created_ receberá a instância da "owningView". Esta é a _view_ na qual componente está declarado dentro. Se o próprio componente tiver uma _view_, isso será passado em segundo.
3. `bind(bindingContext: Object, overrideContext: Object)` - A vinculação de dados é então ativada na _view_ e no _view-model_. Se o _view-model_ tiver uma função de retorno `bind`, ela será chamada neste momento. O "contexto de vinculação" ao qual o componente está sendo vinculado será transmitido primeiro. Um "contexto de substituição" será passado em segundo. O contexto de substituição contém informações usadas para percorrer a hierarquia pai e também pode ser usado para adicionar quaisquer propriedades contextuais que o componente queira adicionar.
4. `attached()` - Em seguida, o componente é anexado ao DOM (no documento). Se o _view-model_ tiver umafunçao de retorno `attached`, ela será chamada neste momento.
5. `detached()` - Se definido em seu _view-model_ - é chamado depois que o componente foi removido do DOM. Devido a navegação ou outras razões.
6. `unbind()` - Depois que um componente é desanexado, geralmente é desvinculado. Se o seu _view-model_ tiver a função de retorno `unbind`, ela será invocada durante este processo.

Cada um dessas funções de retorno (_callbacks_) é opcional. Implemente o que quer que faça sentido para o seu componente, mas não se sinta obrigado a implementar nenhuma delas se elas não forem necessárias para o seu cenário. Normalmente, se você implementar `bind`, precisará implementar o `unbind`. O mesmo vale para `attached` e `detached`, mas, novamente, isso não é obrigatório.

A ordem em que as funções do ciclo de vida são listadas acima corresponde à ordem em que são invocadas. Por exemplo, `bind` acontece antes de `attached` para garantir que os elementos tomem seu estado inicial do _view-model_ antes que a _view_ seja anexada ao DOM e feita a transição. Da mesma forma, `detached` acontece antes de `unbind` para garantir que a _view_ seja transicionada e desanexada do DOM antes de `unbind` potencialmente caude uma mudança na _view_.

> Info
> É importante notar que, se você implementar a função de retorno `bind`, então as funções de retorno _property changed_ de qualquer propriedade `bindable` não será chamada quando o valor da propriedade for inicialmente definido. A função de retorno _changed_ será chamada a qualquer momento subsequente em que o valor vinculado seja alterado.
