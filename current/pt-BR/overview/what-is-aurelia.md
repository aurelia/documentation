---
name: O que é Aurelia?
description: Aurelia é um moderno framework front-end para criar aplicações _web_, _mobile_ e _desktop_.
author: Rob Eisenberg (http://robeisenberg.com)
---
## O que é Aurelia?

Aurelia é uma coleção de módulos feita em JavaScript Moderno que, quando usados em conjunto, funcionam como uma plataforma poderosa para a criação de aplicações de navegador, desktop e móveis; de código aberto e construída sob os padrões _web_ abertos. 

Vamos entender isso melhor...

### Uma Coleção de Módulos em JavaScript Moderno

Em vez de ser um _framework_ monolítico, o Aurelia é dividido em uma coleção de módulos orientados à funcionalidade.
Exemplos de funcionalidades incluem: metadados, injeção de dependências, _binding_ (ligações), _templating_ (templates), _router_ (roteador), entre outros. Cada módulo é escrito usando ECMAScript (conhecido como JavaScript) ou TypeScript (um _superset_ do JavaScript que adiciona a checagem de tipos em tempo de compilação). Muitos desses módulos podem ser utilizados individualmente em qualquer tipo de projeto JavaScript, incluindo Node.js.

### Uma Plataforma Poderosa para a Construção de Aplicações

Apesar dos módulos Aurelia poderem ser usados para várias finalidades, sua real força se dá ao usá-los em conjunto como uma plataforma de aplicação _front-end_. Se seu objetivo final é criar experiências ricas e engajadoras, no mesmo nível ou até mesmo superando o que é encontrado em aplicações nativas modernas, Aurelia provê os meios. Através do seu modelo de componentes ricos, composição de intefaces dinâmicas (UI), roteamento e um conjunto extensivo de _plugins_, Aurelia provê um conjunto compreensivo de recursos e ferramentas para construir qualquer experiência _front-end_ que você sonhar, esteja você focando em _mobile_, _web_ ou _desktop_.

### Código Aberto

Com todo o poder e recursos que Aurelia oferece você pode esperar que exista um modelo de licensiamento caro ou que ele seja de código fechado, mas nada disso é verdade. Aurélia é gratuito e seu código é aberto sob [a Licensa MIT](http://opensource.org/licenses/MIT), uma licensa bastante permissiva utilizada hoje em dia por diversos projetos _web_ modernos. O seu _kit_ de iniciante e documentação estão disponíveis sob a licensa [Creative Commons 0](http://creativecommons.org/publicdomain/zero/1.0/legalcode). Há também [uma Licensa de Acordo do Contribuidor (CLA)](https://github.com/durandalproject/about/blob/master/CLA.md) para aqueles que desejam entrar no time principal e trabalhar no desenvolvimento do Aurelia. Por fim, isso significa que você pode utilizar Aurelia sem medo de repercussões legais e nós podemos construí-lo com a mesma confiança.

### Construído sob os Padões _Web_ Abertos

Aurelia não só é escrito em ECMAScript como também é projetada para fazer o uso cuidadoso do padrão DOM. Em vez de utilizar uma abstração custosa do DOM, implementar seu próprio _parser_ HTML customizado ou adotar extensões JavaScript específicas do _framework_, ele potencializa as APIs do DOM mais recentes para obter performance "nativa", eficiênca excepcional de memória, e tudo isso enquanto se mantem atualizado com as melhorias contínuas das plataformas dos navegadores. Além disso, por ter um modelo de componentes baseado em _W3C Web Components HTML Templates_ e _ShadowDOM_, Aurelia é duradouro e permite que você evolua a sua aplicação junto com os avanços dos padrões, evitando grandes reescritas de aplicação ou rotatividade de _frameworks_.

## Porque Escolher Aurelia?

Existem muitos _frameworks_ para se escolher hoje em dia. Nós acreditamos que Aurelia provê uma abordagem nova e empolgante para o desenvolvimento _front-end_ com poder e flexibilidade que não são alcançados pelas outras opções. Dito isso, nós reconhecemos que cada time e cada projeto tem necessidades diferentes. Você pode achar que Aurelia é a escolha certa para você se...

* **Você quer uma solução completa** - Aurelia provê recursos básicos como injeção de dependência, modelos (_templating_), roteamento e _pub/sub_, desse modo você não precisa juntar uma série de bibliotecas a fim de construir uma aplicação. Acima dessa base rica, Aurelia também provê vários _plugins_ adicionais para internacionalização, validação, caixas de diálogo modais, virtualização de UI e muito mais. Você também não tem que ficar remendando com várias ferramentas diferentes. Aurelia provê um CLI para gerar e construir os projetos, um _plugin_ para navegadores para facilitar a depuração assim como um _plugin_ para o VSCode. Mesmo assim, você não é obrigado a utilizar nada disso pois o _framework_ Aurelia é estruturado para permitir que você troque qualquer detalhe, mesmo o mecanismo de _templating/binding_, de modo a garantir o máximo de flexibilidade.
* **Você precisa de uma incrível velocidade de renderização e grande eficiência de memória** - Em medições de terceiros, como a do DB Monster, Aurelia renderiza mais rápido do que qualquer outro _framework_ da atualidade. Por conta da sua renderização em lotes e agrupamento de objetos observáveis, Aurelia também utiliza menos memória e causa menos trocas do GC (_Garbage Collector_) do que os outros _frameworks_.
* **Você precisa da segurança de um fluxo de dados unidirecional mas precisa da produtividade do _data-binding_** - Aurelia possui um sistema de _binding_ (ligação) baseado em observáveis que utiliza um fluxo de dados unidirecional por padrão, levar os dados do seu modelo para sua _view_ através de um mecanismo de lotes no DOM altamente eficiente. O _binding_ de mão dupla (_two-way_) também pode ser aproveitado em controles de formulários HTML, possibilitando maior produtividade para o desenvolvedor, sem sacrificar a segurança do fluxo de dados unidirecional ou do encapsulamento de componente.
* **Você deseja estabilidade de API no meio de um cenário turbulento de JavaScript** - Aurelia segue o Semver e trabalha duro para não fazer alterações significativas em suas APIs. Nós temos orgulho de dizer que continuamos a inovar e avançar a plataforma sem ter nehuma mudança significativa no núcleo das APIs do _framework_ desde a versão 1.0 de 27 de julho de 2016.
* **Você valoriza uma alta conformidade com os padrões** - Focado no ES2015+ e nos _Web Components_ da W3C, e evitando abstrações desnecessárias, Aurelia provê o modelo de componentes mais limpo e em confirmidade com os padrões que você poderá ebcibtrar.
* **Você acha que o _framework_ deveria "sair do seu caminho"** - Aurelia é o único _framework_ que permite que você construa componentes com JavaScript/TypeScript puro. O _framework_ fica fora do seu caminho de modo que seu código permanece limpo e fácil de evoluir com o tempo.
* **Você gosta de modelos de programação que são fáceis de aprender e lembrar** - Por causa de seu desenho simples e consistente, os desenvolvedores estão aptos a aprender um conjunto pequeno de padrões e APIs do Aurelia, enquanto alcançam possibilidades ilimitadas. Convenções simples ajudam os desenvolvedores a seguir padrões sólidos e a reduzir a quantidade de código que eles têm que escrever e manter. Isso tudo resulta em menos perda de tempo com o _framework_ e mais foco na aplicação.
* **Você prefere uma plataforma que se integra bem com outros _frameworks_ e bibliotecas** - Por causa do desenho extensível do Aurelia e da sua aderência rigorosa aos padrões _web_, é fácil integrar o Aurelia com qualquer biblioteca ou _framework_ de terceiros, incluindo jQuery, React, Polymer, Bootstrap, MaterializeCSS e muitos mais.
* **Você adora e quer ser parte de iniciativas de código aberto** - Aurelia tem o código aberto sob a licensa MIT e não adiciona ou remove cláusulas especiais ou condições à licensa. Temos orgulho do trabalho que a comunidade tem feito conosco e adoraríamos que você se juntasse e nos ajudasse a fazer o Aurelia ainda melhor para todos.
* **Você progride ao fazer parte de uma comunidade receptiva** - Com um dos maiores e mais ativos canais de desenvolvedores no Git, um número enorme de contribuidores e um grande e ativo time principal, Aurelia tem uma comunidade incrível. Nosso time principal e a comunidade adora dar as boas vindas a novos desenvolvedores e trabalhamos bastante para ajudar uns aos outros.
