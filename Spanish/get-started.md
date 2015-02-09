# Para empezar

**¡Bienvenido a Aurelia!** Este tutorial te guiará a través de la creación de una aplicación sencilla usando Aurelia y explicará brevemente sus conceptos principales. Damos por supuesto que te manejas con Javascript, HTML y CSS. Para obtener una rápida visión de conjunto, te recomendamos que vayas directamente a la sección titulada "Configurar la página HTML" de manera que puedas ver sin más como se usa Aurelia. Luego, cuando ya estés listo para montar realmente algo, vuelve aquí y lee acerca de "Configurar tu entorno" y de "Configurar la estructura y el montaje del proyecto". Para ver el resultado completo de este tutorial, échale por favor un vistazo a nuestro [proyecto de esqueleto de navegación](https://github.com/aurelia/skeleton-navigation/releases).

>**Nota:** ¿Buscas esta guía en otro idioma? Échale un vistazo a nuestro repositorio [documentation](https://github.com/aurelia/documentation).

## Configurar tu entorno

Empecemos configurando un gran conjunto de herramientas que podrás usar para crear aplicaciones Javascript modernas. Todo nuestro equipamiento está basado en [Node.js](http://nodejs.org/). Si ya lo tienes instalado, estupendo! Si no es así, vete a la [página web oficial](http://nodejs.org/), descárgatelo e instálalo. Todo lo demás que vamos a necesitar lo instalaremos usando el gestor de paquetes de Node ([npm](https://docs.npmjs.com/getting-started/what-is-npm)).

Primero, empecemos por instalar [Gulp](http://gulpjs.com/), que vamos a utilizar para automatizar el montaje. Si aún no lo tienes, puedes usar npm de esta manera para configurarlo (es posible que necesites usar `sudo`):

 ```shell
  npm install -g gulp
 ```

A continuación, necesitamos instalar [jspm](http://jspm.io/). Este nos servirá de gestor de paquetes del lado del cliente. Puedes hacerlo de así:

  ```shell
  npm install -g jspm
  ```

> **Nota:** Igual que Bower y Yeoman, jspm aprovecha [git](http://git-scm.com/), así que necesitas instalarlo si no lo tuvieras. Además, jspm busca en Github para instalar paquetes, pero Github admite un número limitado de peticiones anónimas de la API. Te recomendamos que configures jspm con tus credenciales de Github para evitarte problemas. Puedes hacerlo ejecutando `jspm endpoint config github` y respondiendo a la linea de comandos. ¿Que no quieres usar jspm? No hay problema. Todos los paquetes de Aurelia están también disponibles vía [Bower](http://bower.io/).

## Configurar la estructura y el montaje del proyecto

Con las herramientas ya instaladas, podemos ocuparnos ahora de la creación de una estructura básica para tu aplicación. Empieza [descargándote el esqueleto de navegación](https://github.com/aurelia/skeleton-navigation/releases). Descomprímelo y renombra la carpeta a _navigation-app_. Dentro de esta carpeta encontrarás todo lo que necesitas, incluyendo un montaje básico, una configuración de paquetes, estilos y demás. Como queremos enseñarte a construir tu propia aplicación, borra por favor el contenido de la carpeta _src_ y el archivo `index.html`. En un minuto aprenderemos a crearlos.

Con estos archivos colocados en su sitio, vamos a ejecutar algunos comandos.

1. Abre una linea de comandos y cambiate al directorio _navigation-app_.

2. Ejecuta el comando siguiente para instalar los complementos de Gulp listados en la sección _devDependencies_ del manifiesto de paquetes.

  ```shell
  npm install
  ```
3. A continuación, ejecuta el siguiente comando para instalar la librería de Aurelia, Bootstrap y Font-awesome, incluidos en la sección _jspm.dependencies_ del manifiesto de paquetes.

  ```shell
  jspm install -y
  ```

Hasta aquí todo lo que hemos hecho son procedimientos Node.js normales de montaje y gestión de paquetes. No tienen nada de específicos en relación a Aurelia. Simplemente te estamos guiando desde cero a través de la configuración de un proyecto moderno en Javascript y de la configuración de montaje. Es posible que ya conocieras todo esto, pero si no es así, entonces bienvenido a este mundo nuevo y excitante! 

> **Nota:** Aurelia **no depende** ni de Bootstrap ni de Font-Awesome. Simplemente los hemos aprovechado como parte de este tutorial para disponer con rapidez de un aspecto aceptable listo para ser usado.

## Configurar la página HTML

Si nos has seguido hasta aquí, ahora dispones de todas las librerías, configuración de montaje y herramientas necesarias para crear aplicaciones sorprendentes con Aurelia. Lo próximo que has de hacer es crear el archivo _index.html_ en la raíz de nuestra carpeta de proyecto. Créalo ahora y utiliza el código que viene a continuación.

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

Si, eso es. Esta es la única página HTML de nuestra aplicación. La cabecera del documento ("head") es bastante evidente: enlazamos a nuestras hojas de estilo personalizadas, de Bootstrap y de Font-Awesome. Lo que es interesante es el cuerpo ("body").

> **Nota:** Asegúrate de que las direcciones locales para bootstrap y font-awesome se corresponden con los "href" de los enlaces. Es posible que estas librerías hayan actualizado sus versiones desde la redacción de este documento.

Empecemos con las etiquetas script. Primero tenemos _system.js_, nuestro cargador de módulos basado en estándares ES6. Es el responsable de cargar tanto la librería de Aurelia como tu propio código. A continuación tenemos _config.js_. Este contiene la configuración para el cargador. Y es generado automáticamente cada vez que ejecutamos un comando jspm. jspm es el gestor de paquetes del lado del cliente que nosotros recomendamos. Este proporciona una experiencia de desarrollo estupenda al integrar la gestión de paquetes del lado del cliente con un cargador de módulos que cumple con ES6.

> **Nota:** El marco de trabajo ("framework") Aurelia no está atado a jspm o a SystemJS. También soporta APIs estilo `require` como RequireJS y Dojo Loader directamente. Incluso puedes implementar tu propio cargador y desarrollar tu gestión de paquetes como te parezca. En cualquier caso pensamos que jspm/SystemJS es la mejor opción actual orientada a ES6 y es el planteamiento que nosotros recomendamos. 

Una vez que ya tenemos nuestro cargador de módulos y su configuración, establecemos el valor de `baseUrl` para apuntar a la ubicación de nuestro código. A continuación cargamos el módulo `aurelia-bootstrapper` con una llamada a `System.import`, asegurándonos de capturar cualquier error que pudiera suceder en el arranque y los enlaza a la consola. 

Cuando se carga el iniciador (boostrapper) este inspecciona el documento HTML en busca de atributos _aurelia_. En este caso encontrará que el cuerpo ("body") tiene un atributo _aurelia-app_. Esto le dice al iniciador ("bootstrapper") que cargue el modelo (de la vista) y la vista de nuestra _app_ (aplicación), ubicados por convención en _app.js_ y _app.html_, para componerlos como aplicación Aurelia en el DOM. 

Un momento... si no tenemos ningún modelo (de la vista) ni vista de nuestra _app_ (aplicación). Ummm... ¿¡Y AHORA QUE!? 

## Creación de nuestra primera pantalla

En Aurelia, los elementos de la interfaz de usuario están compuestos por parejas de vista (_view_) y modelo (de vista) (_view-model_). La vista se escribe en HTML y se muestra en el DOM. El modelo (de vista) está escrito en Javascript y provee de datos y comportamiento (lógica) a la vista. El potente enlazado de datos (_databinding_) vincula las dos partes permitiendo que los cambios en tus datos se reflejen en la vista y viceversa. 

Veamos como funciona...

Crea en la carpeta _src_ los archivos _app.html_ y _app.js_. Estos son la vista y el modelo que buscaba el iniciador ("bootstrapper"). Empecemos por el modelo creando una simple clase que contenga un _firstName_ ("nombre") y un _lastName_ ("apellido"). También añadiremos una propiedad computada _fullName_ ("nombre completo") y un método _welcome_ de bienvenida. Este es el aspecto que debería tener: 

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

¿Que... eso es Javascript?

Si. Lo es. De hecho es ECMAScript 6 (ES6), la próxima versión de Javascript que incorpora numerosas características nuevas al lenguaje. Afortunadamente el archivo Gulp que te descargaste más arriba te ha equipado con [6to5](https://6to5.org/), un sorprendente transpilador (transpiler) -compilador de código fuente a código fuente- de ES6 a ES5 que nos permite escribir Javascript del futuro y ejecutarlo en navegadores actuales. De esta manera podemos utilizar módulos, clases, lambdas, interpolación de cadenas y otros. ¡Que bueno! Bien, como se crea un modelo (de vista) _view-model_? Creamos una simple clase y la exportamos con _export_ al marco de trabajo (framework). Pedazo. De. Tarta.

> **Nota:** No tienes que usar 6to5 necesariamente para escribir una aplicación con Aurelia. Puedes usar AtScript, CoffeScript,... o el lenguaje de los navegadores actuales: ES5. Todo lo que tienes que hacer es seguir los patrones estándar del lenguaje para crear clases y todo irá bien. Nosotros pensamos que ES6 es alucinante y deseamos que lo tomes en consideración en primer lugar. Para aprender más acerca de las versión más reciente de Javascript incluyendo exportación de módulos y clases te recomendamos la lectura de [Luke Hoban's feature repo](https://github.com/lukehoban/es6features#readme), el repositorio de características de Luke Hoban.

Bien. Ahora que tenemos un modelo (de vista) con algunos datos y comportamiento básicos, echemos un vistazo a su cómplice... la vista. 

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

Todas las vistas van contenidas en un elemento `template`. Esta vista es un formulario básico, al que hemos dado estilo usando las clases de Bootstrap. Observa los controles de entrada. ¿Te has fijado en `value.bind="firstName"`? Esto enlaza (_bind_) el valor de la entrada (_value_) a la propiedad _firstName_ de nuestro modelo. Cada vez que cambie la propiedad en el modelo, la entrada se actualizará con el nuevo valor. Cada vez que cambies el valor en la entrada de nuestra vista, Aurelia enviará el nuevo valor a nuestro modelo. Es así de fácil. 

Hay un par de cosas más de interés en este ejemplo. En el último grupo del formulario encontramos la siguiente sintaxis en el contenido HTML: `${fullName}`. Se trata de una interpolación de cadena. Es un enlace uni-direccional del modelo con la vista que es convertido inmediatamente en un cadena e insertado en el documento. Finalmente, observa el elemento _form_ mismo. Habrás notado esto: `submit.delegate="welcome()"`. Esto es un enlace de evento (_event binding_). Este usa la delegación de evento (_event delegation_) para enlazar el evento _submit_ de forma que ejecute el método _welcome_ cada vez que se envía el formulario.

> **Nota:** Si no has oído nada acerca de la delegación de eventos (event delegation), es una técnica usada para manipular más eficientemente eventos en el navegador estableciendo un único gestor de evento a nivel documento para gestionar todos los eventos de un tipo, en lugar de añadir un gestor de evento a cada nodo. 

Ejecutémoslo y veamoslo en acción. Usa el siguiente comando en la consola para montar y lanzar el servidor. 

```shell
gulp watch
```

Ahora puedes ir en el navegador a [http://localhost:9000/](http://localhost:9000/) para ver la aplicación. Teclea en los controles de entrada del formulario y ve como la propiedad calculada _Full Name_ se actualiza con cualquier cambio. Pulsa el botón y observa como se llama a tu método. 

> **Nota:** Aurelia posee un motor de enlace de datos único y potente que usa técnicas adaptativas para elegir el mejor modo de observar cambios en cada propiedad. Por ejemplo, si estás usando un navegador que soporta [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe), tanto _firstName_ como _lastName_ serán observados usando esa estrategia. Si no, se generarán accesores (getters) y asignadores (setters) que guarden los cambios en la cola de micro-tareas (Micro Task Queue), emulando correctamente el comportamiento de _Object.observe_. Ya que la propiedad computada _fullName_ no puede ser observada con ninguna de estas técnicas, usaremos [dirty-checking](http://blog.bguiz.com/post/57373805814/accessors-vs-dirty-checking-in-javascript-frameworks/). Usaremos la mejor técnica dependiendo de la situación e incluso pueden incorporarse también estrategias personalizadas con intención de "enseñar" al marco de trabajo a como     observar tipos especiales de patrones de modelo. Esto nos parece estupendo. 
>
El comando `.bind` usa el comportamiento por defecto de enlace para cualquier propiedad. El tipo de enlace por defecto es uni-direccional para todo excepto para controles de formulario, para los que es bi-direccional. Esto puede modificarse en cualquier momento mediante los comandos de enlace `.one-way`, `.two-way` y `.one-time`. Análogamente, podemos usar `.delegate` para la delegación de eventos, pero también podemos usar `.trigger` para asignarlo directamente al elemento objetivo. 

## Añadiendo la navegación

Ya que se trata de una aplicación de navegación, probablemente deberíamos añadir algunas pantallas más y montar un enrutador del lado del cliente, no te parece? Empecemos por renombrar _app.js_ y _app.html_ a _welcome.js_ y _welcome.html_ respectivamente. Esta será la primera página de nuestra aplicación. Ahora creamos unos nuevos archivos _app.js_ y _app.html_ que nos servirán de "diseño" o "página maestra". La vista contendrá nuestra UI (interfaz de usuario) para la navegación y el emplazamiento para contenido de la página actual y el modelo contendrá una instancia del enrutador, configurado con nuestras rutas. Empezaremos por el modelo para que veas como montamos el enrutador: 

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

Bien, aquí hay algunas cosas nuevas realmente interesantes. Queremos usar el enrutador, así que empezamos por importarlo al principio del archivo. Este es el poder de ES6 otra vez. A continuación creamos nuestra clase _App_ que contendrá nuestros datos y lógica para el diseño principal de la aplicación. Echa una ojeada a la función constructora. Esta está esperando algo para pasarle una instancia del enrutador cuando se crea la clase App. ¿De donde sale eso? 

Aurelia crea los componentes de la interfaz de usuario según resultan necesarios para visualizar la aplicación. Esto lo hace usando una contenedor para la [inyección de dependencias](https://es.wikipedia.org/wiki/Inyecci%C3%B3n_de_dependencias) capaz de proveer dependencias como esta del constructor. ¿Como sabe el inyector de dependencias (DI) que es lo que tiene que proporcionar? Todo lo que hemos de hacer es añadir un método estático llamado _inject_ que devuelve un vector (array) de tipos con los que aprovisionar las instancias. Debería haber un elemento en el vector por cada parámetro del constructor. En el ejemplo anterior, necesitamos una instancia del enrutador, así que añadimos el tipo _Router_ al vector de inyección. 

> **Nota:** Si resulta que usas AtScript, te gustará saber que Aurelia entiende las anotaciones de tipo de AtScript y que las puede usar para la inyección de dependencias. Según otros compiladores (de código fuente a código fuente) principales de Javascript vayan adoptando los metadatos en tipos o anotaciones seguiremos mejorando nuestra plataforma para que esta aprenda a entender el formato del lenguaje que tu hayas elegido. 

Necesitamos crear el router como propiedad pública de la clase. La propiedad tiene que llamarse _router_. Esto es importante. No tengas aquí ocurrencias como llamarla _taco_ ni nada parecido, vale? Se trata de un enrutador así que llámalo _router_ y todos estaremos contentos. ¿Hemos mencionado que <strong>tienes que llamarla _router_</strong>? 

Muy bien. Toca configurar el enrutador. Es sencillo. Puedes incluir un título que se usará cuando se genere el título del documento. Luego incluimos las rutas. Cada ruta posee las siguientes propiedades: 

* `route`: Es un patrón el cual, al coincidir, llevará al enrutador a navegar a esta ruta. Puedes usar rutas estáticas como aquí, pero también puedes usar parámetro como este: `customer/:id`. También se da soporte a rutas comodín y a parámetros con cadenas de consulta. La ruta puede ser un único patrón de cadena o un vector de patrones.
* `moduleId`: Esta es una ruta relativa al modelo actual que especifica la pareja vista/modelo que quieres mostrar para esta ruta.
* `title`: Opcionalmente puedes proporcionar un título que se usará en la generación del título del documento. 
* `nav`: Si esta ruta debe ser incluida en el modelo de navegación (_navigation model_), porque quieres generar una interfaz de usuario (UI) con ella, este de ser _true_ (o un número que indicará el orden).

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
Siguiendo nuestra sencilla regla de construcción de aplicaciones, la clase _App_ estará enlazada con la vista anterior _app.html_. Buena parte de este código de marcado tiene que ver con el montaje de la estructura principal de navegación. Ya hemos visto el enlazado básico y la interpolación de cadenas, así que fijémonos en cosas nuevas. Observa el elemento `ul` de _navbar-nav_. Su `li` muestra como usar un repetidor con la siguiente expresión `repeat.for="row of router.navigation"`. Este creará un elemento `li` por cada elemento en el vector `router.navigation`. La variable local es _row_ y puedes ver como se utiliza en los `li` y en sus elementos descendientes. 

> **Nota:** La propiedad`navigation` del router es un vector rellenado con todas las rutas que hayas marcado con `nav:true` en la configuración de tu enrutador. 

También puedes ver en los elementos `li` una demostración de como usar la interpolación de cadenas para añadir/eliminar clases dinámicamente. Más adelante en la vista hay un segundo elemento `ul`. ¿Ves el enlace en su `li` hijo? `if.bind="router.isNavigating"` Esto añade/elimina el elemento `li` dependiendo del valor de la expresión vinculada. Convenientemente, el enrutador actualizará su propiedad `isNavigating` siempre que esté... navegando. 

La última parte en la que nos vamos a fijar es el elemento `router-view` cerca del final de la vista. Siempre que tengas un `Router` en tu modelo, necesitarás un `router-view` en la vista. Estos dos son camaradas y se conectarán automáticamente de manera que cualquier ruta que el enrutador `Router` señale como actual será mostrada en en el elemento `router-view`.

Con esto en su sitio, sigue adelante e inicia el servidor de desarrollo con `gulp watch`. Abre tu navegador y echa un vistazo. Ahora deberías ver un elemento de navegación principal con una única pestaña seleccionada para nuestra ruta de bienvenida "welcome". La vista _welcome_ se mostrará en el área de contenido principal y funcionará como antes. Abre las herramientas de depuración de tu navegador y observa el DOM en vivo. Verás que el contenido de la vista _welcome_ está desplegada dentro de `router-view`. 

> **Nota:** Si mientras tanto has dejado ejecutándose la tarea _gulp_, habrás comprobado que el navegador se actualiza automáticamente cada vez que haces un cambio. Esto ocurre gracias a `browser-sync`, él cual hemos configurado convenientemente para ti como parte de la configuración por defecto de _gulp_. 

## Añadiendo una segunda página

Bueno, técnicamente hemos montado una aplicación de navegación... pero no es demasiado interesante porque solo hay una página. Vamos a añadir una segunda página. ¿Puedes adivinar como hacerlo? Apuesto a que si... 

Vamos a mostrar algunas imágenes de Flickr. Para ello, vamos a configurar nuestro enrutador para esta página hipotética: 

### app.js (updated)

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

Si supones que necesitamos crear los archivos _flickr.js_ y _flickr.html_, has acertado. Aquí está el código fuente:

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

Aquí hay un montón de cosas estupendas. Vamos a empezar por el principio. Estamos importando `HttpClient` de Aurelia. Esto nos permite realizar peticiones HTTP de manera muy sencilla. No está incluido en la configuración por defecto de Aurelia, así que necesitas instalar el paquete. Para hacerlo ejecuta este comando en la consola: 


```shell
jspm install aurelia-http-client
```
Ahora espero que hayas visto la potencia del gestor y cargador de paquetes integrado. Simplemente instalas un paquete con jspm y luego los importas a tu código usando exactamente el mismo identificador. De esta manera puedes instalar cualquier cosa desde Github o NPM. 

Como antes, estamos usando la inyección de dependencias para contar con la instancia de `HttpClient` en el modelo. De todas maneras, no la usamos hasta el método _activate_. ¿Cual es la significación de eso que dices? A eso iba ahora... 

El enrutador de Aurelia inicia un ciclo de vida en los modelos siempre que las rutas cambian. A este nos referimos como "Screen Activation Lifecycle", ciclo de vida para activación de pantalla. Los modelos pueden acceder opcionalmente a varias partes del ciclo de vida para controlar el flujo para entrar y salir de la ruta. Cuando tu ruta está lista para estar activa, el enrutador llama a `activate`, si está presente. En el código anterior, usamos este gancho (acceso) para llamar a la API de Flickr y obtener de vuelta algunas fotos. Observa que devolvemos el resultado de la petición http desde nuestro método `activate`. Todas las APIs de `HttpClient` devuelven un objeto de tipo `Promise`. El enrutador lo detectará y esperará para completar la navegación hasta que este se resuelva. Así, de esta manera, puedes forzar opcionalmente al enrutador a retrasar el despliegue de la página hasta que se haya rellenado con los datos.

Hay un segundo gancho en el ciclo de vida que aparece aquí: `canDeactive`. El enrutador lo llama antes de que la navegación se aparte de la ruta. Esto te da la oportunidad de permitir o no que continúe la navegación devolviendo un booleano. También puedes devolver un objeto `Promise` en lugar de ese valor. El ciclo completo incluye los ganchos `canActivate`, `activate`, `canDeactivate` y `deactivate`. 

> **Nota:** Si no estás familiarizado con el tipo [Promises](http://www.html5rocks.com/en/tutorials/es6/promises/), es una nueva característica de ES6 diseñada para mejorar la programación asíncrona. Un objeto `Promise` representa un resultado futuro. Esencialmente, representa una "promesa" de completar cierta tarea o de proporcionar algunos datos en cierto momento del futuro. 

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

La vista para esta pantalla es bastante sencilla. No hay nada que no hayas visto antes, incluido el sorprendente uso del gran tabúdel estilo en línea. (Míralo del otro lado... no hay nada que ver.) 

Una vez que tengas todo en su sitio, sigue adelante y pon en funcionamiento otra vez tu aplicación. Ahora deberías ver dos elementos en la barra de navegación y ser capaz de moverte adelante y atrás entre ambos. ¡Hurra!

Recapitulemos. Para añadir una página a nuestra aplicación:

1. Añade la configuración de la ruta a _app.js_ al enrutador.
2. Añade un modelo de vista.
3. Añade una vista con el mismo nombre (pero con la extensión .html).
4. Celébralo.

## Bonificación: creación de un elemento personalizado

¡Mírate, empollón! Te veo interesado en aprender alguna maravilla extra en este día estupendo. En ese caso, vamos a crear un elemento HTML personalizado. Opino que un buen candidato para ello sería nuestra barra de navegación. Hay un montón de HTML en nuestro archivo _app.html_. ¿Por qué no extraer un elemento `<nav-bar>` a medida para hacer las cosas algo más declarativas? Esto es lo que queremos tener al final: 

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

Este código importa un elemento `nav-bar` desde "./nav-bar" y una vez que está disponible en la vista, ya podemos usarlo como cualquier otro elemento, incluyendo el enlace a sus propiedades particulares (como _router_). Así, que como conseguimos este producto final?

¿Sabes qué? Nuestras sencillas reglas relativas a modelo y vista se aplican también con los elementos a medida. (De hecho venimos creando lo que a veces llamamos elementos a medida "anónimos"... solo que no lo sabías.) Creemos un _nav-bar.js_ y un _nav-bar.html_. Aquí está primero el código para el modelo: 

### nav-bar.js

```javascript
import {Behavior} from 'aurelia-framework';

export class NavBar {
  static metadata(){ return Behavior.withProperty('router'); }
}
```

Para crear un elemento a medida, has de crear y exportar una clase. Puesto que esta clase va a ser utilizada en HTML como un elemento, necesitamos indicarle al marco de trabajo (framework) que propiedades de la clase deben aparecer como atributos del elemento. Para ello, usamos el método _metadata_. Como _inject_ metadata es una manera de proporcionarle información acerca de tu clase al marco de trabajo de Aurelia. Aurelia es inteligente y es capaz de inferir muchas cosas, pero cuando no puede o cuando quieres hacer algo diferente a las convenciones, entonces usamos alguna forma de metadatos. Para sacar provecho de esta capacidad, añade a la clase un método estático _metadata_ y devuelve un vector de instancias de metadatos (realmente simples objetos normales). En este caso estamos usando el auxiliar para metadatos de Aurelia _Behavior_. El método `withProperty` añade una propiedad `BehaviorProperty` que le dice al marco de trabajo que queremos que la propiedad `router` de la clase debe aflorar como un atributo en el HTML. Una vez que aflora como atributo, podemos enlazar datos a este en la vista.

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

El resultado parece prácticamente idéntico a la barra de navegación HTML de nuestro archivo _app.html_ original. Básicamente la extrajimos y la colocamos en esta plantilla. En lugar de enlazar con _app.js_, ahora enlazamos con _nav-bar.js_. 

Este es un elemento a medida muy simple sin ningún comportamiento real, pero está completo y podemos usarlo, tal y como hemos visto. Repetimos el marcado aquí para tu comodidad. 

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

Para recapitular: primero tenemos un elemento `import`. Aurelia lo utiliza para cargar el elemento a medida a través de la fuente relativa indicada en el atributo _from_. Este sigue nuestras sencillas convenciones así que este sabrá como cargar nuestros archivos _nav-bar.js_ y _nav-bar.html_. Cualquier cosa importada a una vista de esta manera es local a la vista. Como resultado, no tienes que preocuparte por los conflictos entre nombres. Lo segundo es el uso real del elemento que usa el enlace de datos con el enrutador de `App`. Estamos conectando (piping) la instancia del enrutador de nuestra clase `App` a la correspondiente propiedad del elemento `NavBar` para su representación interna. ¡Que bueno! 

> **Nota:** También puedes cargar elementos de la aplicación y otros comportamientos por conveniencia, de manera que no necesites importar recursos comunes en cada vista.  

Puede que desees saber como determina Aurelia el nombre del elemento a medida. Por convención, usara el nombre de la clase, en minúscula y con guión. De todas maneras,siempre puedes ser explícito. Para ello, añade más metadatos encadenando `.customElement('nav-bar')` a `Behavior`. ¿Que pasa si tu elemento a medida no tiene una plantilla para la vista, porque está implementado enteramente con código? No hay problema, encadena `.noView`. ¿Deseas usar ShadowDOM para tu elemento a medida? Hazlo como un profesional encadenando `.useShadowDOM()`. No te preocupes de si el navegador lo soporta o no. Tenemos una implementación de reserva eficiente y muy fiel para ShadowDOM. 

Además de crear elementos a medida, también puedes crear atributos aislados que añadan nuevo comportamiento a elementos existentes. A esto se le denomina _comportamientos añadidos_ (attached behaviors). En ocasiones puede que necesites crear _controladores de plantilla_ (template controllers) para añadir o eliminar dinámicamente DOM de la vista, como los métodos `repeat` y `for` que ya empleamos. Esto no es todo lo que puedes hacer. El motor de plantillas de Aurelia es poderoso y extensible. 

> **Nota:** Los usuarios de AtScript pueden aprovechar las anotaciones del lenguaje y Aurelia las pillará. Los usuarios de lenguajes que soporten propiedades estáticas de clase, como TypeScript, puede usar una propiedad `metadata` en lugar de un método, por comodidad. Esto se aplica también a la designación `inject` usada por el contenedor de la inyección de dependencias. 

## Bonificación: aprovechamiento de enrutadores hijos

¿No tienes suficiente? Bueno, tengo un trato que ofrecerte. Añadamos una tercera página a nuestra aplicación... con su propio enrutador! Esto es lo que llamamos un enrutador hijo. Los enrutadores hijos tienen su propia configuración de rutas y navegan relativamente a su enrutador padre. Prepárate para la locura... 

Primero, actualicemos nuestro _app.js_ con la nueva configuración. Aquí está como debe quedar: 

### app.js (actualizado... otra vez)

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

Nada nuevo en esto. La parte interesante es lo que está dentro de _child-router.js_...

### child-router.js

```javascript
import {Router} from 'aurelia-router';

export class Welcome{
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

¿¡Que!? ¿Es prácticamente la misma configuración que en `App`? ¿Que? ¿Por qué? Bien... probablemente no hagas esto nunca en la vida real... , pero es estupendo lo que hace. Esto, queridos amigos, es un enrutador recursivo y lo hacemos, porque podemos. 

Para terminar, aquí está la vista:

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

Lanza la aplicación y contempla la magia... y ruega para que el universo no explote.

## Conclusión
Con su gran atención a la experiencia de desarrollador, Aurelia te puede capacitar no solo para crear aplicaciones sorprendentes, sino también para disfrutar en ese proceso. Lo hemos diseñado con reglas simples en mente de manera que no necesitas perder tiempo con toneladas de configuración o en escribir código modelo solo para satisfacer un marco de trabajo complejo o restrictivo. Nunca chocarás con Aurelia contra barrera alguna. Ha sido cuidadosamente diseñado para que admita complementos y personalizaciones.  

Gracias por tomarte el tiempo de leer nuestra guía. Esperamos que explores la documentación y que construyas algo sorprendente. Estaremos atentos para ver todo 
lo que hagas. 
