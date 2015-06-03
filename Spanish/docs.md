# Documentación

Hemos planeado para Aurelia un conjunto muy rico de documentos. Desafortunadamente aún no los hemos terminado. En cualquier caso, para este temprano periodo de vista previa, hemos compuesto este documento, que contiene ejemplos de las tareas más comunes que puedas necesitar. Si tienes preguntas, espero que te unas a nosotros en nuestro [canal en gitter](https://gitter.im/aurelia/discuss).

> **Nota:** ¿Estás buscando esta guía en otro idioma? Echa un vistazo a nuestro repositorio [documentation](https://github.com/aurelia/documentation).

<h2 id="browser-support"><a href="#browser-support">Soporte a navegadores</a></h2>

Aurelia se diseñó para navegadores actualizados automática y permanentemente (Evergreen Browsers). Esto incluye Chrome, Firefox, IE11 y Safari 8. Además hemos identificado como dar soporte para IE9 y sucesivos. Para ello necesitas instalar un polyfill adicional para MutationObservers. Esto se puede conseguir haciendo una instalación vía jspm de `github:polymer/mutationobservers`. A continuación envuelve la llamada a `aurelia-bootstrpper` de la siguiente manera:

```markup
<script src="jspm_packages/github/polymer/mutationobservers@0.4.2/MutationObserver.js"></script>
<script src="jspm_packages/system.js"></script>
<script src="config.js"></script>
<script>
  // carga el polyfill WeakMap requerido por MutationObservers
  System.import('core-js').then( function() {
    // importa el polyfill MutationObserver
    System.import('mutationobservers').then( function() {
      // asegura que se inicie Aurelia cuando todas las dependencias requeridas por IE9 estén cargadas
      System.import('aurelia-bootstrapper');
    })
  });
</script>
```

> **Nota:** Aurelia en si mismo no necesita de WeakMap, pero este es usado por el polyfill MutationObserver. 


<h2 id="startup-and-configuration"><a href="#startup-and-configuration">Arranque y configuración</a></h2>

Muchas plataformas tienen un punto de entrada o "main" para la ejecución del código. Aurelia no es diferente. Si has leído la página [Get Started](/get-started.html), entonces ya habrás visto el atributo `aurelia-app`. Basta con colocarlo en un elemento HTML y el iniciador (bootstrapper) de Aurelia cargará los archivos _app.js_ y _app.html_, los enlazará y los inyectará en el elemento DOM en el que colocaste dicho atributo.

Pero con frecuencia deseamos configurar el marco de trabajo o ejecutar algún código antes de mostrar nada al usuario. Así que es probable, que según vaya avanzando el proyecto, te encuentres con la necesidad de realizar alguna configuración de arranque. Para ello, puedes proporcionar un valor para el atributo `aurelia-app` que apunte a un módulo de configuración. Este módulo debe exportar exclusivamente una función que se llame `configure`. Aurelia invocará a tu función `configure`, pasándole el objeto Aurelia que podrás entonces usar para configurar el marco de trabajo por ti mismo y decidir que, cuando y donde mostrar tu interfaz de usuario (UI). Aquí tienes un ejemplo de archivo de configuración:

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

Con excepción del complemento a medida -custom plugin-, este código se corresponde esencialmente con lo que `aurelia-app` hace normalmente por nosotros. Cuando cambias a un planteamiento con archivo de configuración tienes que configurar todo esto por ti mismo, pero también puedes instalar complementos a medida, montar el contenedor para inyección de dependencias con algunos servicios e instalar recursos globales que hayan de usarse en plantillas de vista -view templates-.

Si quieres pasarte al planteamiento con archivo de configuración de inicio, puedes escribir un sencillo archivo que reúna todas las opciones estándar que enumeramos antes. Este sería su aspecto:

 ```javascript
export function configure(aurelia) {
  aurelia.use
    .standardConfiguration()
    .developmentLogging();

  aurelia.start().then(a => a.setRoot());
}
```

<h3 id="logging"><a href="#logging">Sistema de acceso -logging-</a></h3>

Aurelia tiene una abstracción sencilla para accesos que usa el propio marco de trabajo -framework-. Por defecto, no produce efectos -no-op-. La configuración anterior muestra como instalar un objeto appender que tomará más tarde los datos de acceso -log data- y los sacará por la consola. También puedes ver como se establece el nivel del registro. Las opciones disponibles para este ajuste son: `none`, `error`, `warn`, `info` y `debug`.

Puedes crear fácilmente tus propios objetos appender. Solo implementa una clase que se acomode a la interfaz de appender. La mejor forma de ver como hacerlo es estudiar nuestro propio [código fuente del appender para el registro de consola](https://github.com/aurelia/logging-console/blob/master/src/index.js).

<h3 id="plugins"><a href="#plugins">Plugins</a></h3>

Un _complemento_ -plugin- es solo un módulo con una función `install` exportada. Durante el arranque Aurelia cargará todos los módulos de complementos -plugin modules- y llamará a sus respectivas funciones `install`, pasándoles el objeto Aurelia de manera que puedan configurar correctamente el marco de trabajo. Los complementos  pueden opcionalmente devolver un objeto `Promise` -promesa- a través de su función `install` a fin de realizar tareas asíncronas de configuración. Cuando escribas un complemento asegúrate de proporcionar todos los metadatos, incluyendo una estrategia de vista para los elementos personalizados.

Para realizar la configuración de tus complementos desde la aplicación puedes especificar una función como segundo argumento de la función `install`. Tu función de instalación de complementos la puede ejecutar después de que las tareas de instalación se hayan efectuado. Quien quisiera hacer uso de tu complemento escribiría algo así:

```javascript
aurelia.use.plugin('./path/to/plugin', config => { /* configuration work */ });
```

> **Nota:** No te fíes de las convenciones para nombres dentro de los complementos. No sabes como va a modificar el usuario de tu complemento las reglas de Aurelia. Los complementos de terceros deben ser explícitos para asegurarnos de que funcionarán correctamente en diferentes contextos.

<h4 id="promises"><a href="#promises">Promesas -promises</a></h4>

Aurelia usa por defecto promesas ES6 o un polyfill. En cualquier caso, puedes reemplazar esto con la excelente librería de promesa [Bluebird](https://github.com/petkaantonov/bluebird). Inclúyela simplemente en tu página antes de cualquier referencia a los otros scripts. Te proporcionará su propia implementación ajustada a los estándares de promesas -Promise- que actualmente es más rápida que la (implementación) nativa y tiene un mejor soporte para depuración. Además cuando se usa en combinación con el transpilador Babel (compilador de ES6 a ES5), puedes usar [coroutines](https://babeljs.io/docs/usage/transformers/#bluebird-coroutines) para código asíncrono mejorado.

<h3 id="the-aurelia-object"><a href="#the-aurelia-object">El objeto Aurelia</a></h3>

Puesto que tanto un módulo de configuración a medida como los complementos realizan su trabajo interactuando con el objeto Aurelia, te mostramos una breve explicación de esta API en el código siguiente:

```javascript
export class Aurelia {
  loader:Loader; //the module loader
  container:Container; //the app-level dependency injection container
  use:Plugins; //the plugins api (see above)

  withInstance(type, instance):Aurelia; //DI helper method (pass through to container)
  withSingleton(type, implementation):Aurelia; //DI helper method (pass through to container)
  globalizeResources(...resourcePaths):Aurelia; //module ids of resources relative to the configuration/plugin module
  renameGlobalResource(resourcePath, newName); //renames a globally available resource to avoid naming conflicts

  start():Promise; //starts the framework, causing plugins to be installed and resources to be loaded
  setRoot(root, applicationHost):Promise; //set your "root" or "app" view-model and display it
}
```

<h2 id="views-and-view-models"><a href="#views-and-view-models">Vistas y modelos</a></h2>

En Aurelia, los elementos de interfaz de usuario están compuestos por parejas de _vista_ y _modelo_ . La _vista_ se escribe con HTML y se despliega en el DOM. El _modelo_ se escribe con JavaScript y provee datos y comportamiento a la _vista_. El motor de plantillas y/o la inyección de dependencias son responsables de la creación de estas parejas y la aplicación de un ciclo de vida previsible para el proceso. Una vez generado, el poderoso _sistema de enlace_ -databinding- enlaza las dos partes permitiendo que los cambios en los datos se reflejen en la _vista_ y viceversa. Esta división de competencias favorece la colaboración desarrollador/diseñador, el mantenimiento, la flexibilidad de la arquitectura e incluso el control del código fuente.

<h3 id="dependency-injection"><a href="#dependency-injection">Inyección de dependencias (DI)</a></h3>

Modelos y otros elementos de interfaz, como los elementos personalizados -Custom Elements- y los atributos personalizados -Custom Attributes-, son creados como clases que son llamadas por el marco de trabajo usando un contenedor de inyección de dependencias. El código escrito con este estilo es fácil de separar en módulos y de testear. En lugar de crear clases voluminosas, puedes desmenuzar las cosas en pequeños objetos que colaborarán para lograr un objetivo. Luego la inyección de dependencias podrá montar por nosotros el conjunto en tiempo de ejecución.

Para aprovechar la inyección de dependencias, simplemente "decorar" -decorate. tu clase para que esta le indique al marco de trabajo lo que debe pasarle a su constructor (de clase). Aquí tienes un ejemplo de una modelo que depende de un objeto HttpClient de Aurelia.

```javascript
import {inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@inject(HttpClient)
export class CustomerDetail{
    constructor(http){
        this.http = http;
    }
}
```

Con los decoradores ES7 o Typescript habilitados, solamente añade el decorador `inject`, pasando un argumento por cada tipo inyectado. Si no estás usando un lenguaje que soporte el patrón Decorator, o si no quieres usarlo, en su lugar puedes añadir a tu clase una propiedad o método estáticos que se llame `inject`. Este debe devolver un vector de tipos inyectables. Aquí tienes el mismo ejemplo, pero en CoffeeScript con módulos CommonJS:

```coffeescript
HttpClient = require('aurelia-http-client').HttpClient;

class Flickr
  constructor: (@http) ->
  @inject:[HttpClient]
``` 

Si estás usando TypeScript, puedes usar la opción de compilado `--emitDecoratorMEtadata` junto con el decorador `autoinject()` de Aurelia para capacitar al marco de trabajo a leer la información de tipos estándar de TS. Como resultado, no hay necesidad de duplicar los tipos. Así es como quedaría:

```javascript
import {autoinject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@autoinject()
export class CustomerDetail{
    constructor(http:HttpClient){
        this.http = http;
    }
}
```

> **Note:** Hay un detalle interesante acerca de como implementa Typescript esta opción de compilado. En realidad funciona con cualquier decorador. Así, si aparecen otros decoradores en tu clase TS, no hay ninguna necesidad de incluir el decorador `autoinject`. La información acerca del tipo será accesible para el marco de trabajo de inyección de dependencias de Aurelia.

Cuando declaramos explicitamente las dependencias, es importante saber que no tienen que ser necesariamente tipos constructores. También pueden ser instancias de `resolvers`. Por ejemplo, echale un vistazo a esto:

```javascript
import {Lazy, inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@inject(Lazy.of(HttpClient))
export class CustomerDetail{
    constructor(getHTTP){
        this.getHTTP = getHTTP;
    }
}
```

El `lazy resolver` en realidad no nos proporciona un objeto del tipo `HttpClient`. En su lugar nos entrega una función que, al ser llamada, nos devolverá un objeto del tipo `HttpClient`. Hay diferentes `resolvers` disponibles y puedes crear los tuyos propios montando una clase que herede de `Resolver`. Aquí tienes una lista de lo que te proporcionamos:

* `Lazy` - Inyecta una función para evaluar relajadamente -lazily- la dependencia.
    * p.ej. `Lazy.of(HttpClient)`
* `All` - Inyecta un vector de todos los servicios registrados con la clave provista.
    * p.ej. `All.of(Plugin)`
* `Optional` - Inyecta un objeto de una clase solo si ya existe en el contenedor, en otro caso, null.
    * p.ej. `Optional.of(LoggedInUser)`

Junto a estos `resolvers`, también puedes usar decoradores `Registration` (de registro) para especificar el registro o ciclo de vida por defecto de un objeto. Por defecto, el contenedor de inyección de dependencias asume que todos son objetos singulares -singleton instance-; un objeto por aplicación. Sin embargo, puedes usar un decorador de registro -registration decorator- para cambiar esto. Aquí tienes un ejemplo:

```javascript
import {transient, inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@transient()
@inject(HttpClient)
export class CustomerDetail{
    constructor(http){
        this.http = http;
    }
}
```

Ahora, cada vez que se le solicita al contenedor de inyección de dependencias un objeto del tipo `CustomerDetail`, el contenedor devolverá un nuevo objeto, en lugar de uno único -singleton-. Los registros `Singleton` y `Transient` vienen con el paquete, pero puedes crear los tuyos propios escribiendo una clase que herede de `Registration`.

No te preocupes si no sabes o no quieres usar decoradores. Disponemos de un mecanismo alternativo. Incluye simplemente una propiedad o método estáticos `decorators` y luego usa nuestro auxiliar -helper- `Decorators` encadenable. El auxiliar dispone de métodos para todos nuestros decoradores, de manera que te resultará fácil de usar en cualquier lenguaje. Así es como se podría escribir el ejemplo anterior en CoffeeScript 

```coffeescript
HttpClient = require('aurelia-http-client').HttpClient;
Decorators = require('aurelia-framework').Decorators;

class CustomerDetail
  constructor: (@http) ->
  @decorators:Decorators.transient().inject(HttpClient);
```

<h3 id="parent-vm-reference"><a href="#parent-vm-reference">Modelos de vistas padre -Parent View Models-</a></h3>

Por defecto el acceso a un modelo de vista está limitado a los objetos inyectados así como a los descendientes de la clase. A veces puede resultar conveniente referirse a objetos y métodos de un modelo de vista padre, lo que podemos conseguir guardándolo durante el método _bind_ del ciclo de vida de la vista:

```javascript
class ChildViewModel {
  bind(bindingContext) {
    this.$parent = bindingContext;
  }
}
```

<h2 id="templating"><a href="#templating">Uso de plantillas -templating-</a></h2>

El motor de plantillas de Aurelia es responsable de cargar las vistas y sus recursos requeridos, compilando tu HTML para un rendimiento óptimo y mostrando la interfaz de usuario (UI) en pantalla. Para crear una vista, todo lo que tienes que hacer es escribir un archivo HTML que incluya un `HTMLTemplate`. Aquí tienes una vista sencilla:

```markup
<template>
    <div>Hello World!</div>
</template>
```

Todo lo que esté dentro de una etiqueta `template` será manejado por Aurelia. En todo caso, puesto que Aurelia usa la tecnología HTMLImport para cargar las vistas, también puedes incluir _links_ -enlaces-, que serán correctamente cargados, incluyendo semántica de resolución de recursos relativos. En otras palabras, puedes hacer esto:

```markup
<link rel="stylesheet" type="text/css" href="./hello.css">

<template>
    <div class="hello">Hello World!</div>
</template>
```

De paso, esto permite cargar dinámicamente hojas de estilo dependiendo de la vista e incluso componentes web -Web Components-.

Cada vez que quieras importar un recurso específico de Aurelia, ya sea un _Custom Element_ -elemento a medida-, _Custom Attribute_ -atributo personalizado-, o _Value Converter_ -conversor de valores-, debes usar en su lugar un elemento `require` dentro de tu vista. Aquí tienes un ejemplo:

```markup
<template>
  <require from='./nav-bar'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

En este caso `nav-bar` es un _Custom Element_ -elemento a medida- de Aurelia que requerimos para usarlo. Usar el elemento `require` de Aurelia hace que el canal para recursos del marco de trabajo procese el elemento importado, lo que tiene las siguientes ventajas:

* Evita duplicidades - El recurso se descarga una sola vez para toda la aplicación. Aunque otras vistas requieren el mismo elemento, este no será descargado nuevamente.
* Compilado único - Las plantillas requeridas de esta manera para elementos a medida son  compilados una única vez para la aplicación completa.
* Alcance local - El recurso requerido solo es visible dentro de la vista que lo requiere, reduciendo la posibilidad de conflictos entre nombres y facilitando el mantenimiento y la comprensión (del código) al eliminar los elementos globales.
* Renombrado - Los recursos pueden ser renombrados al requerirlos, si resulta que dos recursos ajenos con el mismo nombre u otro parecido son usados en la misma vista.
    - p.ej. `<require from="./nav-bar" as="foo-bar"></require>` - Ahora en vez de usar un elemento `nav-bar` puedes usar un elemento `foo-bar`. (Esto está basado en la sintaxis `import` de ES6 para el cual renombrar es considerado un sustitutivo al uso de un alias porque lo que hace es estrictamente renombrar el tipo.)
* Empaquetado - La importación puede señalar a un módulo con múltiples recursos que serán todos importados a la misma vista.
* Extensibilidad - Puedes definir nuevos tipos de recursos que cuando son importados de esta manera pueden ejecutar la carga (asíncronamente única) y el registro (único por vista) a medida. Se trata de un procedimiento -pipeline- declarativo y extensible para cargar recursos.
* ES6 - El código es cargado por el cargador -loader- de ES6 en lugar de por el mecanismo _HTMLImport_, habilitándose todas las características y extensibilidad de tu cargador.

En tus vistas harás aprovechamiento frecuente de los diferentes tipos de recursos mencionados con anterioridad así como del enlazado de datos -databinding-.

>**Nota:** Puede que te preocupe lo tedioso de tener que importar cosas en cada vista. Recuerda que durante la fase de configuración inicial -bootstrapping- puedes configurar Aurelia para que los recursos globales estén disponibles en todas las vistas. Usa solo `aurelia.globalizeResources(...resourcePaths)`.

Aurelia suplementa -polyfills- los navegadores que no soportan las plantillas. Sin embargo, algunas características de las plantillas no pueden obtenerse usando polyfills y requieren una solución alternativa. En particular esto ocurre cuando añadimos elementos `<template>` dentro de elementos `<select>` y `<table>`. Lo siguiente no podría no podría visualizarse en un navegador que no soportará de forma nativa las plantillas:

```markup
  <table>
    <template repeat.for="customer of customers">
      <tr>
        <td>${customer.fullName}</td>
      </tr>
    </template>
  </table>
```

Para poder iterar sobre los elementos `<tr>`, añade simplemente el `repeat` en el propio elemento `<tr>`:

```markup
  <table>
    <tr repeat.for="customer of customers">
      <td>${customer.fullName}</td>
    </tr>
  </table>
```

<h3 id="databinding"><a href="#databinding">Enlazado de datos -databinding-</a></h3>

El enlazado de datos te permite enlazar el estado y el comportamiento de un objeto Javascript y una vista HTML. Cuando se establece este enlace, cualquier cambio en las propiedades enlazadas puede sincronizarse en una o dos direcciones. Los cambios en el objeto JavaScript pueden reflejarse en la vista y los cambios en la vista pueden reflejarse en el objeto JavaScript. Para establecer este vínculo, harás uso de las órdenes de enlazado -binding commands- en tu HTML. Las órdenes de enlazado son claramente identificables por su uso de "." como una especie de operador de enlace. Siempre que un atributo HTML contenga un ".", el compilador pasará el nombre y el valor del atributo al lenguaje de enlazado para su interpretación. El resultado es una o más expresiones de enlazado capaces de establecer el enlace cuando se crea la vista.

Puedes ampliar el sistema con tus propias órdenes de enlazado, pero Aurelia proporciona una colección para cubrir los casos de uso más frecuentes.

<h4 id="binding-modes"><a href="#binding-modes">bind, one-way, two-way & one-time</a></h4>

La orden de enlazado más común es `.bind` -enlaza-. Esta hará que la propiedad sea vinculada usando un enlace uni-direccional -"one-way"- para todos sus atributos, excepto para el caso de valores de elementos de formulario, que reciben un enlace bi-direccional -"two-way"-.

_¿Pero que significa esto?_

Enlace uni-direccional significa que los cambios fluyen de tus modelos (de vista) Javascript hacia la vista, y no de la vista hacia el modelo. Enlace bi-direccional significa que los cambios fluyen en ambas direcciones. `.bind` intenta usar un supuesto razonable al asumir que si estás estableciendo un enlace con el valor de una propiedad en un elemento de formulario, entonces probablemente querrás que los cambios realizados en el formulario fluyan hacia el modelo. Para todo lo demás usa un enlazado uni-direccional, especialmente teniendo en cuenta que frecuentemente carece de sentido el enlace bi-direccional para elementos que no son de formulario. Aquí tienes un pequeño ejemplo de enlazado usando `.bind`:

```markup
<input type="text" value.bind="firstName">
<a href.bind="url">Aurelia</a>
```

En el ejemplo anterior, el elemento `input` tendrá su `value` -valor- vinculado a la propiedad `firstName` en el modelo. Cambios en la propiedad `firstName` actualizarán el `input.value` y cambios en el `input.value` actualizarán la propiedad `firstName` en el modelo. Por otro lado, la etiqueta `a` tendrá su `href` vinculado a la propiedad `url` en el modelo. Solo los cambios en la propiedad `url` fluirán hacia el `href` de la etiqueta `a`, no al revés.

Aunque siempre puedes ser explícito y usar `.one-way` o `.two-way` en lugar de `.bind`. Un caso común en que se requiere esto es con los componentes web -Web Components- que funcionan como controles de tipo input. Asi que podrás imaginarte haciendo algo como esto:

```markup
<markdown-editor value.two-way="markdown"></markdown-editor>
```

En orden a optimizar el rendimiento y minimizar el uso de CPU y de memoria, puedes aprovechar alternativamente la orden de enlace `.one-time` para enviar datos desde el modelo hacia la vista una sola vez -one time-. Esto ocurrirá durante la fase inicial de enlazado, después de lo cual no habrá ninguna sincronización.

<h4 id="event-modes"><a href="#event-modes">delegate, trigger & call</a></h4>

Las órdenes de enlace no solo sirven para conectar propiedades y atributos, sino que se pueden usar para lanzar comportamientos. Por ejemplo, para invocar un método en el modelo cuando se pulsa un botón de la vista, usarías la orden `trigger` de la siguiente manera:

```markup
<button click.trigger="sayHello()">Say Hello</button>
```

Cuando se pulsa el botón, se invoca el método `sayHello` en el modelo. Dicho esto, no resulta muy eficiente añadir gestores de eventos a cada elemento por separado, así que con frecuencia desearás emplear la delegación de evento -event delegation-. Para ello, usa la orden `.delegate`. Aquí está el mismo ejemplo, pero con delegación de evento:

```markup
<button click.delegate="sayHello()">Say Hello</button>
```

La propiedad `$event` se puede pasar como un argumento en una llamada a una función delegate/trigger si necesitas acceder al objeto evento.

```markup
<button click.delegate="sayHello($event)">Say Hello</button>
```

> **Nota:** Si no estás familiarizado con la delegación de eventos, se trata de una técnica que usa la naturaleza "burbujeante" de los eventos DOM. Cuando usamos `.delegate` se añade un único gestor de eventos al documento, en lugar de a cada elemento. Cuando se dispara el evento del elemento, este "asciende" a través del DOM hasta que alcanza el (nivel de) documento, donde es gestionado (por el gestor asignado). Esta es una manera más eficiente en memoria de gestionar eventos y se recomienda que lo uses como tu mecanismo por defecto.

Todo esto va de una u otra manera en contra de los eventos de DOM. Ocasionalmente puedes encontrarte con un atributo -Custom Attribute- o elemento a medida -Custom Element- de Aurelia que necesite directamente una referencia a tu función de forma que pueda ser invocada a mano posteriormente. Para pasar una referencia de función, usa el enlazador `.`call` (puesto que el atributo la _llamará -call-_ más tarde):

```markup
<div touch.call="sayHello()">Say Hello</button>
```

Ahora el atributo a medida -Custom Attribute- `touch` recibirá una función que podrá llamar para invocar tu código `sayHello()`. Dependiendo de la naturaleza del `implementor`, podrías ser capaz de recibir datos del `caller`. Esto funciona igual que con `trigger/delegate` proporcionando un objeto `$event`. 

<h4 id="string-interpolation"><a href="#string-interpolation">Interpolación de cadenas</a></h4>

A veces necesitas vincular propiedades directamente dentro del contenido del documento o intercalarlas dentro de un valor de atributo. Para ello puedes usar la sintaxis para interpolación de cadenas  `${expresión}`. La interpolación de cadenas es un enlace uni-direccional cuya salida es convertida en una cadena. Aquí tienes un ejemplo:

```markup
<span>${fullName}</span>
```

La propiedad `fullName` será directamente interpolada en el contenido del (elemento) `span`. También puedes usar la interpolación de cadenas para manipular enlaces a clases CSS así:

```markup
<div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
```

En esta linea "dot" es una clase presente estáticamente y "green" solo está presente si `isHappy`es verdadero, en caso contrario estará presente la clase "red". Además, cualquiera que sea el valor de `color`... este es añadido como una clase.

> **Nota:** Puedes usar expresiones simples en tu enlace. No intentes hacer cosas demasiado rebuscadas. No queremos que haya código en la vista. Solo buscamos establecer un vínculo entre la vista y su modelo.

<h4 id="ref"><a href="#ref">ref</a></h4>

Además de órdenes e interpolación, el lenguaje de enlazado reconoce el uso de un atributo especial: `ref`. Al usar `ref` puedes crear un nombre local para un elemento que podrás usar como referencia en otra expresión de enlace. También se añadirá como una propiedad en el modelo, de manera que será accesible a través de código. Aquí tienes un ejemplo claro del uso de `ref`:

```markup
<input type="text" ref="name"> ${name.value}
```

También puedes usar el enlace especial `.view-model` combinado con `ref` para obtener el objeto de tipo modelo que respalda un elemento a medida de Aurelia. Mediante esta técnica puedes vincular diferentes componentes entre si de la siguiente manera:

```markup
<i-produce-a-value ref.view-model="producer"></i-produce-a-value>
<i-consume-a-value input.bind="producer.output"></i-consume-a-value>
```

<h4 id="select-elements"><a href="#select-elements">Elementos select</a></h4>

`value.bind` sobre un HTMLSelectElement tiene un comportamiento especial para soportar los modos de selección simple y múltiple así como el enlazado a objetos.

Un típico elemento select se muestra usando una combinación de `value.bind` y `repeat`, como el siguiente:

```markup
<select value.bind="favoriteColor">
    <option>Select A Color</option>
    <option repeat.for="color of colors" value.bind="color">${color}</option>
</select>
```

A veces deseas trabajar instancias de objeto en lugar de con cadenas. Aquí está el código de marca para construir un elemento select desde un vector teórico de objetos employee:

```markup
<select value.bind="employeeOfTheMonth">
  <option>Select An Employee</option>
  <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
</select>
```

La diferencia fundamental entre este ejemplo y el anterior es que estamos almacenando los valores de opción en una propiedad especial, `model`, en lugar de en la propiedad `value` del elemento option que solo acepta cadenas de texto.

<h4 id="multi-select-elements"><a href="#multi-select-elements">Elementos multi select</a></h4>

Puedes enlazar el valor del elemento select a una propiedad vector en un escenario de selección múltiple. Así es como enlazas un vector de cadenas, `favoriteColors`:

```markup
<select value.bind="favoriteColors" multiple>
    <option repeat.for="color of colors" value.bind="color">${color}</option>
</select>
```

Esto también funciona con vectores de objetos:

```markup
<select value.bind="favoriteEmployees" multiple>
  <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
</select>
```

<h4 id="radios"><a href="#radios">radios</a></h4>

`checked.bind` sobre un HTMLInputElement tiene un comportamiento especial para permitir el enlazado de valores no-booleanos como cadenas u objetos.

Un grupo típico de botones del tipo radio se muestra usando una combinación de `value.bind` y `repeat`, como a continuación:

```markup
<label repeat.for="color of colors">
  <input type="radio" name="clrs" value.bind="color" checked.bind="$parent.favoriteColor" />
  ${color}
</label>
```

A veces puede ser que quieras trabajar con instancias de objetos en lugar con cadenas de texto. Aquí tienes el marcado para construir un grupo de botones tipo radio a partir de un supuesto vector de objetos `employees`:

```markup
<label repeat.for="employee of employees">
  <input type="radio" name="emps" model.bind="employee" checked.bind="$parent.employeeOfTheMonth" />
  ${employee.fullName}
</label>
```

La diferencia fundamental entre este ejemplo y el anterior es que estamos guardando los valores de los `<input>` en una propiedad especial `model`, en lugar de en la propiedad `value` de dichos elementos que solo aceptan cadenas de texto.

También puedes enlazar un grupo de estos a una propiedad booleana:

```markup
<label><input type="radio" name="tacos" model.bind="null" checked.bind="likesTacos" />Unanswered</label>
<label><input type="radio" name="tacos" model.bind="true" checked.bind="likesTacos" />Yes</label>
<label><input type="radio" name="tacos" model.bind="false" checked.bind="likesTacos" />No</label>
```

<h4 id="checkboxes"><a href="#checkboxes">checkboxes</a></h4>

Para responder mejor a escenarios de multi-selección, Aurelia habilita el enlazado de la propiedad `checked` de un elemento `<input>` a un vector. Aquí puedes ver como enlazar un vector de cadenas de texto llamado `favoriteColors`:

```markup
<label repeat.for="color of colors">
  <input type="checkbox" value.bind="color" checked.bind="$parent.favoriteColors" />
  ${color}
</label>
```

Esto funciona igualmente tanto con vectores como con objetos:

```markup
<label repeat.for="employee of employees">
  <input type="checkbox" model.bind="employee" checked.bind="$parent.favoriteEmployees" />
  ${employee.fullName}
</label>
```

Por supuesto puedes enlazar cada `checkbox` a su propiedad booleana:

```markup
<li><label><input type="checkbox" checked.bind="wantsFudge" />Fudge</label></li>
<li><label><input type="checkbox" checked.bind="wantsSprinkles" />Sprinkles</label></li>
<li><label><input type="checkbox" checked.bind="wantsCherry" />Cherry</label></li>
```

<h4 id="innerhtml"><a href="#innerhtml">innerHTML</a></h4>

Puedes enlazar con la propiedad `innerHTML` de un elemento usando el atributo `innerhtml`:

``` markup
<div innerhtml.bind="htmlProperty"></div>
<div innerhtml="${htmlProperty}"></div>
```

Aurelia proporciona un sencillo conversor para depuración de HTML que se puede usar de la siguiente manera:

``` markup
<div innerhtml.bind="htmlProperty | sanitizeHtml"></div>
<div innerhtml="${htmlProperty | sanitizeHtml}"></div>
```

Te animamos a usar un depurador HTML más completo como [sanitize-html](https://www.npmjs.com/package/sanitize-html). Así es como construirías usando este paquete:

``` bash
jspm install npm:sanitize-html
```

``` javascript
import sanitizeHtml from 'sanitize-html';

export class MySanitizeHtmlValueConverter {
  toView(untrustedHtml) {
    return sanitizeHtml(untrustedHtml);
  }
}
```

> **Nota**: Enlazar usando el atributo `innerhtml` simplemente establece la propiedad `innerHTML` del elemento. El marcado no pasa a través del sistema de plantillas de Aurelia. Enlazar expresiones y elementos require no se evaluarán. Una solución para este escenario está siendo seguido en [aurelia/templating#35](https://github.com/aurelia/templating/issues/35).

<h4 id="textcontent"><a href="#textcontent">textContent</a></h4>

Puedes enlazar con una propiedad `textContent` de un elemento usando el atributo `textcontent`:

``` markup
<div textcontent.bind="stringProperty"></div>
<div textcontent="${stringProperty}"></div>
```

El enlazado de datos bidireccional es soportado con elementos `contenteditable`:

``` markup
<div textcontent.bind="stringProperty" contenteditable="true"></div>
```

<h4 id="style"><a href="#style">style</a></h4>

Puedes enlazar con una cadena o un objeto CSS con el atributo `style` de un elemento:

``` javascript
export class Foo {
  constructor() {
    this.styleString = 'color: red; background-color: blue';

    this.styleObject = {
      color: 'red',
      'background-color': 'blue'
    };
  }
}
```

``` markup
<div style.bind="styleString"></div>
<div style.bind="styleObject"></div>
```

Usa el alias del atributo `style`, `css`, cuando hagas interpolación de cadenas para asegurar que tu aplicación es compatible con Internet Explorer:

``` markup
<!-- bien: -->
<div css="width: ${width}px; height: ${height}px;"></div>

<!-- incompatible con Internet Explorer: -->
<div style="width: ${width}px; height: ${height}px;"></div>
```

<h4 id="adaptive-binding"><a href="#adaptive-binding">Enlazado adaptable -Adaptive Binding-</a></h4>

Aurelia incorpora un sistema de enlazado adaptable, que escoge entre cierto número de estrategias a la hora de determinar como observar los cambios de la manera más eficiente. Para más información acerca de como funciona esto lee [esta entrada](http://blog.durandal.io/2015/04/03/aurelia-adaptive-binding/). En la mayoría de las situaciones no necesitarás ocuparte de estos detalles, pero en cualquier caso nos ayudará a ser consciente de escenarios que nos conduzcan a usos ineficientes del sistema de enlazado de datos.

**Lo primero de lo que hay que ser conscientes es que las propiedades computadas (propiedades con funciones accesoras -getter functions-) se observan usando dirty-checking (N.T.: revisión cíclica y automática de todas las variables en todos los ámbitos -"comprobación bruta"-).** Otras estrategias más eficientes como `Object.observe` y la reescritura de propiedades no son compatibles con estos tipos de propiedades.

En el entorno actual de navegadores el `dirty-checking` es un mal necesario. Muy pocos navegadores soportan `Object.observe` en el momento de escribir estas notas. El mecanismo de dirty-checking de Aurelia es similar al usado en [Polymer](https://www.polymer-project.org/). Es muy eficiente y utiliza la cola de micro-tareas de Aurelia para guardar las actualizaciones del DOM.

Unos pocos enlaces a datos usando `dirty-checking` no provocará problemas de rendimiento de la aplicación. Pero un uso extenso del mismo podría. Afortunadamente hay una forma de evitar este mecanismo para comprobar propiedades computadas simples. Observa la propiedad 'fullName' en el ejemplo siguiente:

```javascript
export class Person {
  firstName = 'John';
  lastName = 'Doe';

  @computedFrom('firstName', 'lastName')
  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }
}
```

Hemos usado el decorador `@computedFrom` para proporcionar una pista al sistema de enlazado de Aurelia. Este sabe así que solo tiene que comprobar si hay cambios en `fullName` cuando los hay en `firstName` o `lastName`.

También es importante ser conscientes de como funciona el `dirty-checking`. Cuando una propiedad es "comprobada en bruto" `"dirty-checked"` El sistema de enlazado comprueba periodicamente si el valor actual de la propiedad coincide con el valor previamente observado de la misma. Por defecto esta comprobación se realiza cada 120 milisegundos. Esto significa que la función accesor de una propiedad podría ser llamada con bastante frecuencia lo que significa que debería ser tan eficiente como fuera posible. También deberías evitar devolver innecesariamente nuevas instancias de objetos o vectores. Considera la vista siguiente:

```markup
<template>
  <label for="search">Search Issues:</label>
  <input id="search" type="text" value.bind="searchText" />
  <ul>
    <li repeat.for="issue of filteredIssues">${issue.abstract}</li>
  </ul>
</template>
```

Una implementación simplona del modelo de vista:

```javascript
export class IssueSearch {
  searchText = '';

  constructor(allIssues) {
    this.allIssues = allIssues;
  }

  // esto devuelve una nueva instancia del vector con cada llamada lo que resultará en su momento en un montón de actualizaciones del DOM
  get filteredIssues() {
    if (this.searchText === '')
      return [];
    return this.allIssues.filter(x => x.abstract.indexOf(this.searchText) !== -1);
  }
}
```

Implementación mejorada del modelo de vista:

```javascript
export class IssueSearch {
  filteredIssues = [];
  _searchText = '';

  constructor(allIssues) {
    this.allIssues = allIssues;
  }

  get searchText() {
    return this._searchText;
  }
  set searchText(newValue) {
    this._searchText = newValue;
    if (newValue === '') {
      this.filteredIssues = [];
    } else {
      this.filteredIssues = this.allIssues.filter(x => x.abstract.indexOf(this.searchText) !== -1);
    }
  }
}
```

<h3 id="html-extensions"><a href="#html-extensions">Extensiones del HTML -HTML Extensions-</a></h3>

Junto al enlazado de datos -databinding-, cuentas con el poder de las extensiones al HTML de Aurelia para usarlos en tus vistas. Son dos los tipos:

* Elementos a medida -Custom Elements- ¡Amplía HTML mediante nuevas etiquetas! Tus elementos a medida pueden tener sus propias vistas (que usan el enlazado de datos y otros comportamientos) y, opcionalmente, hacer uso del [ShadowDOM](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/) (incluso aunque el navegador no lo soporte).
* Atributos a media -Custom Attributes- Amplía HTML con atributos nuevos que pueden añadirse elementos estándar o a medida. Estos atributos "añaden" -attach- nuevo comportamiento a los elementos.

Naturalmente, todos funcionan sin problemas con el enlazado de datos. Echemos un vistazo a los comportamientos que te proporciona Aurelia y que están disponibles globalmente en todas las vistas.

<h4 id="show"><a href="#show">show</a></h4>

El atributo a medida `show` te permite condicionar la visualización de un elemento HTML. Si el valor de `show` es `true` el elemento se visualiza el elemento, en caso contrario se mantiene oculto. Este comportamiento no añade/elimina el elemento del DOM, solo modifica su visibilidad. Aquí tienes un ejemplo:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

Cuando la propiedad `isSaving` es verdadera, el `div` será visible, en caso contrario estará oculto.

<h4 id="if"><a href="#if">if</a></h4>

El atributo a medida -Custom Attribute- `if` permite añadir/eliminar un elemento HTML condicionalmente. Si el valor es verdadero, el elemento estará presente en el DOM, en caso contrario no.

```markup
<div if.bind="isSaving" class="spinner"></div>
```

Este ejemplo es parecido al anterior con `show`. La diferencia estriba en que si la evaluación de la expresión de enlace resulta falsa, el `div`será eliminado del DOM, en lugar de solo quedar ocultado.

Si necesitas añadir/eliminar condicionalmente un grupo de elementos y no puedes colocar el atributo `if` en un elemento padre, entonces puedes envolver esos elementos en una etiqueta `template` que incluya el atributo `if`. Así es como quedaría:

```markup
<template if.bind="hasErrors">
    <i class="icon error"></i>
    ${errorMessage}
</template>
```

<h4 id="repeat"><a href="#repeat">repeat</a></h4>

El atributo a medida -Custom Attribute- `repeat` te permite mostrar una plantilla varias veces, una por cada elemento de un vector. Aquí tienes un ejemplo que muestra la lista de nombres de clientes:

```markup
<ul>
    <li repeat.for="customer of customers">${customer.fullName}</li>
</ul>
```

Una consideración importante acerca del atributo `repeat` es que trabaja junto con la orden de enlace `.for`. Esta orden de enlace interpreta una sintaxis especial de la forma "elemento de un vector" donde "elemento" es el nombre local que vas a usar en la plantilla y "vector" es una expresión de enlace normal cuya evaluación resulta en un vector.

Hablando de mapas, así es como lo enlazarías con un mapa ES6:

```markup
<ul>
  <li repeat.for="[id, customer] of customers">${id} ${customer.fullName}</li>
</ul>
```

Si en lugar de iterar sobre una colección lo que quieres es hacerlo un determinado número de veces, puedes usar en su lugar la sintaxis "i of count" donde "i" es el índice de la iteración y "count" es una expresión de enlace que se evalúa a un entero.

```markup 
<ul>
  <li repeat.for="i of rating">*</li>
</ul>
```

> **Nota:**: Como el atributo `if`, también puedes usar una etiqueta `template` para agrupar una colección de elementos que no tienen elemento padre.

Cada elemento que está siendo repetido por el atributo `repeat` cuenta con varios valores contextuales especiales disponibles para enlazar:

* `$parent` - A día de hoy, las propiedades y métodos del modelo de la vista principal no son visibles desde dentro de los elementos repetidos. Esperamos resolver esto en una próxima actualización. Entre tanto, puedes acceder ese modelo de vista (view model) usando `$parent`.
* `$index` - El índice del elemento en el vector.
* `$first` - True si el elemento es el primero del vector.
* `$last` - True si el elemento es el último del vector.
* `$even` - True si el elemento tiene un índice par.
* `$odd` - True si el elemento tiene un índice impar.

<h4 id="compose"><a href="#compose">compose</a></h4>

El elemento a medida `compose` te capacita para mostrar dinámicamente interfaz de usuario dentro del DOM. Imagínate que tienes un vector heterogéneo de elementos, pero que cada uno tiene una propiedad `type` que nos dice que es lo que es. Entonces puedes hacer algo así:

```markup
<template repeat.for="item of items">
    <compose
      model.bind="item"
      view-model="widgets/${item.type}">
    </compose>
</template>
```

Ahora, dependiendo del _type_ del elemento, el elemento `compose` cargará un modelo (y vista) diferente y mostrado en el DOM. Si el modelo tiene un método `activate`, el elemento `compose` lo llamará y le`pasará el `model` como parámetro. El método `activate` puede devolver incluso una `Promise` -promesa- que hace que el proceso de composición espere hasta que alguna tarea asíncrona concluya antes de ejecutar realmente los enlaces de datos y el despliegue en el DOM.

El elemento `compose` también tiene un atributo `view` que puede ser usado de la misma manera que `view-model` si no deseas aprovechar la convención estándar de vista/modelo. Si especificas una vista (view), pero no un modelo-vista (view-model), entonces la vista se enlazará al contexto que la envuelve. 

```markup
<template repeat.for="item of items">
    <compose view="my-view.html"></compose>
</template>
```

Si lo que quieres es enlazar a datos un objeto en particular cuando usas solo `view` en lugar de un modelo de vista completamente equipado (tal vez como parte de un `repeat`), puedes hacerlo enlazando directamente el `view-model`. Ahora podrás usar propiedades de ese objeto directamente en tu `view`:

```markup
<template>
    <div repeat.for="item of items">
      <compose view="my-view.html" view-model.bind="item">
    </div>
</template>
```

¿Que pasa si quieres determinar la vista dinámicamente en base a los datos o a las condiciones en tiempo de ejecución? También puedes hacer esto implementando un método `getViewStrategy()` en tu modelo. Este puede retornar una ruta relativa a la vista o un objeto del tipo `ViewStrategy` para el comportamiento a medida para la carga de vistas. La parte bonita es que este método se ejecuta después de la retrollamada -callback- de `activate`, así que tienes acceso a los datos del modelo al determinar la vista.

<h4 id="global-behavior"><a href="#global-behavior">global-behavior</a></h4>

Este no es unq mejora HTML que vayas a usar directamente. En lugar de eso, este funciona en conjunción con una orden de enlace a medida para habilitar dinámicamente el uso declarativo de los complementos jQuery y otras APIs similares en HTML. Veamos un ejemplo para clarificar la idea:

```markup
<div jquery.modal="show: true; keyboard.bind: allowKeyboard">...</div>
```

Este ejemplo está basado en el complemento [Bootstrap modal widget](http://getbootstrap.com/javascript/#modals). En este caso, el complemento `modal` de jQuery será añadido al `div` y será configurado con su opción `show` puesta a `true` y su opción `keyboard` puesta al valor de la propiedad `allowKeyboard` del modelo. Cuando la vista contenedora esté desenlazada, el complemento jQuery será destruido.

Esta capacidad combina el especial `global-behaviour` con sintaxis a medida para habilitar estas capacidades. La sintaxis que ves aquí está basada en la sintaxis del atributo nativo (HTML) `style` que lista las propiedades y los valores separados de la misma manera que en el ejemplo. Ten en cuenta que puedes usar órdenes de enlace como `.bind`, para pasar datos de tu modelo directamente al complemento, o `.call`, para pasar una función de retrollamada directamente al complemento.

Así es como funciona:

Cuando el sistema de enlazado encuentra una orden de enlace que no reconoce, entonces lo interpreta dinámicamente. El atributo nombre es mapeado a un gestor global de enlaces que interpreta la orden de enlace. El gestor puede usar los valores para crear un objeto de opciones que aquel puede pasar al complemento. Cuando la vista se desenlaza, el gestor es capaz de limpiarse a si mismo. En este caso el gestor jQuery conoce el patrón para crear objetos del tipo del complemento y el uso del método `destroy` para hacer limpieza.

> **Nota:** `global-behavior` tiene una lista de gestores que tienes que configurar. Por defecto, solo está configurado para jQuery. Puedes desactivarlo todo, si quieres, pero resulta más fácil sacar partido de los complementos básicos jQuery sin necesidad de hacer algo por tu parte.

<h2 id="routing"><a href="#routing"> Gestión de rutas -routing-</a></h2>

Te pueden pedir que crees muchos estilos diferentes de aplicaciones. Desde aplicaciones de navegación, a escritorios, a interfaces MDI -multiple documents interface-, Aurelia puede manejarlas todas. En muchos de estos casos una pieza clave de tu arquitectura es un sistema de gestión de rutas -router- del lado del cliente, capaz de traducir cambios en URLs en estados de la aplicación.

Si leíste la guía para empezar, sabrás que hay dos partes en la gestión de rutas. Primero, está el (objeto) `Router` que se encuentra en tu modelo. Está configurado con información de rutas y controla la navegación. Luego, tenemos `router-view` (vista del gestor de rutas) que se encuentra en la vista y que es responsable de mostrar lo que sea que el sistema (de gestión de rutas) identifica como el estado actual.

Veamos un ejemplo de configuración.

```javascript
export class App {
  configureRouter(config, router){
    this.router = router;

    config.title = 'Aurelia';
    config.map([
      { route: ['', 'home'],               moduleId: './home/index' },
      { route: 'users',                    moduleId: './users/index',                      nav: true },
      { route: 'users/:id/detail',         moduleId: './users/detail' },
      { route: 'files*path',               moduleId: './files/index',     href:'#files',   nav: true }
    ]);
  }
}
```

Empezamos configurando el método `configureRouter`. Opcionalmente podemos asignar una propiedad `title` -título- para ser usada en la construcción del título del documento, pero la parte más importante es el establecimiento de las rutas. El método `map` del enrutador toma una simple estructura de datos JSON que contiene tu tabla de rutas. Las dos propiedades más importantes son `route` (una cadena de texto o un vector de cadenas de texto), que define el patrón de la ruta, y `moduleId`, que contiene la ruta *relativa* del módulo Id a tu modelo de vista. También puedes asignar una propiedad `title`, a usar cuando se vaya a generar el título del documento, una propiedad `nav` que indica si la ruta debe ser incluida o no el modelo de navegación (también puede ser un número que indicará el orden) y una propiedad `href` que puedes usar para enlazar con ella en el _modelo de navegación `navigation model`_.

>**Nota:** Cualquier propiedad que dejes fuera será determinada convencionalmente por el marco de trabajo en base a lo que hayas proporcionado.

Así que, que opciones tienes para el patrón del sistema de gestión de rutas?

* rutas estáticas -static routes-
    - p.ej. 'home' - Coincide con la cadena exactamente.
* rutas parametrizadas -parameterized routes-
    - p.ej.  'users/:id/detail' - Coincide con la cadena y luego evalúa el parámetro ìd`. La función de retrollamada del modelo `activate` será llamada con un objeto que tiene una propiedad `id` con un valor extraído de la URL.
* rutas comodín -wildcard routes-
    - p.ej. 'files*path' - Coincide con la cadena seguida de cualquier cosa que le siga. La función de retrollamada `activate` será llamado con un objeto que tiene una propiedad `path` con el valor del comodín.

Todas las rutas con una propiedad `nav` verdadera se recogen en un vector `navigation`. Esto hace realmente sencillo usar el enlace de datos para generar una estructura de menú. Otra propiedad importante para el enlazado es la propiedad `isNavigating`. Aquí  tienes un sencillo marcado que muestra lo que podría emparejarse con el modelo mostrado arriba:

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

<h3 id="the-screen-activation-lifecycle"><a href="#the-screen-activation-lifecycle">El ciclo de vida de activación de pantalla -the screen activation lifecycle-</a></h3>

Siempre que el sistema de enrutado procesa una navegación, este ejecuta un estricto ciclo de vida de los modelos desde y hacia los que está navegando. Hay cuatro estados en el ciclo de vida. Se puede acceder opcionalmente a cualquiera de ellos mediante la implementación del método adecuado en la clase de los modelos. Aquí hay una lista de retrollamadas al ciclo de vida:

* `canActivate(params, config, navigationInstruction)` - Implementa este gancho si quieres controlar si se puede navegar o no a tu modelo. Devuelve un valor booleano, una promesa de valor booleano o una orden de navegación.
* `activate(params, config, navigationInstruction)` - Implementa este gancho si quieres ejecutar lógica a la medida justo antes de que se presente tu modelo. Puedes devolver opcionalmente una promesa para decirle al sistema de enrutado que espere el enlazado y añada la vista después de que termines tu trabajo.
* `canDeactivate()` - Implementa este gancho si quieres controlar si el sistema de enrutado puede irse (_can navigate away_) de tu modelo cuando nos movemos a una nueva ruta. Devuelve un valor booleano, una promesa de un valor booleano o una orden de navegación.
* `deactivate()` - Implementa este gancho si deseas ejecutar lógica a medida cuando se navega abandonando tu modelo. Puedes optar por devolver una promesa para decirle al enrutador que espere hasta que termines tu trabajo.

El objeto `params` tendrá una propiedad por cada parámetro de la ruta que fue interpretada. Cualquier parámetro de cadena de consulta también se fusionarán en el objeto `params`. El objeto `config` será el objeto original de configuración de rutas que tu montaste.  

> **Nota** Una _Navigation Command_ orden de navegación es cualquier objeto con un método `navigate(router)`. Cuando se encuentra uno, se cancelará la navegación y el control se transferirá a la orden de navegación. Hay una orden de navegación integrado: `Redirect`.

<h3 id="child-routers"><a href="#child-routers">Enrutadores hijos -child routers-</a></h3>

Si no has leído la guía [Get Started](/get-started.html), te recomendamos que lo hagas ahora y que pongas especial atención en la sección titulada "Bonificación: usando enrutadores hijos.

Siempre que configures una ruta para mapear a un modelo, ese modelo puede en realidad contener su propio enrutador... y cuando configuras rutas con él... esos modelos pueden contener sus propios enrutadores... y así sucesivamente. Los patrones de rutas son relativos al enrutador padre y las IDs de módulos y vistas son relativas al modelo mismo. Esto te permite encapsular fácilmente características o aplicaciones hijas así como gestionar un estado jerárquico complejo.

Un enrutador hijo es solo un enrutador como otro cualquiera. Así que todo lo expuesto anteriormente es de aplicación. Para añadir un enrutador hijo, simplemente solicita que sea inyectado un `Router` y configúralo con tus rutas hijas. El ciclo de vida de activación de pantalla explicado más arriba se aplica también a los enrutadores hijos. Cada fase del ciclo de vida es ejecutado contra la jerarquía de enrutadores completa antes de pasar a la siguiente fase. Los ganchos de activación se ejecutan de arriba abajo y los ganchos de desactivación se ejecutan de abajo arriba.

<h3 id="conventional-routing"><a href="#conventional-routing">Enrutamiento convencional -conventional routing-</a></h3>

Como todo en Aurelia, tenemos un fuerte soporte para convenciones. De esta manera, en realidad puedes optar por enrutar dinámicamente en lugar de preconfigurar todas tus rutas previamente. Aquí tienes como configurar el enrutador para hacer esto:

```javascript
export class App {
  configureRouter(config){
    config.mapUnknownRoutes(instruction => {
      //check instruction.fragment
      //set instruction.config.moduleId
    });
  }
}
```

Todo lo que tienes que hacer es establecer la propiedad `config.moduleId` y estarás listo. También puedes devolver una promesa desde `mapUnknownRoutes` para determinar el destino asíncronamente.

>**Nota:** Aunque no necesariamente relacionado con el enrutamiento convencional, puedes necesitar a veces configurar tu enrutador asíncronamente. Por ejemplo, puedes necesitar llamar a un servicio web para obtener permisos de usuario antes de establecer las rutas. Para hacer esto, implementa una retrollamada en tu modelo de enrutador llamada `configureRouter`. En esta retrollamada puedes configurar tu enrutador y devolver opcionalmente una promesa -`Promise`- si fuera necesario.

<h3 id="customizing-the-navigation-pipeline"><a href="#customizing-the-navigation-pipeline">Personalizando el procesamiento de rutas -customizing the navigation pipeline-</a></h3>

El procesado -pipeline- de rutas se compone de pasos separados que se ejecutan sucesivamente. Cada uno de estos pasos tiene la capacidad tanto de modificar lo que ocurre durante el enrutamiento, como de detenerlo. El procesado cuenta con algunos puntos para su ampliación por los que se nos permite añadir nuestros propios pasos. Están `` authorize` -autoriza- y `modelbind` -enlazamodelo-. `authorize` ocurre antes que `modelbind`. Estas extensiones se denominan filtros de rutas -route filters-.

El ejemplo a continuación muestra como añadir una autorización a tu aplicación:

```javascript
import {Redirect} from 'aurelia-router';

export class App {
  configureRouter(config) {
    config.title = 'Aurelia';
    config.addPipelineStep('authorize', AuthorizeStep); // Add a route filter to the authorize extensibility point.
    config.map([
      { route: ['welcome'],     moduleId: 'welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',        moduleId: 'flickr',       nav: true, auth: true },
      { route: 'child-router',  moduleId: 'child-router', nav: true, title:'Child Router' },
      { route: '',              redirect: 'welcome' }
    ]);
  }
}

class AuthorizeStep {
  run(routingContext, next) {
    // Check if the route has an "auth" key
    // The reason for using `nextInstructions` is because
    // this includes child routes.
    if (routingContext.nextInstructions.some(i => i.config.auth)) {
      var isLoggedIn = /* insert magic here */false;
      if (!isLoggedIn) {
        return next.cancel(new Redirect('login'));
      }
    }

    return next();
  }
}
```

Estos puntos de ampliación -extensibility points- son en si mismos pequeños procesados -pipelines- que nos permiten añadir múltiples pasos a cada uno de ellos. Por ejemplo, si ,además de `AuthorizeStep` -PasoAutorizar- (que solo comprobaría que el usuario ha accedido), podrías añadir un `IsAdminStep` -PasoEsAdmin- al punto de ampliación `authorize`. Estos se ejecutarían sucesivamente.

También es posible crear tus propios filtros con nombre simplemente pasando un nombre diferente a `addPipelineStep` -añadirPasoAlProcesado-. Esto lo puedes usar de la siguiente manera:

```javascript
config.addPipelineStep('myname', MyFirstStep); // Transparently creates the pipeline "myname" if it doesn't already exist.
config.addPipelineStep('myname', MySecondStep); // Adds another step to it.
config.addPipelineStep('modelbind', 'myname'); // Makes the entire `myname` pipeline run as part of the `modelbind` pipeline.
```

<h3 id="configuring-push-state"><a href="#configuring-push-state">Configurando PushState</a></h3>

Si prefieres desprenderte de los `#` (almohadilla) en tus URLs, entonces vas a tener que habilitar `pushState` en tu aplicación. ¡Por suerte Aurelia admite esto! Tendrás que hacer algún ajuste también del lado del servidor para que esto funcione correctamente. Empecemos por el lado de Aurelia en la ecuación.

Primero necesitas decirle a Aurelia en el `router` `config` que quieres usar `pushState` de esta manera:

``` javascript
export class App {
  configureRouter(config) {
    config.title = 'Aurelia';
    config.options.pushState = true; // <-- this is all you need here
    config.map([
      { route: ['welcome'],     moduleId: 'welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',        moduleId: 'flickr',       nav: true, auth: true },
      { route: 'child-router',  moduleId: 'child-router', nav: true, title:'Child Router' },
      { route: '',              redirect: 'welcome' }
    ]);
  }
}
```

También querrás añadir una [etiqueta base](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) en la cabeza (head) de tu documento HTML. Esta es importante, así que no la dejes fuera.

A continuación, el lado del servidor tiene que ser configurado para devolver el mismo archivo `index.html` independientemente de la petición que se haga desde el lado del cliente. Asi que, si estás usando la tarea `gulp watch` con `browsersync` como en el ejemplo de navegación , entonces puedes modificar tu configuración de esta manera:

Desde la consola en la raíz del proyecto, ejecuta lo siguiente:

```shell
npm install --save connect-history-api-fallback
```

Esto descargará e instalará el componente de intermediación -middleware- que necesitas para ello. Luego abre tu carpeta _build/tasks_ y coloca la tarea _serve_. Abre la primera y pon esta en algún sitio cerca del inicio junto a los otros sentencias `require`:

``` javascript
var historyApiFallback = require('connect-history-api-fallback')
```

Más abajo puedes modificar la tarea `serve` para usar el complemento de intermediación -middleware-:

``` javascript
gulp.task('serve', ['build'], function(done) {
  browserSync({
    open: false,
    port: 9000,
    server: {
      baseDir: ['.'],
      middleware: [historyApiFallback, function (req, res, next) { // it's the first one in the array
        res.setHeader('Access-Control-Allow-Origin', '*');
        next();
      }]
    }
  }, done);
});
```

Ahora tu servidor node puede comportarse tal cual es y dejar que Aurelia se ocupe del enrutamiento.

Si estás usando un marco de trabajo .NET del lado del servidor como ASP.NET MVC, entonces configuralo de esta manera:

* Crea un `Controller` y llámalo `ApplicationController` o como a tí te apetezca. Debe quedar algo así:

```csharp
public class ApplicationController : Controller {
  public ActionResult Index() {
    return View();
  }
}
`
``
* Crea una vista "index.cshtml" en tu carpeta `View`.

* Configura tu sistema de enrutado -routing- así:

```csharp
context.MapRoute(
  name: "AureliaRouting",
  url: "{*.}",
  defaults: new { controller = "Application", action = "Index" }
);
```
Ten en cuenta que lo anterior te obliga a usar un archivo de vista Razor. Si quieres usar un archivo HTML convencional, hay diferentes maneras de hacerlo. [Este artículo de SO te ayudará a hacerlo](http://stackoverflow.com/questions/20871938/render-html-file-in-asp-net-mvc-view).

Si estás usando [Nancy FX](http://nancyfx.org), entonces la configuración es así de simple. Localiza tu `IndexModule.cs` o comoquiera que lo hayas llamado y asegúrate de que se asemeja a esto y todo irá bien:

```csharp
public class IndexModule : NancyModule {
  public IndexModule()     {
    this.Get["/robots.txt"] = p => this.Response.AsFile("robots.txt");
    this.Get["/sitemap.xml"] = p => this.Response.AsFile("sitemap.xml");
    this.Get["/"] = x => this.View["index"];
    this.Get["/{path*}"] = x => this.View["index"];
  }
}
```

Técnicas similares las puedes emplear en entornos con otros servidores - solo necesitas asegurarte de que cualquiera que sea el servidor que estás usando, este devuelve siempre el mismo `index.html` independientemente de la petición que reciba. Todos los marcos de trabajo del lado del servidor deberían ser capaces de esto. Aurelia encontrará la página que ha de devolver en base a los datos de enrutamiento.

<h3 id="reusing-an-existing-vm"><a href="#reusing-an-existing-vm">Reutilizando un modelo de vista existente</a></h3>

A veces puedes querer usar el mismo modelo-vista (VM, view-model) para varias rutas. Por defecto Aurelia verá esas rutas como alias del mismo modelo-vista para múltiples rutas y así solo realiza el proceso de construcción y añadido, así como un ciclo de vida completo. Puede que esto no sea exactamente lo que estás buscando. Mira el siguiente ejemplo de enrutador:

```javascript
export class App {
  configureRouter(config) {
    config.title = 'Aurelia';
    config.map([
      { route: 'product/a',         moduleId: './product'      nav: true },
      { route: 'product/b',         moduleId: './product',     nav: true },
    ]);
  }
}
```

Puesto que el ciclo de vida del modelo-vista (VM) es llamado solo una vez puedes tener problemas para reconocer que el usuario cambie la ruta de `Product A` a `Product B`.

Para corregir esto implementa el método `determineActivationStrategy` en tu modelo-vista (VM) y devuelve indicaciones para el enrutador acerca de que es lo que quieres que ocurra. En este ejemplo, para forzar una reconstrucción del modelo-vista impleméntalo como este:

```javascript
import {activationStrategy} from 'aurelia-router';

export class YourViewModel {
  determineActivationStrategy(){
    return activationStrategy.replace;
  }
}
```

Si solo deseas forzar un refresco del ciclo de vida (útil con enlaces `<compose>`) puedes hacer algo como esto:

```javascript
import {activationStrategy} from 'aurelia-router';

export class YourViewModel {
  determineActivationStrategy(){
    return activationStrategy.invokeLifecycle;
  }
}
```

> **Nota:** Recuerda que forzando los refrescos, Aurelia tiene que reconstruir el modelo-vista completo. Por razones de eficiencia un simple observador sobre `router.currentInstruction` podría ser suficiente para escenarios en los que simplemente te gustaría intercambiar algunos datos.

<h2 id="extending-html"><a href="#extending-html">Extendiendo HTML</a></h2>

Aurelia tiene un compilador de plantillas HTML poderoso y extensible. El compilador en si es un algoritmo para interactuar con estas extensiones del HTML. De entrada, Aurelia proporciona dos extensiones: _Custom Elements_ elementos a medida, y _Custom Attributes_ atributos a medida.

Por defecto, estas extensiones no son visibles para el compilador. Hay tres vías principales para incorporarlos:

* Usar el elemento `require` para requerir una extensión en una vista. El atributo `from` especifique la ruta relativa al módulo de extensión. La extensión será definida localmente.
* Usar el objeto Aurelia durante tu fase de configuración inicial -bootstrapping- para llamar  `.globalizeResources(...resourcePaths)` para registrar extensiones con visibilidad global en tus aplicaciones.
* Instala un complemento que registre extensiones con visibilidad global en tu aplicación.

>**Nota:** Una práctica recomendada para tu propia aplicación es colocar todos tus comportamientos específicos de aplicación, convertidores de valores, etc. en una carpeta _resources_. Luego crear un archivo _index.js_ que lo convierte todo en un complemento interno. Finalmente, instala ese complemento durante la fase de configuración inicial -bootstrapping- de la aplicación. Esto mantiene todos los recursos en una ubicación conocida, junto a su código de registro. Esto además mantendrá tu archivo de configuración limpio y sencillo.

Todos los comportamientos pueden incorporarse opcionalmente al ciclo de vida de vista implementando cualquiera de los ganchos siguientes:

* `bind(bindingContext)` - Invocado cuando el motor de enlazado de datos enlaza la vista. El contexto de enlazado es el objeto al que está enlazada la vista.
* `unbind()` - Invocado cuando el motor de enlazado de datos desvincula la vista.
* `attached()` - Invocado cuando la vista que contiene esa extensión es añadida al DOM.
* `detached()` - Invocado cuando la vista que contiene esa extensión es eliminada del DOM.

>**Nota:** Si decides implementar la retro-llamada `bind`, el enlazado inicial de tu extensión fluirá de manera un poco distinta. Habitualmente, si tienes retrollamadas a las propiedades enlazables de tus extensiones, estas son llamadas una a una durante la fase de enlazado. Pero si añades la retro-llamada `bind`, dichas propiedades no serán llamadas durante la inicialización, sino que, la retrollamada `bind` será llamada una vez que todas las propiedades tengan asignados sus valores enlazados iniciales. Esta es una importante y útil característica, en particular para extensiones complejas que puedan querer no "actuar" hasta que no estén disponibles todos los valores evaluados.

<h3 id="custom-attributes"><a href="#custom-attributes">Atributos a medida -Custom Attributes-</a></h3>

Los atributos a medida -Custom Attributes- añaden nuevo comportamiento a elementos HTML. Usos comunes para atributos a medida añadidos incluyen:

* Envolver complementos jQuery y similares (cuando el comportamiento global -`global-behavior`- es insuficiente).
* Atajos para enlazados comunes a estilo, clases o atributos.
* Simplemente todo que requiera cambiar un elemento HTML existente o incluso un elemento a medida -Custom Element- que no puedes modificar directamente.

Los atributos a medida tienden a representar competencias transversales. Por ejemplo, puedes crear un atributo recomendado a medida que puedes añadir a cualquier elemento. Esta idea es mejor que la de crear la funcionalidad recomendada directamente en cada elemento a medida que crees.

Veamos la implementación de uno de los atributos a medida (propio) de Aurelia: `show`. Aquí tienes como se usa:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

El atributo `show` aplicará condicionalmente una clase a un elemento en función de la falsedad de su valor. (La clase, cuando es aplicada, oculta el elemento.) Aquí está la implementación:

```javascript
import {inject, customAttribute} from 'aurelia-framework';

@customAttribute('show')
@inject(Element)
export class Show {
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

Lo primero a destacar es que los atributos a medida son clases y siguen los mismos patrones que hemos visto anteriormente. Observa que los decoradores -decorators- juegan un importante papel en la definición de un atributo. Aquí tienes lo que hacen:

* `@customAttribute('show')` - Indica que esta clase es un atributo a medida de manera que el compilador HTML sepa como se integra esta clase. Este atributo será reconocido por el compilador cada vez que encuentre un atributo con el nombre `show`. 
* `inject` - Esto forma parte del sistema de inyección de dependencias; lo mismo que hemos visto antes. Los atributos a medida pueden recibir inyectado en sus constructores el elemento sobre el que están definido. Eso es lo que ocurre aquí. Todo lo que tienes que hacer es usar el tipo `Element` del navegador para indicarlo.

Hay algunas otras cosas que tienen lugar.

* Los atributos en HTML tienen un valor. Por lo tanto, tu clase de elemento a medida tendrá una propiedad que se mantendrá sincronizada con el HTML. Si implementas un método `valueChanged` -valor modificado-, este será invocado cada vez que el valor del atributo cambie. Su primer argumento será el nuevo valor y el segundo será el antiguo. 

Bien. Hablemos de convenciones.

Si el nombre de exportación de tu clase cumple el patrón {UnNombre}AtributoAMedida, entonces no necesitas incluir el decorador `@customAttribute`. El nombre del atributo será inferido del nombre de exportación de la clase eliminando "AtributoAMedida", escribiendo en minúsculas y con guión la parte restante del nombre, p.ej. "un-nombre".

Estas convenciones significan que podemos definir nuestro comportamiento `show` de esta manera:

```javascript
export class ShowCustomAttribute {
  static inject = [Element]; //showing non-decorator method here for variety

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

> **Nota:** ¿Y por qué no aprovecha Aurelia mismo internamente estas convenciones? Siempre que estemos creando una librería externa, lo mejor es ser explícitos. Tu no sabes si los desarrolladores que utilicen tu librería van a cambiar o no las convenciones de Aurelia, quebrando así tu librería. Para evitar esto, se siempre explícito usando decoradores. Dentro de tus propias aplicaciones puedes usar las convenciones que quieras para simplificar el desarrollo.

<h4 id="options-properties"><a href="#options-properties">Atributos de opciones -options attributes-</a></h4>

Puede que te preguntes que hacer si quieres crear un atributo a medida con varias propiedades, teniendo en cuenta que habitualmente los atributos se corresponden con un único valor. Aquí tienes un ejemplo de como se usa:

```javascript
import {customAttribute, bindable} from 'aurelia-framework';

@customAttribute('my-attribute')
export class MyAttribite {
  @bindable foo;
  @bindable bar;
}
```

Esto crea un atributo a medida de nombre `my-behavior` con dos propiedades `foo` and `bar`. Cada una de estas propiedades están disponibles directamente en la clase. Ambas pueden tener (funciones) retrollamada para modificarlas, `fooChanged` y `barChanged` respectivamente. En cualquier caso su configuración en HTML es algo distinta. Aquí está como lo haríamos:

```markup
<div my-attribute="foo: some literal value; bar.bind: some.expression"></div>
```

Fíjate en que no usamos una orden de enlace directamente sobre el atributo. En lugar de eso, lo usamos sobre cada propiedad individual dentro del valor del atributo. Puedes utilizar literales u órdenes de enlazado estándar.

>**Note:** No se usan las órdenes `delegate` y `trigger` dentro de un atributo de opción. Estas están siempre añadidas al elemento mismo, puesto que trabajan directamente con eventos nativos del DOM. En cambio si puedes usar `call`.

Si no estás usando inicializadores de propiedad de ES7, puedes añadirle el decorador `@bindable` directamente a la clase. Unicamente asegúrate de proporcionar un nombre de propiedad del tipo `@bindable('nombrePropiedad')`. Para especificar más detalles de una propiedad enlazable, necesitas pasar un objeto de opciones como el siguiente:

```javascript
@bindable({
  name:'myProperty', //name of the property on the class
  attribute:'my-property', //name of the attribute in HTML
  changeHandler:'myPropertyChanged', //name of the method to invoke when the property changes
  defaultBindingMode: bindingMode.oneWay, //default binding mode used with the .bind command
  defaultValue: undefined //default value of the property, if not bound or set in HTML
})
```

Los valores por defecto y convenciones se muestran arriba. De esta manera, solo necesitas especificar estas opciones si tienes que desviarte de ello.

> **Nota:** También existe un decorador especial `@dynamicOptions`. Este permite que un atributo a medida disponga de un conjunto dinámico de propiedades que se mapean desde la sintaxis de las opciones de atributo a la clase en tiempo de ejecución. No declares propiedades `bindable` -enlazables-. Simplemente añade un solo decorador `@dynamicOptions` y cualquier cosa que el usuario liste en la sintaxis de opciones de atributo se mapeara.

> **Nota:** Recuerda que todos los decoradores disponibles se encuentran en el auxiliar `Decorators` -decoradores- y puede especificarse usando una propiedad o método estáticos como decoradores si lo prefieres (o si estás usando un lenguaje que no soporta decoradores). Veanse más arriba los ejemplos con CoffeeScript.

<h4 id="template-controllers"><a href="#template-controllers">Controles de plantillas -Template Controllers-</a></h4>

Los atributos a medida pueden indicar que se trata controles de plantilla con el decorador `@templateController`. Esto indica que transforman el DOM en una plantilla HTML inerte. La clase del atributo a medida puede luego decidir cuando y donde (o cuantas veces) insertar y desplegar la plantilla en el DOM. Ejemplos de esto son los atributos `if` y `repeat`. Coloca simplemente uno de ellos en un nodo DOM y este se convertirá en una plantilla, controlada por la clase del atributo a medida.

Echemos un vistazo a la implementación del atributo a medida `if` para ver como se construye uno. Aquí está el código fuente completo:

```javascript
import {BoundViewFactory, ViewSlot, customAttribute, templateController, inject} from 'aurelia-framework';

@customAttribute('if')
@templateController
@inject(BoundViewFactory, ViewSlot)
export class If {
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

Antes de adentrarnos en los aspectos únicos, déjame recordarte la parte de ello que es compartida. Primero tenemos simplemente una clase con decoradores. Además por defecto también tiene una propiedad de valor único que podemos observar añadiendo una retro-llamada `valueChanged` -valor modificado-.

Bien, y que distinto? Echemos un vistazo al constructor. Tenemos dos elementos únicos inyectados: `BoundViewFactory` y `ViewSlot`.

`BoundViewFactory` es capaz de generar objetos a partir de las plantillas HTML a las que está vinculada el atributo. No hay necesidad de preocuparse por la compilación, etc. Todo eso ya se ha tenido en cuenta. ¿Por que se llama `BoundViewFactory` -factoría de vista vinculada-? Bien, ya se refiere al contexto antecesor de enlazado. Por lo tanto, en un sentido está vinculado. Así que si llamas a su método `create` este generará una nueva vista -View- a partir de la plantilla que será enlazada a dicho contexto. Esto es lo que buscamos con un atributo `if`. Esto no es lo que quieres con un atributo `repeat`. En este caso, cada vez que llamas a `create` quieres generar una nueva vista vinculada a cada uno de los elementos del vector. Para conseguir esto, simplemente pasa el objeto que deseas vincular a la vista en el método `create`.   

`ViewSlot` representa el hueco o localización en el DOM desde la que se extrajo la plantilla. Normalmente se trata de la localización en la que deseas añadir los objetos del tipo View.

> **Nota:** A diferencia de atributos anteriores, un controlador de plantilla trabaja más directamente con las primitivas del marco de trabajo (Aurelia). Views, ViewFactories y ViewSlots son todos componentes de bajo nivel del motor de plantillas.

Echa un vistazo con más detenimiento a la función (retro-llamada) `valueChanged`. Aquí puedes ver donde está creando el atributo `if` la vista y añadiendola al hueco, en función de la verdad del valor. Hay unos cuantos detalles importantes de esto:

* El atributo siempre llama a `bind` en la vista _antes_ de añadirla al hueco -ViewSlot- correspondiente. Esto asegura que todos los enlaces internos son inicialmente evaluados fuera del DOM visible -live-. Esto es importante para el rendimiento.
* Análogamente, siempre llama a `unbind` _después_ de eliminar la vista del DOM.
* Después de que la vista sea creada inicialmente, el atributo `if` no la deshecha aunque el valor resulte falso. La almacena temporalmente en memoria -cache-. Aurelia puede reusar las vistas e incluso redirigirlas a diferentes contextos de enlazado. De nuevo, esto es importante para el rendimiento, puesto que se elimina la re-creación innecesaria de vistas.


<h3 id="custom-elements"><a href="#custom-elements">Elementos a medida -Custom Elements-</a></h3>

Los elementos a medida añaden nuevas etiquetas a tu marcado HTML. Cada elemento a la medida pueden tener su propia plantilla de vista puede desplegarse bien en el Light DOM o en el Shadow DOM. Los elementos a medida también pueden tener cualquier número de propiedades que aflorarán como atributos en HTML para dar soporte al enlazado de datos y a los cuales pueden vincularse dentro de su plantilla de vista.

¿Por qué no creamos un sencillo elemento a medida para ver como funciona esto? Vamos a crear un elemento que salude a alguien, llamado `say-hello`. Aquí está como queremos poder usarlo una vez que esté hecho:

```markup
<template>
    <require from="./say-hello"></require>

    <input type="text" ref="name">
    <say-hello to.bind="name.value"></say-hello>
</template>
```

Así que, como lo construimos? Bien, empezamos con una clase, tal y como hicimos con el comportamiento añadido. Aquí está el código:

#### say-hello.js
```javascript
import {customElement, bindable} from 'aurelia-framework';

@customElement('say-hello')
export class SayHello {
  @bindable to;

  speak(){
    alert('Hello ${this.to}!');
  }
}
```

Si leíste la sección sobre atributos a medida, entonces ya sabes lo que hace esto. Aquí también hay algunas convenciones, lo que significa que podríamos hacer eso si quisiéramos:

#### say-hello.js (with conventions)
```javascript
import {bindable} from 'aurelia-framework';

export class SayHelloCustomElement {
  @bindable to;

  speak(){
    alert('Hello ${this.to}!');
  }
}
```

Por defecto, los elementos a medida tienen una vista. Aquí está la del nuestro:

#### say-hello.html
```markup
<template>
    <button click.delegate="speak()">Say Hello To ${to}</button>
</template>
```

Como puedes ver, contamos con acceso a las propiedades y métodos de nuestra clase. Es importante señalar que no necesitas declarar propiedades `@bindable` -enlazable- para cada propiedad con la que deseas establecer un enlace en tu plantilla. Solo necesitas declararlos para propiedades que quieres que existan como atributos en tu elemento a medida.

Esto es todo realmente. Sigue las mismas convenciones para nombres que con las parejas vista/modelo y todos los mismos patrones para los elementos a medida. Hay algunos pocos decoradores para los elementos a medida que puedes necesitar:

* `@syncChildren(propiedad, gestorCambio, selector)` - Crea una propiedad vector en tu clase cuyos elementos son sincronizados automáticamente con su vista en base a un selector de búsqueda.
*  `@skipContentProcessing` - Le dice al compilador que no procese el contenido de tu elemento a medida. Se supone que vas a realizar tu propio procesamiento a medida.
*  `@useView(path)` - Especifica una vista diferente a usar.
*  `@noView` - Indica que este elemento a medida no tiene una vista y que el autor pretende que el elemento gestione su propia representación internamente.

<h2 id="eventing"><a href="#eventing">La gestión de eventos -Eventing-</a></h2>

El sistema de gestión de eventos es una poderosa herramienta cuando necesitas que componentes inconexos de tu aplicación se comuniquen entre si. Aurelia soporta tanto eventos estándar del DOM como eventos más específicos de aplicación a través de `EventAggregator` (agregador de eventos).

<h3 id="dom-events"><a href="#dom-events">Eventos del DOM -DOM Events-</a></h3>

Los eventos DOM deben usarse cuando han de enviarse mensajes específicos de la interfaz de usuario. No deben ser usados para enviar mensajes específicos de la aplicación. Aurelia no añade ninguna funcionalidad más allá del DOM para eventos de interfaz de usuario (por ahora). Cualquier atributo o elemento a medida puede tener su elemento -`Element`- asociado inyectado en su constructor. Entonces puedes usar el `Element` para lanzar eventos. Para aprender más acerca de la creación y el lanzamiento de eventos DOM a medida [lee por favor este artículo](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events).

<h3 id="the-event-aggregator"><a href="#the-event-aggregator">El agregador de eventos</a></h3>

Si necesitas eventos de aplicación acoplados débilmente, querrás usar el `EventAggregator`. Su interfaz pub/sub simplificada lo hace ideal para un amplio rango de escenarios de gestión de mensajes.

El agregador de eventos puede publicar eventos en un canal de mensajes o puede publicar mensajes fuertemente tipificados. Primero echemos un vistazo a la publicación por canales:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class APublisher{
    static inject = [EventAggregator];
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    publish(){
        var payload = {}; //any object
        this.eventAggregator.publish('channel name here', payload);
    }
}
```

Empezamos por recibir a través de la inyección de dependencias una función singular -singleton- Event Aggregator. A continuación llamamos a su método `publish`, pasándole el nombre del canal de mensajes y el conjunto de datos a enviar por ese canal. Aquí está como se auto-configura un suscriptor para recibir por determinado canal:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class ASubscriber{
    static inject = [EventAggregator];
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    subscribe(){
        this.eventAggregator.subscribe('channel name here', payload => {
            //do something with the payload here
        });
    }
}
```

Como puedes ver, se usa el mismo nombre de canal, pero se proporciona una función de retro-llamada, que será invocada con cada mensaje enviado por este canal.

Alternativamente, puedes publicar y suscribirte a mensajes fuertemente tipificados. Aquí tienes un ejemplo de editor -publisher:

```javascript
export class SomeMessage{ }
```

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';
import {SomeMessage} from './some-message';

export class APublisher{
    static inject = [EventAggregator];
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    publish(){
        this.eventAggregator.publish(new SomeMessage());
    }
}
```

En este caso, publicamos un mensaje de uno de los tipos en particular. Aquí tienes un suscriptor de ejemplo:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';
import {SomeMessage} from './some-message';

export class ASubscriber{
    static inject = [EventAggregator];
    constructor(eventAggregator){
        this.eventAggregator = eventAggregator;
    }

    subscribe(){
        this.eventAggregator.subscribe(SomeMessage, message => {
            //do something with the message here
        });
    }
}
```

El (receptor) suscriptor -subscriber- será llamado cada vez que se publica un objeto del tipo `SomeMessage`. La suscripción -subscription- es polimórfica, así que si se publica un objeto de un tipo subclase de `SomeMessage`, este (suscriptor) también recibirá notificación de ello.

>**Nota:** Todas las formas del método `subscribe` devuelven una _función desechar_ -dispose function-. Puedes llamar a esta función para desechar la suscripción y dejar de recibir mensajes. Un lugar adecuado para desechar (una suscripción) es en una (función de) de retrollamada `deactivate` -desactivar- del modelo, si este es manejado por un enrutador, o en su retrollamada `detached`, si se encuentra en cualquier otro modelo de vista.

<h2 id="http-client"><a href="#http-client">Cliente HTTP</a></h2>

Como ventaja, Aurelia incluye un cliente HTTP `HttpClient` básico para proporcionarnos una interfaz confortable para el objeto `XMLHttpRequest` del navegador. `HttpClient` no está incluido en el módulo que instala Aurelia con el configurador inicial -bootstrapper-, ya que es completamente opcional y muchas aplicaciones pueden querer usar una estrategia diferente para la recuperación de datos. Así, si quieres usarlo, primero necesitas instalarlo con la orden siguiente:

```shell
jspm install aurelia-http-client
```

Después puedes usarlo de esta manera:

```javascript
import {HttpClient} from 'aurelia-http-client';

export class WebAPI {
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }

    getAllContacts(){
        return this.http.get('url goes here');
    }
}

```

El `HttpClient` -cliente HTTP- tiene la siguiente implementación:

```javascript
export class HttpClient {
  configure(fn){
    var builder = new RequestBuilder(this);
    fn(builder);
    this.requestTransformers = builder.transformers;
    return this;
  }

  createRequest(url){
    let builder = new RequestBuilder(this);

    if(url) {
      builder.withUrl(url);
    }

    return builder;
  }

  delete(url){
    return this.createRequest(url).asDelete().send();
  }

  get(url){
    return this.createRequest(url).asGet().send();
  }

  head(url){
    return this.createRequest(url).asHead().send();
  }

  jsonp(url, callbackParameterName='jsoncallback'){
    return this.createRequest(url).asJsonp(callbackParameterName).send();
  }

  options(url){
    return this.createRequest(url).asOptions().send();
  }

  put(url, content){
    return this.createRequest(url).asPut().withContent(content).send();
  }

  patch(url, content){
    return this.createRequest(url).asPatch().withContent(content).send();
  }

  post(url, content){
    return this.createRequest(url).asPost().withContent(content).send();
  }
}
```

Como puedes comprobar, nos proporciona métodos preparados para todas las acciones estándar, así como para `jsonp`. Cada uno de estos métodos envía un `HttpRequestMessage` -mensaje de petición HTTP- excepto `jsonp` que envía un `JSONPRequestMessage` -mensaje de petición JSON-. El resultado de enviar un mensaje es una `Promise` -promesa- de un `HttpResponseMessage` -mensaje de respuesta HTTP-.

El `HttpResponseMessage` -mensaje de respuesta HTTP- tiene las siguientes propiedades:

* `response` - Devuelve en crudo el contenido enviado por el servidor.
* `responseType` - El tipo de respuesta previsto.
* `content` - Formatea el contenido crudo de la `response` -respuesta- en base al `responseType` -tipo de respuesta- y lo devuelve.
* `headers` - Devuelve un objeto `Headers` -cabeceras- con los datos interpretados de la cabecera.
* `statusCode` - El código de estado de la respuesta del servidor.
* `statusText` - El mensaje de texto del estado del servidor.
* `isSuccess` - Indica si el código de estado está dentro del rango de éxito o no.
* `reviver` - Una función usada para transformar el contenido crudo de la `response` -respuesta-.
* `requestMessage` - Una referencia al mensaje de petición original.

> **Nota:** Por defecto, el `HttpClient` -cliente HTTP- asume que estás esperando una respuesta de tipo JSON -responseType-.

Hay otras dos APIS que merecen ser destacadas. Puedes usar `configure` para acceder a una fluent API para configurar todas las peticiones enviadas por el cliente. También puedes usar `createRequest` para configurar personalmente peticiones individuales. Aquí tienes un ejemplo de configuración:  

```javascript
var client = new HttpClient()
  .configure(x => {
    x.withBaseUrl('http://aurelia.io');
    x.withHeader('Authorization', 'bearer 123');
  });

client.get('some/cool/path');
```

En este caso, todas las peticiones del cliente tendrán la baseUrl de 'http://aurelia.io' y tendrán el encabezamiento de autorización especificado. La misma API está disponible por medio del constructor de peticiones. Así que puedes hacer lo mismo con una petición individual de esta manera:

```javascript
var client = new HttpClient();

client.createRequest('some/cool/path')
  .asGet()
  .withBaseUrl('http://aurelia.io')
  .withHeader('Authorization', 'bearer 123')
  .send();
`````

La fluent API tiene los siguientes métodos enlazables: `asDelete()`, `asGet()`, `asHead()`, `asOptions()`, `asPatch()`, `asPost()`, `asPut()`, `asJsonp()`, `withUrl()`, `withBaseUrl()`, `withContent()`, `withParams()`, `withResponseType()`, `withTimeout()`, `withHeader()`, `withCredentials()`, `withReviver()`, `withReplacer()`, `withProgressCallback()`, y `withCallbackParameterName()`.

<h2 id="customization"><a href="#customization">Ejecución a medida -customization-</a></h2>

<h3 id="view-and-view-model-conventions"><a href="#view-and-view-model-conventions">Convenciones relativas a las vistas y a los modelos (de vista)</a></h3>

¿Como se enlazan vistas y modelos? Nuestra sencilla convención se basa en la identidad de módulo -module id-. Si cuentas con un modelo de identidad `./foo/bar/baz` (su ruta básicamente) esté será mapeado por defecto con `./foo/bar/baz.js` y `./foo/bar/baz.html`. Supón que quieres seguir otra convención diferente. ¿Que pasa si todos tus modelos están en una carpeta `view-models` y quieres que tus vista estén en una carpeta `views`? ¿Que tendrías que hacer? Para esto necesitas modificar el comportamiento de la estrategia convencional de vistas -Conventional View Strategy-. Así es como lo harías:

```javascript
import {ConventionalViewStrategy} from 'aurelia-framework';

ConventionalViewStrategy.convertModuleIdToViewUrl = function(moduleId){
  return moduleId.replace('view-models', 'views') + '.html';
}
```

Este código tendrías que ejecutarlo como parte de tu lógica de configuración inicial -bootstrapping configuration logic- para que surtiera efecto antes de que se carguen los comportamientos. Esto afectará a *todo* incluido a los elementos a medida. Así que si necesitas o quieres que estos actúen de otra manera, tendrás que tenerlo en cuenta en tu implementación de `convertModuleIdToViewUrl`.

>**Nota:** Este es un ejemplo de por que los autores de complementos externos no deben fiarse de las convenciones. Los desarrolladores pueden modificar estas convenciones para adaptarse a las necesidades de su propia aplicación.
