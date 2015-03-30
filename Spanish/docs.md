# Documentación

Hemos planeado para Aurelia un conjunto muy rico de documentos. Desafortunadamente aún no los hemos terminado. En cualquier caso, para este temprano periodo de vista previa, hemos compuesto este documento, que contiene ejemplos de las tareas más comunes que puedas necesitar. Si tienes preguntas, espero que te unas a nosotros en nuestro [canal en gitter](https://gitter.im/aurelia/discuss).

>**Nota:** ¿Estás buscando esta guía en otro idioma? Echa un vistazo a nuestro repositorio [documentation](https://github.com/aurelia/documentation).

## Soporte a navegadores

Aurelia está diseñado para navegadores con actualización automática y permanente -Evergreen Browsers-. Esto incluye Chrome, Firefox, IE11 y Safari 8. Tal y como viene no funcionará en ninguna versión de IE anterior a la 11.

Si necesitas que Aurelia funcione con una versión de IE anterior a la 11 existe un repositorio [Aurelia Skeleton Nav IE Polyfill Test](https://github.com/devmondo/skeleton-navigation-IE-Polyfill-Test) que sirve como prueba de concepto usando ES6Shim. Este experimento fue realizado por la comunidad y parece permitir que (nuestro) marco de trabajo funcione sin problemas aparentes con IE10 e IE9. Esperamos investigar esto en el futuro con más detenimiento y ve si podemos elaborar una solución oficial. Te invitamos a experimentar con esto y a ayudarnos con la posibilidad de dar soporte a navegadores más antiguos.

## Arranque y configuración

Muchas plataformas tienen un punto de entrada o "main" para la ejecución del código. Aurelia no es diferente. Si has leído la página [Get Started](/get-started.html), entonces ya habrás visto el atributo `aurelia-app` . Simplemente, colócalo en algún elemento HTML y el configurador inicial -bootstrapper- de Aurelia cargará _app.js_ y _app.html_, los vinculará y los inyectará en el elemento del DOM en el que incluiste dicho atributo. Si no quieres usar esta convención, provee simplemente de un valor al atributo indicando que modelo (de vista) deseas cargar. Por ejemplo, `<body aurelia-app="todo">` hará que se carguen los archivos _todo.js_ y _todo.html_.

El atributo `aurelia-app` está bien para empezar, pero a menudo deseamos configurar nuestro marco de trabajo -framework- o ejecutar algún código antes de mostrarle nada al usuario. Así que cabe la posibilidad, según vaya progresando tu proyecto, de que tiendas a usar `aurelia-main`.

>**Nota:** Si estas usando AtScript, añade un atributo `atscript` al elemento DOM de tu aplicación. Si estás usando ES5 en lugar de ES6, añade un atributo `es5`. De está manera se activa la función que hace más sencillo el uso de estos lenguajes.

**¿Cual es la diferencia?**

`aurelia-app` genera un objeto de tipo aplicación Aurelia y lo pre-configura con el conjunto de opciones por defecto del marco de trabajo, después carga el modelo (de vista) de la aplicación. `aurelia-main` carga tu módulo de configuración particular, _main.js_ por defecto, luego invoca tu función `configure`, pasándole el objeto Aurelia que puedes usar entonces para configurar por ti mismo el marco de trabajo y decidir que, cuando y donde mostrar tu interfaz de usuario -UI-. Aquí tienes un ejemplo de archivo _main.js_:

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

Con excepción del complemento a medida -custom plugin-, este código se corresponde esencialmente con lo que `aurelia-app` hace normalmente por nosotros. Cuando cambias a `aurelia-main` tienes que configurar todo esto por ti mismo, pero también puedes instalar complementos a medida, montar el contenedor para inyección de dependencias con algunos servicios e instalar recursos globales que hayan de usarse en plantillas de vista -view templates-.

>**Nota:** Para activar AtScript en la configuración manual, haz la llamada `aurelia.use.atscript()` y para activar ES5, haz la llamada `aurelia.use.es5()`.

### Sistema de acceso -logging-

Aurelia tiene una abstracción sencilla para accesos que usa el propio marco de trabajo -framework-. Por defecto, no produce efectos -no-op-. La configuración anterior muestra como instalar un objeto appender que tomará más tarde los datos de acceso -log data- y los sacará por la consola. También puedes ver como se establece el nivel del registro. Las opciones disponibles para este ajuste son: `none`, `error`, `warn`, `info` y `debug`.

Puedes crear fácilmente tus propios objetos appender. Solo implementa una clase que se acomode a la interfaz de appender. La mejor forma de ver como hacerlo es estudiar nuestro propio [código fuente del appender para el registro de consola](https://github.com/aurelia/logging-console/blob/master/src/index.js) .

### Complementos -plugins-

Un _complemento_ -plugin- es solo un módulo con una función `install` exportada. Durante el arranque Aurelia cargará todos los módulos de complementos -plugin modules- y llamará a sus respectivas funciones `install`, pasándoles el objeto Aurelia de manera que puedan configurar correctamente el marco de trabajo. Los complementos  pueden opcionalmente devolver un objeto `Promise` -promesa- a través de su función `install` a fin de realizar tareas asíncronas de configuración. Cuando escribas un complemento asegúrate de seguir estas reglas:

1. Usa una estructura de directorio plana. No coloques ni comportamientos ni vistas en subdirectorios.
2. El nombre de archivo y el nombre del comportamiento tienen que coincidir.
3. Proporciona explícitamente todos los metadatos, incluyendo una estrategia para vistas de elementos a medida.

> **Nota:** Respecto a #2 y #3: No te fíes de las convenciones para nombres dentro de los complementos. No sabes como va a modificar el usuario de tu complemento las reglas de Aurelia. Los complementos de terceros deben ser explícitos para asegurarnos de que funcionarán correctamente en diferentes contextos.

#### Promesas -promises-

Aurelia usa por defecto promesas ES6 o un polyfill. En cualquier caso, puedes reemplazar esto con la excelente librería de promesa [Bluebird](https://github.com/petkaantonov/bluebird). Inclúyela simplemente en tu página antes de cualquier referencia a los otros scripts. Te proporcionará su propia implementación ajustada a los estándares de promesas -Promise- que actualmente es más rápida que la (implementación) nativa y tiene un mejor soporte para depuración. Además cuando se usa en combinación con el transpilador 6to5 (compilador de ES6 a ES5), puedes usar [coroutines](https://6to5.org/docs/usage/transformers/#bluebird-coroutines) para código asíncrono mejorado.

### El objeto Aurelia

Puesto que tanto un módulo _main_ a medida como los complementos realizan su trabajo interactuando con el objeto Aurelia, te mostramos una breve explicación de esta API en el código siguiente:

```javascript
export class Aurelia {
  loader:Loader; //the module loader
  container:Container; //the app-level dependency injection container
  use:Plugins; //the plugins api

  withInstance(type, instance):Aurelia; //DI helper method (pass through to container)
  withSingleton(type, implementation):Aurelia; //DI helper method (pass through to container)
  withResources(resources):Aurelia; //resource helper method

  start():Promise; //starts the framework, causing plugins to be installed and resources to be loaded
  setRoot(root, applicationHost):Promise; //set your "root" or "app" view-model and display it
}
```

## Vistas y modelos

En Aurelia, los elementos de interfaz de usuario están compuestos por parejas de _vista_ y _modelo_ . La _vista_ se escribe con HTML y se despliega en el DOM. El _modelo_ se escribe con JavaScript y provee datos y comportamiento a la _vista_. El motor de plantillas y/o la inyección de dependencias son responsables de la creación de estas parejas y la aplicación de un ciclo de vida previsible para el proceso. Una vez generado, el poderoso _sistema de enlace -databinding-_ enlaza las dos partes permitiendo que los cambios en los datos se reflejen en la _vista_ y viceversa.

### Inyección de dependencias

Modelos y otros elementos de interfaz, como los controles de plantilla -Template Controllers- y los comportamientos añadidos -Attached Behaviors-, son creados como clases que son llamadas por el marco de trabajo usando un contenedor de inyección de dependencias. El código escrito con este estilo es fácil de separar en módulos y de testear. En lugar de crear clases voluminosas, puedes desmenuzar las cosas en pequeños objetos que colaborarán para lograr un objetivo. Luego la inyección de dependencias podrá montar por nosotros el conjunto en tiempo de ejecución.

Para aprovechar la inyección de dependencias tienes simplemente que añadir algunos metadatos a la clase para que esta le indique al marco de trabajo lo que debe pasarle a su constructor (de clase). Aquí tienes un ejemplo de una modelo que depende de un objeto HttpClient de Aurelia.

```javascript
import {HttpClient} from 'aurelia-http-client';

export class CustomerDetail{
    static inject() { return [HttpClient]; }
    constructor(http){
        this.http = http;
    }
}
```

Solo necesitamos proporcionar un método estático llamado `inject` que devuelva un vector de cosas para inyectar.

> **Nota:** Si escribes en TypeScript o CoffeeScript, puedes usar una propiedad estática de tipo vector en lugar de un método. En ES5 puedes añadir la propiedad al constructor mismo. También puedes hacer esto con ES6, pero habilitamos la opción del método estático puesto que puede ser ubicado más cerca del constructor en Vanilla JS. Si estás usando AtScript, puedes en realidad sacar partido de la anotación de tipos -type annotations- mediante la definición de tu constructor de la siguiente manera: `constructor(http:HttpClient)`. (Antes de que esto funciones es necesario que incluyas el atributo `atscript` en el elemento que aloja nuestra aplicación o hacer una llamada `aurelia.use.atscript()` manualmente.)

Las dependencias en el vector de inyección no tienen por que ser solo del tipo constructor. También pueden ser objetos del tipo `resolvers`. Por ejemplo, echa un vistazo a esto:

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

El `lazy resolver` en realidad no nos proporciona un objeto del tipo `HttpClient`. En su lugar nos entrega una función que, al ser llamada, nos devolverá un objeto del tipo `HttpClient`. Hay diferentes `resolvers` disponibles y puedes crear los tuyos propios montando una clase que herede de `Resolver`. Aquí tienes una lista de lo que te proporcionamos:

* `Lazy` - Inyecta una función para evaluar relajadamente -lazily- la dependencia.
    * p.ej. `Lazy.of(HttpClient)`
* `All` - Inyecta un vector de todos los servicios registrados con la clave provista.
    * p.ej. `All.of(Plugin)`
* `Optional` - Inyecta un objeto de una clase solo si ya existe en el contenedor, en otro caso, null.
    * p.ej. `Optional.of(LoggedInUser)`
* `Parent` - Puentea el contenedor de inyección de dependencias e intenta inyectar un objeto guardado en un contenedor padre.
    * p.ej. `Parent.of(Router)`

Junto a estos `resolvers`, también puedes usar anotaciones `Registration` (de registro) para especificar el registro o ciclo de vida por defecto de un objeto. Por defecto, el contenedor de inyección de dependencias asume que todos son objetos singulares -singleton instance-; un objeto por contenedor. Sin embargo, puedes usar una anotación de registro -registration annotation- para cambiar esto. Aquí tienes un ejemplo:

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

Ahora, cada vez que se le solicita al contenedor de inyección de dependencias un objeto del tipo `CustomerDetail`, el contenedor devolverá un nuevo objeto, en lugar de uno único -singleton-. Los registros `Singleton` y `Transient` vienen con el paquete, pero puedes crear los tuyos propios escribiendo una clase que herede de `Registration`.

> **Nota:** Este último ejemplo introduce _metadata_ -metadatos- para proporcionar información sobre el contexto al marco de trabajo. Verás otra vez _metadata_ cuando hablemos acerca de los _comportamientos_ -behaviors-.

## Uso de plantillas -templating-

El motor de plantillas de Aurelia es responsable de cargar las vistas y sus recursos importados, compilando tu HTML para un rendimiento óptimo y mostrando la interfaz de usuario (UI) en pantalla. Para crear una vista, todo lo que tienes que hacer es escribir un archivo HTML que incluya un `HTMLTemplate`. Aquí tienes una vista sencilla:

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

De paso, esto te permite cargar dinámicamente hojas de estilo dependiendo de la vista e incluso componentes web -Web Components-.

Cada vez que quieras importar un recurso específico de Aurelia, ya sea un _Custom Element_ -elemento a medida-, _Attached Behavior_ -comportamiento añadido-, _Template Controller_ -control de plantillas- o _Value Converter_ -conversor de valores-, puedes usar en su lugar un elemento `import` dentro de tu vista. Aquí tienes un ejemplo:

```markup
<template>
  <import from='./nav-bar'></import>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

En este caso `nav-bar` es un _Custom Element_ -elemento a medida- de Aurelia que importamos para usarlo. Usar el elemento `import` de Aurelia hace que el canal para recursos del marco de trabajo procese el elemento importado, lo que tiene las siguientes ventajas:

* Evita duplicidades - El recurso se descarga una sola vez para toda la aplicación. Aunque otras vistas importen el mismo elemento, este no será descargado nuevamente.
* Compilado único - Las plantillas importadas de esta manera para elementos a medida son  compilados una única vez para la aplicación completa.
* Alcance local - El recurso importado solo es visible dentro de la vista que lo importa, reduciendo la posibilidad de conflictos entre nombres.
* Renombrado - Los recursos pueden ser renombrados al importarlos, si resulta que dos recursos ajenos con el mismo nombre son usados en la misma vista.
    - p.ej. `<import from="./nav-bar" as="foo-bar"></import>` - Ahora en vez de usar un elemento `nav-bar` puedes usar un elemento `foo-bar`. (Esto está basado en ES6 para el cual renombrar es considerado un sustitutivo al uso de un alias porque lo que hace es estrictamente renombrar el tipo.)
* Empaquetado - La importación puede señalar a un módulo con múltiples recursos que serán todos importados a la misma vista.
* Extensibilidad - Puedes definir nuevos tipos de recursos que cuando son importados de esta manera pueden ejecutar la carga (asíncronamente única) y el registro (único por vista) a medida.
* ES6 - El código es cargado por el cargador -loader- de ES6 en lugar de por el mecanismo _HTMLImport_, habilitándose todas las características y extensibilidad de tu cargador.

En tus vistas harás aprovechamiento frecuente de los diferentes tipos de recursos mencionados con anterioridad así como del enlazado de datos -databinding-.

>**Nota:** Puede que te preocupe lo tedioso de tener que importar cosas en cada vista. Recuerda que durante la fase de configuración inicial -bootstrapping- puedes configurar Aurelia para que los recursos globales estén disponibles en todas las vistas.

### Enlazado de datos -databinding-

El enlazado de datos te permite enlazar el estado y el comportamiento de un objeto Javascript y una vista HTML. Cuando se establece este enlace, cualquier cambio en las propiedades enlazadas puede sincronizarse en una o dos direcciones. Los cambios en el objeto JavaScript pueden reflejarse en la vista y los cambios en la vista pueden reflejarse en el objeto JavaScript. Para establecer este vínculo, harás uso de las órdenes de enlazado -binding commands- en tu HTML. Las órdenes de enlazado son claramente identificables por su uso de "." como una especie de operador de enlace. Siempre que un atributo HTML contenga un ".", el compilador pasará el nombre y el valor del atributo al lenguaje de enlazado para su interpretación. El resultado es una o más expresiones de enlazado capaces de establecer el enlace cuando se crea la vista.

Puedes ampliar el sistema con tus propias órdenes de enlazado, pero Aurelia proporciona una colección para cubrir los casos de uso más frecuentes.

#### bind, one-way, two-way & one-time

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

#### delegate, trigger & call

Las órdenes de enlace no solo sirven para conectar propiedades y atributos, sino que se pueden usar para lanzar comportamientos. Por ejemplo, para invocar un método en el modelo cuando se pulsa un botón de la vista, usarías la orden `trigger` de la siguiente manera:

```markup
<button click.trigger="sayHello()">Say Hello</button>
```

Cuando se pulsa el botón, se invoca el método `sayHello` en el modelo. Dicho esto, no resulta muy eficiente añadir gestores de eventos a cada elemento por separado, así que con frecuencia desearás emplear la delegación de evento -event delegation-. Para ello, usa la orden `.delegate`. Aquí está el mismo ejemplo, pero con delegación de evento:

```markup
<button click.delegate="sayHello()">Say Hello</button>
```

> **Nota:** Si no estás familiarizado con la delegación de eventos, se trata de una técnica que usa la naturaleza "burbujeante" de los eventos DOM. Cuando usamos `.delegate` se añade un único gestor de eventos al documento, en lugar de a cada elemento. Cuando se dispara el evento del elemento, este "asciende" a través del DOM hasta que alcanza el (nivel de) documento, donde es gestionado (por el gestor asignado). Esta es una manera más eficiente en memoria de gestionar eventos y se recomienda que lo uses como tu mecanismo por defecto.

Todo esto va de una u otra manera en contra de los eventos de DOM. Ocasionalmente puedes encontrarte con un comportamiento a medida de Aurelia que necesite directamente una referencia a tu función de forma que pueda ser invocada posteriormente. Para pasar una referencia de función, usa el enlazador `.`call` (puesto que el comportamiento la _llamará -call-_ más tarde):

```markup
<div touch.call="sayHello()">Say Hello</button>
```

Ahora el comportamiento asociado recibirá una función que podrá llamar para invocar tu código `sayHello()`.

#### Interpolación de cadenas

A veces necesitas vincular propiedades directamente dentro del contenido del documento o intercalarlas dentro de un valor de atributo. Para ello puedes usar la sintaxis para interpolación de cadenas  `${expresión}`. La interpolación de cadenas es un enlace uni-direccional cuya salida es convertida en una cadena. Aquí tienes un ejemplo:

```markup
<span>${fullName}</span>
```

La propiedad `fullName` será directamente interpolada en el contenido del (elemento) `span`. También puedes usar la interpolación de cadenas para manipular enlaces a clases CSS así:

```markup
<div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
```

En esta linea "dot" es una clase presente estáticamente y "green" solo está presente si `isHappy`es verdadero, en caso contrario estará presente la clase "red". Además, cualquiera que sea el valor de `color`... este es añadido como clase.

> **Nota:** Puedes usar expresiones simples en tu enlace. No intentes hacer cosas demasiado rebuscadas. No queremos que haya código en la vista. Solo buscamos establecer un vínculo entre la vista y su modelo.

#### ref

Además de órdenes e interpolación, el lenguaje de enlazado reconoce el uso de un atributo especial: `ref`. Al usar `ref` puedes crear un nombre local para un elemento que podrás usar como referencia en otra expresión de enlace. También se añadirá como una propiedad en el modelo, de manera que será accesible a través de código. Aquí tienes un ejemplo claro del uso de `ref`:

```markup
<input type="text" ref="name"> ${name.value}
```

También puedes usar el enlace especial `.view-model` combinado con `ref` para obtener el objeto de tipo modelo que respalda un elemento a medida de Aurelia. Mediante esta técnica puedes vincular diferentes componentes entre si de la siguiente manera:

```markup
<i-produce-a-value ref.view-model="producer"></i-produce-a-value>
<i-consume-a-value input.bind="producer.output"></i-consume-a-value>
```

### Comportamientos -behaviors-

Junto al enlazado de datos -databinding-, cuentas con el poder de los comportamientos de Aurelia para usarlo en tus vistas. Son tres los tipos de comportamiento incluidos:

* Elementos a medida -Custom Elements- ¡Amplía HTML mediante nuevas etiquetas! Tus elementos a medida pueden tener sus propias vistas (que usan el enlazado de datos y otros comportamientos) y, opcionalmente, hacer uso del [ShadowDOM](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/) (incluso aunque el navegador no lo soporte).
* Comportamientos añadidos -Attached Behavior- Amplía HTML con atributos nuevos que pueden añadirse a los elementos, ya sean normales o a medida. Estos atributos "añaden" -attach- nuevo comportamiento a los elementos.
* Controles de plantillas -Template Controllers- Crean mecanismos nuevos para desplegar las plantillas. Un control de plantillas es una clase capaz de crear interfaz de usuario dinámicamente y de inyectarla en el DOM.

Naturalmente, todos funcionan sin problemas con el enlazado de datos. Echemos un vistazo a los comportamientos que te proporciona Aurelia y que están disponibles globalmente en todas las vistas.

#### show

El comportamiento añadido `show` te permite condicionar la visualización de un elemento HTML. Si el valor de `show` es `true` el elemento se visualiza el elemento, en caso contrario se mantiene oculto. Este comportamiento no añade/elimina el elemento del DOM, solo modifica su visibilidad. Aquí tienes un ejemplo:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

Cuando la propiedad `isSaving` es verdadera, el `div` será visible, en caso contrario estará oculto.

#### if

El control de plantillas `if` permite añadir/eliminar un elemento HTML condicionalmente. Si el valor es verdadero, el elemento estará presente en el DOM, en caso contrario no.

```markup
<div if.bind="isSaving" class="spinner"></div>
```

Este ejemplo es parecido al anterior con `show`. La diferencia estriba en que si la evaluación de la expresión de enlace resulta falsa, el `div`será eliminado del DOM, en lugar de solo quedar ocultado.

Si necesitas añadir/eliminar condicionalmente un grupo de elementos y no puedes colocar el comportamiento -behavior- `if` en un elemento padre, entonces puedes envolver esos elementos en una etiqueta `template` que incluya el comportamiento `if`. Así es como quedaría:

```markup
<template if.bind="hasErrors">
    <i class="icon error"></i>
    ${errorMessage}
</template>
```

#### repeat

El control de plantillas `repeat` te permite mostrar una plantilla varias veces, una por cada elemento de un vector. Aquí tienes un ejemplo que muestra la lista de nombres de clientes:

```markup
<ul>
    <li repeat.for="customer of customers">${customer.fullName}</li>
</ul>
```

Una consideración importante acerca del comportamiento `repeat` es que trabaja junto con la orden de enlace `.for`. Esta orden de enlace interpreta una sintaxis especial de la forma "elemento de un vector" donde "elemento" es el nombre local que vas a usar en la plantilla y "vector" es una expresión de enlace normal cuya evaluación resulta en un vector.

> **Nota:**: Como el comportamiento `if`, también puedes usar una etiqueta `template` para agrupar una colección de elementos que no tienen elemento padre. De hecho esto es válido para todos los controles de plantillas. Cuando colocas un control de plantillas en un elemento este se transforma en un HTMLTemplate durante la compilación, así que siempre puedes añadir explícitamente tu plantilla a tu marcado si quieres o tienes que hacerlo.

#### compose

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

El elemento `compose` también tiene un atributo `view` que puede ser usado de la misma manera que `view-model` si no deseas aprovechar la convención estándar de vista/modelo.

¿Que pasa si quieres determinar la vista dinámicamente en base a los datos o a las condiciones en tiempo de ejecución? También puedes hacer esto implementando un método `getViewStrategy()` en tu modelo. Este puede retornar una ruta relativa a la vista o un objeto del tipo `ViewStrategy` para el comportamiento a medida para la carga de vistas. La parte bonita es que este método se ejecuta después de la retrollamada -callback- de `activate`, así que tienes acceso a los datos del modelo al determinar la vista.

#### selected-item

HTMLSelectElement es una bestia interesante. Usualmente, puedes enlazar estos datos combinando un `repeat` para las opciones con un enlace con el valor, como esto:

```markup
<select value.bind="favoriteNumber">
    <option>Select A Number</option>
    <option repeat.for="number of numbers" value.bind="number">${number}</option>
</select>
```

Pero a veces deseas trabajar con objetos del tipo selector en lugar de primitivas. Para ello puedes usar el comportamiento añadido `selected-item`. Aquí está como configurar esto para una teórica lista de empleados:

```markup
<select selected-item.bind="employeeOfTheMonth">
  <option>Select An Employee</option>
  <option repeat.for="employee of employees" value.bind="employee.id" model.bind="employee">${employee.fullName}</option>
</select>
```

Primero, especificamos la orden de enlace `.bind` para el `selected-item`. Luego usamos un repetidor de la forma habitual, asegurándonos de enlazar `value`con alguna (propiedad) primitiva. También añadimos una segunda propiedad con el nombre `model` que será usado por el comportamiento `selected-item` para correlacionar la selección con un objeto de este tipo. En otras palabras, cuando se selecciona una opción la propiedad `employeeOfTheMonth` recibirá el valor de la propiedad `model` para esa opción. Cuando se establece la propiedad `employeeOfTheMonth` en el modelo, la opción con el correspondiente valor de `model` será seleccionada en la vista.

> **Nota:** Dijimos antes que solo los valores de elementos de formulario se enlazan por defecto bi-direccionalmente, pero en este caso nuestro atributo a medida `selected-item` también está enlazado en modo bi-direccional por defecto. ¿Como funciona esto? Resulta que cuando definimos un comportamiento en Aurelia, podemos especificar opcionalmente el modo por defecto de enlace con las propiedades.

#### global-behavior

Este no es un comportamiento añadido que vayas a usar directamente. En lugar de eso, este funciona en conjunción con una orden de enlace a medida para habilitar dinámicamente el uso declarativo de los complementos jQuery y otras APIs similares en HTML. Veamos un ejemplo para clarificar la idea:

```markup
<div jquery.modal="show: true; keyboard.bind: allowKeyboard">...</div>
```

Este ejemplo está basado en el complemento [Bootstrap modal widget](http://getbootstrap.com/javascript/#modals). En este caso, el complemento `modal` de jQuery será añadido al `div` y será configurado con su opción `show` puesta a `true` y su opción `keyboard` puesta al valor de la propiedad `allowKeyboard` del modelo. Cuando la vista contenedora esté desenlazada, el complemento jQuery será destruido.

Esta capacidad combina el comportamiento añadido especial `global-behaviour` con sintaxis a medida para habilitar estas capacidades. La sintaxis que ves aquí está basada en la sintaxis del atributo nativo (HTML) `style` que lista las propiedades y los valores separados de la misma manera que en el ejemplo. Ten en cuenta que puedes usar órdenes de enlace como `.bind`, para pasar datos de tu modelo directamente al complemento, o `.call`, para pasar una función de retrollamada directamente al complemento.

Así es como funciona:

Cuando el sistema de enlazado encuentra una orden de enlace que no reconoce, entonces lo interpreta dinámicamente. El atributo nombre es mapeado a un gestor global de enlaces que interpreta la orden de enlace. El gestor puede usar los valores para crear un objeto de opciones que aquel puede pasar al complemento. Cuando la vista se desenlaza, el gestor es capaz de limpiarse a si mismo. En este caso el gestor jQuery conoce el patrón para crear objetos del tipo del complemento y el uso del método `destroy` para hacer limpieza.

> **Nota:** `global-behavior` tiene una lista de gestores que tienes que configurar. Por defecto, solo está configurado para jQuery. Puedes desactivarlo todo, si quieres, pero resulta más fácil sacar partido de los complementos básicos jQuery sin necesidad de hacer algo por tu parte.

## Gestión de rutas -routing-

Te pueden pedir que crees muchos estilos diferentes de aplicaciones. Desde aplicaciones de navegación, a escritorios, a interfaces MDI -multiple documents interface-, Aurelia puede manejarlas todas. En muchos de estos casos una pieza clave de tu arquitectura es un sistema de gestión de rutas -router- del lado del cliente, capaz de traducir cambios en URLs en estados de la aplicación.

Si leíste la guía para empezar, sabrás que hay dos partes en la gestión de rutas. Primero, está el (objeto) `Router` que se encuentra en tu modelo. Está configurado con información de rutas y controla la navegación. Luego, tenemos `router-view` (vista del gestor de rutas) que se encuentra en la vista y que es responsable de mostrar lo que sea que el sistema (de gestión de rutas) identifica como el estado actual.

Veamos un ejemplo de configuración.

```javascript
import {Router} from 'aurelia-router';

export class App {
  static inject() { return [Router]; }
  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = 'Aurelia';
      config.map([
        { route: ['', 'home'],         moduleId: './home/index' },
        { route: 'users',              moduleId: './users/index', nav: true },
        { route: 'users/:id/detail',   moduleId: './users/detail' },
        { route: 'files*path',         moduleId: './files/index', href:'#files',   nav: true }
      ]);
    });
  }
}
```

Empezamos preguntando por un objeto del tipo `Router` para ser inyectado. Entonces asignamos este objeto a una propiedad `router` del modelo. _Tienes que llamar a esta propiedad **router**_. A continuación llamamos a la API `configure`. Le pasamos una función y esta nos pasa un objeto de configuración.

Opcionalmente podemos crear una propiedad `title` para ser usada en la construcción del título del documento. Pero la parte más importante es configurar las rutas. El método `map` del sistema de gestión de rutas toma una estructura de datos JSON que representa tu tabla de rutas. Las dos propiedades más importantes son `route` (una cadena o un vector de cadenas), que define el patrón de rutas, y `moduleId`, que contiene la ruta *relativa* `moduleId` al modelo. También puedes poner una propiedad `title`, para ser usado al generar el título del documento, una propiedad `nav` indicadora de si la ruta debe incluirse o no debe ser incluida en el modelo de navegación (también puede ser un número indicador de orden) y una propiedad `href` que puedes usar para establecer un enlace en el modelo de navegación.

>**Nota:** Cualquier propiedad que dejes fuera será determinada convencionalmente por el marco de trabajo en base a lo que hayas proporcionado.

Así que, que opciones tienes para el patrón del sistema de gestión de rutas?

* rutas estáticas -static routes-
    - p.ej. 'home' - Coincide con la cadena exactamente.
* rutas parametrizadas -parameterized routes-
    - p.ej.  'users/:id/detail' - Coincide con la cadena y luego evalúa el parámetro ìd`. La función de retrollamada del modelo `activate` será llamada con un objeto que tiene un parámetro `id` con un valor extraído de la URL.
* rutas comodín -wildcard routes-
    - p.ej. 'files*path' - Coincide con la cadena seguida de cualquier cosa que le siga. La función de retrollamada `activate` será llamado con un objeto que tiene un parámetro `path` con el valor del comodín.

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

### El ciclo de vida de activación de pantalla -the screen activation lifecycle-

Siempre que el sistema de enrutado procesa una navegación, este ejecuta un estricto ciclo de vida de los modelos desde y hacia los que está navegando. Hay cuatro estados en el ciclo de vida. Se puede acceder opcionalmente a cualquiera de ellos mediante la implementación del método adecuado en la clase de los modelos. Aquí hay una lista de retrollamadas al ciclo de vida:

* `canActivate(params, queryString, routeConfig)` - Implementa este gancho si quieres controlar si se puede navegar o no a tu modelo. Devuelve un valor booleano, una promesa de valor booleano o una orden de navegación.
* `activate(params, queryString, routeConfig)` - Implementa este gancho si quieres ejecutar lógica a la medida justo antes de que se presente tu modelo. Puedes devolver opcionalmente una promesa para decirle al sistema de enrutado que espere el enlazado y añada la vista después de que termines tu trabajo.
* `canDeactivate()` - Implementa este gancho si quieres controlar si el sistema de enrutado puede irse (_can navigate away_) de tu modelo cuando nos movemos a una nueva ruta. Devuelve un valor booleano, una promesa de un valor booleano o una orden de navegación.
* `deactivate()` - Implementa este gancho si deseas ejecutar lógica a medida cuando se navega abandonando tu modelo. Puedes optar por devolver una promesa para decirle al enrutador que espere hasta que termines tu trabajo.

El objeto `params` tendrá una propiedad por cada parámetro de la ruta que fue interpretada, `queryString` tendrá una propiedad por cada valor de cadena de consulta y `routeConfig` será el objeto original de configuración de rutas que tu montaste.  

> **Nota** Una _Navigation Command_ orden de navegación es cualquier objeto con un método `navigate(router)`. Cuando se encuentra uno, se cancelará la navegación y el control se transferirá a la orden de navegación. Hay una orden de navegación integrado: `Redirect`.

### Enrutadores hijos -child routers-

Si no has leído la guía [Get Started](/get-started.html), te recomendamos que lo hagas ahora y que pongas especial atención en la sección titulada "Bonificación: usando enrutadores hijos.

Siempre que configures una ruta para mapear a un modelo, ese modelo puede en realidad contener su propio enrutador... y cuando configuras rutas con él... esos modelos pueden contener sus propios enrutadores... y así sucesivamente. Los patrones de rutas son relativos al enrutador padre y las IDs de módulos y vistas son relativas al modelo mismo. Esto te permite encapsular fácilmente características o aplicaciones hijas así como gestionar un estado jerárquico complejo.

Un enrutador hijo es solo un enrutador como otro cualquiera. Así que todo lo expuesto anteriormente es de aplicación. Para añadir un enrutador hijo, simplemente solicita que sea inyectado un `Router` y configúralo con tus rutas hijas. El ciclo de vida de activación de pantalla explicado más arriba se aplica también a los enrutadores hijos. Cada fase del ciclo de vida es ejecutado contra la jerarquía de enrutadores completa antes de pasar a la siguiente fase. Los ganchos de activación se ejecutan de arriba abajo y los ganchos de desactivación se ejecutan de abajo arriba.

### Enrutamiento convencional -conventional routing-

Como todo en Aurelia, tenemos un fuerte soporte para convenciones. De esta manera, en realidad puedes optar por enrutar dinámicamente en lugar de preconfigurar todas tus rutas previamente. Aquí tienes como configurar el enrutador para hacer esto:

```javascript
router.configure(config => {
  config.mapUnknownRoutes(instruction => {
    //check instruction.fragment
    //set instruction.config.moduleId
  });
});
```

Todo lo que tienes que hacer es establecer la propiedad `config.moduleId` y estarás listo. También puedes devolver una promesa desde `mapUnknownRoutes` para determinar el destino asíncronamente.

>**Nota:** Aunque no necesariamente relacionado con el enrutamiento convencional, puedes necesitar a veces configurar tu enrutador asíncronamente. Por ejemplo, puedes necesitar llamar a un servicio web para obtener permisos de usuario antes de establecer las rutas. Para hacer esto, implementa una retrollamada en tu modelo de enrutador llamada `configureRouter`. En esta retrollamada puedes configurar tu enrutador y devolver opcionalmente una promesa -`Promise`- si fuera necesario.

### Añadiendo pasos al procesado de rutas -pipeline-

El procesado -pipeline- de rutas se compone de pasos separados que se ejecutan sucesivamente. Cada uno de estos pasos tiene la capacidad tanto de modificar lo que ocurre durante el enrutamiento, como de detenerlo. El procesado cuenta con algunos puntos para su ampliación por los que se nos permite añadir nuestros propios pasos. Están `` authorize` -autoriza- y `bindmodel` -enlazamodelo-. `authorize` ocurre antes que `bindmodel`. Estas extensiones se denominan filtros de rutas -route filters-.

El ejemplo a continuación muestra como añadir una autorización a tu aplicación:

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
      config.addPipelineStep('authorize', AuthorizeStep); // Add a route filter to the authorize extensibility point.
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
    // Check if the route has an "auth" key
    // The reason for using `nextInstructions` is because
    // this includes child routes.
    if (routingContext.nextInstructions.some(i => i.config.auth)) {
      var isLoggedIn = /* insert magic here */false;
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

Estos puntos de ampliación -extensibility points- son en si mismos pequeños procesados -pipelines- que nos permiten añadir múltiples pasos a cada uno de ellos. Por ejemplo, si ,además de `AuthorizeStep` -PasoAutorizar- (que solo comprobaría que el usuario ha accedido), podrías añadir un `IsAdminStep` -PasoEsAdmin- al punto de ampliación `authorize`. Estos se ejecutarían sucesivamente.

También es posible crear tus propios filtros con nombre simplemente pasando un nombre diferente a `addPipelineStep` -añadirPasoAlProcesado-. Esto lo puedes usar de la siguiente manera:

```javascript
config.addPipelineStep('myname', MyFirstStep); // Transparently creates the pipeline "myname" if it doesn't already exist.
config.addPipelineStep('myname', MySecondStep); // Adds another step to it.
config.addPipelineStep('modelbind', 'myname'); // Makes the entire `myname` pipeline run as part of the `modelbind` pipeline.
```

### Configurando PushState

Si prefieres desprenderte de los `#` (almohadilla) en tus URLs, entonces vas a tener que habilitar `pushState` en tu aplicación. ¡Por suerte Aurelia admite esto! Tendrás que hacer algún ajuste también del lado del servidor para que esto funcione correctamente. Empecemos por el lado de Aurelia en la ecuación.

Primero necesitas decirle a Aurelia en el `router` `config` que quieres usar `pushState` de esta manera:

``` javascript
this.router.configure(config => {
  config.title = 'First Impressions';
  config.options.pushState = true; // <-- this is all you need here
  config.map([
    { route: ['','welcome'],  moduleId: './welcome',      nav: true, title:'Welcome' },
    { route: 'child-router',  moduleId: './child-router', nav: true, title:'Child Router' }
  ]);
});
```

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

## Extendiendo HTML

Aurelia tiene un compilador de plantillas HTML poderoso y extensible. El compilador en si mismo es un algoritmo para interactuar con varios _tipos de comportamiento_ que contiene la lógica para manipular el HTML. A priori, Aurelia proporciona implementaciones para tres tipos de comportamientos del núcleo, los cuales creemos que cubre la mayoría de escenarios que encontrarás en el día a día. Los tres tipos son _Attached Behaviors -comportamientos añadidos-_, _Custom Elements -elementos a medida-_ y _Template Controllers -controles de plantilla-_.

Por defecto, los comportamientos no son visibles para el compilador. Hay tres vías principales para incorporarlos:

* Usar el elemento `import` para importar un comportamiento en una vista. El atributo `from` especifique la ruta relativa al módulo de comportamiento. El comportamiento será definido localmente.
* Usar el objeto Aurelia durante tu fase de configuración inicial -bootstrapping- para llamar `.withResources(resources)` para registrar comportamientos con visibilidad global en tus aplicaciones.
* Instala un complemento que registre comportamientos con visibilidad global en tu aplicación.

>**Nota:** Una práctica recomendada para tu propia aplicación es colocar todos tus comportamientos específicos de aplicación, convertidores de valores, etc. en una carpeta _resources_. Luego crear un archivo _index.js_ que lo convierte todo en un complemento interno. Finalmente, instala ese complemento durante la fase de configuración inicial -bootstrapping- de la aplicación. Esto mantiene todos los recursos en una ubicación conocida, junto a su código de registro. Esto además mantendrá tu archivo _main.js_ limpio y sencillo.

Todos los comportamientos pueden incorporarse opcionalmente al ciclo de vida de vista implementando cualquiera de los ganchos siguientes:

* `bind(bindingContext)` - Invocado cuando el motor de enlazado de datos enlaza la vista. El contexto de enlazado es el objeto al que está enlazada la vista.
* `unbind()` - Invocado cuando el motor de enlazado de datos desvincula la vista.
* `attached()` - Invocado cuando la vista que contiene ese comportamiento es añadida al DOM.
* `detached()` - Invocado cuando la vista que contiene ese comportamiento es eliminada del DOM.

>**Nota:** Si decides implementar la retrollamada `bind`, el enlazado inicial de tu comportamiento fluirá de manera un poco distinta. Habitualmente, si tienes retrollamadas a tus propiedades de comportamiento -Behavior Properties-, estas son llamadas una a una durante la fase de enlazado. Pero si añades la retrollamada `bind`, dichas propiedades no serán llamadas durante la inicialización. En cambio, la retrollamada `bind` será llamada una vez que todas las propiedades tengan asignados sus valores enlazados iniciales. Esta es una importante y útil característica, en particular para comportamientos complejos que puedan querer no "actuar" hasta que no estén disponibles todos los valores evaluados.

### Comportamientos añadidos -attached behaviors-

Los comportamientos añadidos añaden nuevo comportamiento o función a un elemento HTML existente mediante la adición de un atributo a medida a tu marcado. Usos comunes para comportamientos añadidos incluyen:

* Envolver complementos jQuery y similares (cuando el comportamiento global -`global-behavior`- es insuficiente).
* Atajos para enlazados comunes a estilo, clases o atributos.
* Simplemente todo que requiera cambiar un elemento HTML existente o incluso un elemento a medida que no puedes modificar directamente.

Los comportamientos añadidos tienden a representar competencias transversales. Por ejemplo, puedes crear un comportamiento recomendado a medida que puedes añadir a cualquier elemento. Esta idea es mejor que la de crear la función recomendada directamente en cada elemento a medida que crees.

Veamos la implementación de uno de los comportamientos añadidos (propios) de Aurelia: `show`. Aquí tienes como se usa:

```markup
<div show.bind="isSaving" class="spinner"></div>
```

El comportamiento `show` aplicará condicionalmente una clase a un elemento en función de la falsedad de su valor. (La clase, cuando es aplicada, oculta el elemento.) Aquí está la implementación:

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

Lo primero a destacar es que un comportamiento añadido es una clase y sigue los mismos patrones que hemos visto anteriormente. Observa que los `metadata` -metadatos- juegan un importante papel en la definición de un comportamiento. Aquí tienes lo que los metadatos están haciendo:

* `.attachedBehavior('show')` - Crea un objeto de tipo metadato `AttachedBehavior` para comunicarle al compilador HTML como se integra esta clase. El comportamiento será reconocido por el compilador siempre que encuentre un atributo con el nombre `show`. En Aurelia, los comportamientos añadidos siempre se vinculan a un único atributo  HTML. Se establece una relación de uno a uno.
* `.withProperty('value', 'valueChanged', 'show')` - Crea una propiedad de comportamiento -`BehaviorProperty`- que le dice al compilador de HTML que hay una propiedad específica en tu clase que se corresponde con un atributo en HTML. El primer parámetro de este método es el nombre de la propiedad de tu clase. El último parámetro es el nombre del atributo, que solo se necesita si es distinto del nombre de la propiedad. El segundo parámetro indica opcionalmente una retrollamada a la clase que será invocada cada vez que cambie la propiedad.

Bien. Hablemos de convenciones.

* Si el nombre de tu función de retrollamada cumple el patrón   {nombrePropiedad}Changed, entonces no necesitas especificarlo. Así, en el caso anterior, pudimos omitir el valor del segundo parámetro.
* Si los nombres de tu propiedad y de tu atributo son iguales, entonces no es necesario que los especifiques. En el caso anterior, al ser distintos, es necesario que lo especifiquemos.
* Los comportamientos añadidos siempre se corresponden con un único atributo. Esto nos permite optimizar un sencillo patrón de uso. Si llamas "value" a tu propiedad, entonces no necesitas incluir los metadatos de la propiedad. Automáticamente estableceremos una correspondencia entre un atributo con el mismo nombre que tu comportamiento y la propiedad `value`.
* Si el nombre de tu clase cumple el patrón {NombreComportamiento}AttachedProperty, entonces no necesitas incluir los metadatos del comportamiento añadido. El nombre del atributo será inferido del nombre de la clase eliminando "AttachedBehavior", escribiendo en minúsculas y con guión la parte restante del nombre, p.ej. comportamiento-nombre -behavior-name-.

Estas convenciones significan que podemos definir nuestro comportamiento `show` de esta manera:

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

> **Nota:** ¿Y por qué no aprovecha Aurelia mismo internamente estas convenciones? Siempre que estemos creando una librería externa de comportamientos, lo mejor es ser explícitos. Tu no sabes si los desarrolladores que utilicen tu librería van a cambiar o no las convenciones de Aurelia, quebrando así tu librería. Para evitar esto, indica siempre explícitamente los metadatos para los comportamientos que previsiblemente van a ser usados en otras aplicaciones. Dentro de tu propia aplicación puedes usar las convenciones que quieras para simplificar el desarrollo.

A continuación, veamos el constructor.

Los comportamientos añadidos pueden obtener fácilmente acceso al elemento HTML al que se han añadido especificándolo en el vector `inject`. El comportamiento `show` almacene la referencia de manera que puede actualizar `classList` posteriormente.

Finalmente, veamos la retrollamada `valueChanged`. Dijimos anteriormente que esto está configurado mediante los metadatos de la propiedad de manera que sea llamada siempre que el valor cambie. El sistema de enlazado actualizará automáticamente las propiedades disparando la retrollamada. Así, todo lo que tiene que hacer la implementación es  añadir/eliminar la clase apropiada en función del valor.

### Propiedades con opciones -options properties-

Puede que te preguntes que hacer si quieres crear un comportamiento añadido con múltiples propiedades teniendo en cuenta que los comportamientos añadidos siempre están en correspondencia con un único atributo. Para este escenario usamos una propiedad `OptionsProperty` que capacita a tu atributo único para funcionar como el atributo nativo `style`, con múltiples propiedades insertadas en él. Aquí tienes un ejemplo de como se usa:

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

Esto crea un comportamiento añadido de nombre `my-behavior` con dos propiedades `foo` and `bar`. Cada una de estas propiedades están disponibles directamente en la clase, aunque su configuración en HTML es algo distinta. Aquí está como lo haríamos:

```markup
<div my-behavior="foo: some literal value; bar.bind: some.expression"></div>
```

Fíjate en que no usamos la orden de enlace sobre el comando mismo. En lugar de eso, lo usamos con cada propiedad individual dentro del valor del atributo. Puedes utilizar literales u órdenes de enlazado estándar.

>**Note:** No se usan las órdenes `delegate` y `trigger` dentro de un atributo de opción. Estas están siempre añadidas al elemento mismo, puesto que trabajan directamente con eventos nativos del DOM. En cambio si puedes usar `call`.

### Elementos a medida -custom elements-

Los elementos a medida añaden nuevas etiquetas a tu marcado HTML. Cada elemento a la medida pueden tener su propia plantilla de vista puede desplegarse bien en el Light DOM o en el Shadow DOM. Los elementos a medida también pueden tener cualquier número de propiedades que aflorarán como atributos en HTML para dar soporte al enlazado de datos y a los cuales pueden vincularse dentro de su plantilla de vista.

¿Por qué no creamos un sencillo elemento a medida para ver como funciona esto? Vamos a crear un elemento que salude a alguien, llamado `say-hello`. Aquí está como queremos poder usarlo una vez que esté hecho:

```markup
<template>
    <import from="./say-hello"></import>

    <input type="text" ref="name">
    <say-hello to.bind="name.value"></say-hello>
</template>
```

Así que, como lo construimos? Bien, empezamos con una clase, tal y como hicimos con el comportamiento añadido. Aquí está el código:

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

Si leíste la sección sobre comportamientos añadidos, entonces ya sabes lo que hace esto. Aquí también hay algunas convenciones, lo que significa que podríamos hacer eso si quisiéramos:

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

Por defecto, los elementos a medida tienen una vista. Aquí está la del nuestro:

#### say-hello.html
```markup
<template>
    <button click.delegate="speak()">Say Hello To ${to}</button>
</template>
```

Como puedes ver, contamos con acceso a las propiedades y métodos de nuestra clase. Es importante señalar que o necesitas declarar metadatos de la propiedad para cada propiedad con la que deseas establecer un enlace en tu plantilla. Solo necesitas declararlos para propiedades que quieres que existan como atributos en tu elemento a medida.

Esto es todo realmente. Sigue las mismas convenciones para nombres que con las parejas vista/modelo y todos los mismos patrones para los elementos a medida. Hay algunas pocas opciones únicas de metadatos para los elementos a medida que debes conocer:

* `.useShadowDOM()` - Esta hace que las vistas de tus componentes sean desplegadas en el Shadow DOM en lugar de en Light DOM. Si no estás familiarizado con estos términos, lee [este artículo](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/). Cuando usamos el Shadow DOM, puedes usar _selectores de contenido_ -content selectors- en tu plantilla de vista.
* `.noView()` - Si tu elemento a medida no tiene una vista, porque todos su comportamiento está implementado en código, entonces usa esta opción.
* `.useView(relativePath)` - Si deseas usar una vista diferente de la que se usaría siguiendo las convenciones, puedes usar esta opción de metadatos para especificar una ruta relativa de la vista que quieres usar.

### Controles de plantilla -template controllers-

Los controles de plantilla convierten el DOM en una plantilla HTML inerte. El control podrá decidir cuando y donde (y cuantas veces) generar el contenido de la plantilla en el DOM. Ejemplos de esto son los comportamientos `if` y `repeat`. Coloca simplemente uno de estos comportamientos en un nodo del DOM y este se convertirá en una plantilla, controlada por un comportamiento.

Echemos una ojeada a la implementación del comportamiento `if` para ver como se compone uno de estos. Aquí está el código fuente completo:

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

Antes de profundizar en los aspectos únicos de los controles de plantilla, déjame recordarte aquello que ya hemos visto que es similar. Primero, tenemos una clase simple con metadatos. Nuestras metadatos son declarados de la misma manera que en los dos tipos anteriores de comportamiento. Las convenciones también funcionan de la misma manera. Así que puedes llamar a esta clase `IfTemplateController` y no necesitarías especificarlo en los metadatos. También podrías prescindir de los metadatos de la propiedad al declarar la retrollamada `valueChanged`. Sigue el mismo patrón que los comportamientos añadidos -AttachedBehaviors-.

Bien, que es distinto? Observa el constructor. Nuestro control de plantilla recibe inyectados únicamente dos elementos: `BoundViewFactory` y `ViewSlot`.

La función `BoundViewFactory` es capaz de generar objetos del tipo plantilla HTML a la que está vinculado el control. No hay necesidad de preocuparse por el compilado, etc. Ya nos hemos ocupado de ello por ti. ¿Pero por qué se llama "Bound" View Factory (factoría de vista enlazada)? Bien, se está refiriendo al contexto (padre) de enlazado. Este está enlazado -"bound"- en cierto sentido. Así, si llamas a su método `create` este va a generar una nueva vista a partir de la plantilla que va a estar enlazada a ese contexto. Esto es lo que quieres cuando usas un comportamiento `if`. Esto no es lo que quieres con un comportamiento `repeat`. En ese caso, cada vez que llamas a `create` quieres una vista vinculada a un elemento (de vector) en particular. Para conseguir esto, simplemente pásale al método `create` cualquier objeto al que quieras vincular la vista.

`ViewSlot` (espacio para vista) representa el espacio o ubicación dentro del DOM del que la plantilla fue extraída. Esta es usualmente la ubicación en la que quieres añadir los objetos de tipo View.

>**Nota**: A diferencia de los comportamientos previos, el control de plantilla funciona más directamente con las _primitivas_ -primitives- del marco de trabajo. Views, ViewFactories y ViewSlots son todos componentes de bajo nivel del motor de plantillas.

Mira con atención la función de retrollamada `valueChanged`. Aquí puedes ver donde está el comportamiento `if` creando la vista y añadiéndola al hueco, en base a la verdad del valor. Hay algunos detalles importantes de esto:

* El comportamiento siempre llama a `bind` en la vista View _antes de_ añadirla a la posición ViewSlot. Esto garantiza que todos los enlaces internos se evalúan fuera del DOM activo. Esto es importante para el rendimiento.
* De forma parecida, siempre llama a `unbind` _después de_ eliminar la vista View del DOM.
* Después de que la vista View sea creada inicialmente, el comportamiento `if` no la desecha cuando el valor se vuelve falso. Mantiene el objeto del tipo View en memoria (cache). Aurelia puede hacer un nuevo uso de estos objetos View y re-dirigirlos  a diferentes contextos de enlazado. Nuevamente, esto es importante para el rendimiento, puesto que elimina la necesidad de recrear estos objetos del tipo View.

## La gestión de eventos -eventing-

El sistema de gestión de eventos es una poderosa herramienta cuando necesitas que componentes inconexos de tu aplicación se comuniquen entre si. Aurelia soporta tanto eventos estándar del DOM como eventos más específicos de aplicación a través de `EventAggregator` (agregador de eventos).

### Eventos del DOM

Los eventos DOM deben usarse cuando han de enviarse mensajes específicos de la interfaz de usuario. No deben ser usados para enviar mensajes específicos de la aplicación. Aurelia no añade ninguna funcionalidad más allá del DOM para eventos de interfaz. Cualquier comportamiento puede tener su elemento -`Element`- asociado inyectado en su constructor. Entonces puedes usar el `Element` para lanzar eventos. Para aprender más acerca de la creación y el lanzamiento de eventos DOM a medida [lee por favor este artículo](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events).

### El agregador de eventos

Si necesitas eventos de aplicación acoplados débilmente, querrás usar el `EventAggregator`. Su interfaz pub/sub simplificada lo hace ideal para un amplio rango de escenarios de gestión de mensajes.

El agregador de eventos puede publicar eventos en un canal de mensajes o puede publicar mensajes fuertemente tipificados. Primero echemos un vistazo a la publicación por canales:

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

Empezamos por recibir a través de la inyección de dependencias una función singular -singleton- Event Aggregator. A continuación llamamos a su método `publish`, pasándole el nombre del canal de mensajes y el conjunto de datos a enviar por ese canal. Aquí está como se auto-configura un suscriptor para recibir por determinado canal:

```javascript
import {EventAggregator} from 'aurelia-event-aggregator';

export class ASubscriber{
    static inject(){ return [EventAggregator]; }
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

Como puedes ver, se usa el mismo nombre de canal, pero se proporciona una función de retrollamada, que será invocada con cada mensaje enviado por este canal.

Alternativamente, puedes publicar y suscribirte a mensajes fuertemente tipificados. Aquí tienes un ejemplo de editor -publisher:

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

En este caso, publicamos un mensaje de uno de los tipos en particular. Aquí tienes un suscriptor de ejemplo:

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
            //do something with the message here
        });
    }
}
```

El (receptor) suscriptor -subscriber- será llamado cada vez que se publica un objeto del tipo `SomeMessage`. La suscripción -subscription- es polimórfica, así que si se publica un objeto de un tipo subclase de `SomeMessage`, este (suscriptor) también recibirá notificación de ello.

>**Nota:** Todas las formas del método `subscribe` devuelven una _función desechar_ -dispose function-. Puedes llamar a esta función para desechar la suscripción y dejar de recibir mensajes. Un lugar adecuado para desechar (una suscripción) es en una (función de) de retrollamada `deactivate` -desactivar- del modelo, si este es manejado por un enrutador, o en su retrollamada `detached`, si se encuentra en cualquier otro modelo.

## Cliente HTTP

Como ventaja, Aurelia incluye un cliente HTTP `HttpClient` básico para proporcionarnos una interfaz confortable para el objeto XMLHttpRequest del navegador. `HttpClient` no está incluido en el módulo que instala Aurelia con el configurador inicial -bootstrapper-, ya que es completamente opcional y muchas aplicaciones pueden querer usar una estrategia diferente para la recuperación de datos. Así, si quieres usarlo, primero necesitas instalarlo con la orden siguiente:

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
        return this.http.get('uri goes here');
    }
}

```

El `HttpClient` -cliente HTTP- tiene la siguiente implementación:

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

Como puedes comprobar, nos proporciona métodos preparados para `get`, `put`, `patch`, `post`, `delete` and `jsonp`. Cada uno de estos métodos envía un `HttpRequestMessage` -mensaje de petición HTTP- excepto `jsonp` que envía un `JSONPRequestMessage` -mensaje de petición JSON-. El resultado de enviar un mensaje es una `Promise` -promesa- de un `HttpResponseMessage` -mensaje de respuesta HTTP-.

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

## Ejecución a medida -customization-

### Convenciones relativas a las vistas y a los modelos (de vista)

¿Como se enlazan vistas y modelos? Nuestra sencilla convención se basa en la identidad de módulo -module id-. Si cuentas con un modelo de identidad `./foo/bar/baz` (su ruta básicamente) esté será mapeado por defecto con `./foo/bar/baz.js` y `./foo/bar/baz.html`. Supón que quieres seguir otra convención diferente. ¿Que pasa si todos tus modelos están en una carpeta `view-models` y quieres que tus vista estén en una carpeta `views`? ¿Que tendrías que hacer? Para esto necesitas modificar el comportamiento de la estrategia convencional de vistas -Conventional View Strategy-. Así es como lo harías:


```javascript
import {ConventionalView} from 'aurelia-framework';

ConventionalView.convertModuleIdToViewUrl = function(moduleId){
  return moduleId.replace('view-models', 'views') + '.html';
}
```
Este código tendrías que ejecutarlo como parte de tu lógica de configuración inicial -bootstrapping logic- para que surtiera efecto antes de que se carguen los comportamientos. Esto afectará a *todo* incluido a los elementos a medida. Así que si necesitas o quieres que estos actúen de otra manera, tendrás que tenerlo en cuenta en tu implementación de `convertModuleIdToViewUrl`.

>**Nota:** Este es un ejemplo de por que los autores de complementos externos no deben fiarse de las convenciones. Los desarrolladores pueden modificar estas convenciones para adaptarse a las necesidades de su propia aplicación.
