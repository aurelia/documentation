# Erste Schritte

**Herzlich Willkommen bei Aurelia!** In dieser Anleitung werden anhand einer kleinen Beispielanwendung die grundlegenden Ideen und Konzepte von Aurelia kurz vorgestellt. Wir gehen davon aus, dass Du bereits mit  JavaScript, HTML und CSS vertraut bist. Für einen kurzen Überblick kannst Du die folgenden Abschnitte überspringen und zu "Einrichten der HTML-Seite" springen. Wenn Du dann in der Lage bist, ein Projekt zu erstellen, fahre mit "Konfiguration der Systemumgebung" und "Aufsetzen der Projekt Struktur und der Build-Prozess" fort. Das Ergebnis des Tutorials findest Du in unserem Projekt [Navigationsgerüst](https://github.com/aurelia/skeleton-navigation/releases).

> **Hinweis:** Suchst Du diese Anleitung in einer anderen Sprache? Schau in unserem [Dokumentations-Repository](https://github.com/aurelia/documentation) nach.


## Konfiguration der Systemumgebung

Wir beginnen mit dem Installieren unserer Tools für moderne Javascript Anwendungen, welche allesamt auf  [Node.js](http://nodejs.org/) basieren. Alles, was wir benötigen, wird mittels Node's Paketmanager ([npm](https://docs.npmjs.com/getting-started/what-is-npm)) installiert. Fall Node.js bereits auf Deinem System ist - perfekt! Ansonsten besuche [die offizielle Webseite](http://nodejs.org/) und installiere es. Um etwaigen Problemen von vorn herein aus dem Weg zu gehen, stelle bitte sicher, dass Du die [allerneuste stabile Version](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) auf Deinem System hast.

Zuerst installieren wir das Build Automatisierungstool [Gulp](http://gulpjs.com/). Falls Du es noch nicht installiert hast, verwende den `npm` Manager, um es global verfügbar zu machen. (Du solltest es mittels `sudo` als root ausführen):

  ```shell
  npm install -g gulp
  ```

Nun ist [jspm](http://jspm.io/) an der Reihe (das ist unser client seitiger Paketmanager). Du kannst ihn folgendermassen einrichten:

  ```shell
  npm install -g jspm
  ```

> **Hinweis:** `jspm` unterstützt wie Bower und Yeoman [git](http://git-scm.com/). `jspm` installiert direkt von GitHub, allerdings schränkt GitHub die Anzahl anonymer Anfragen ein. Um Probleme zu vermeiden, empfehlen wir Dir dringenst, dass Du jspm mit Deinen GitHub Zugangsdaten konfigurierst. Dazu musst Du nur `jspm registry config github` ausführen und die englisch sprachigen Anweisungen befolgen. Falls Du jspm nocht verwenden magst - kein Problem: Alle Aurelia Pakete sind auch via [Bower](http://bower.io/) verfügbar.

## Aufsetzen der Projekt Struktur und des Build-Prozesses

Wenn alle Werkzeuge installiert sind, können wir mit dem Aufsetzen unserer Beispielanwendungung beginnen. Dazu laden wir zunächst das [Navigationsgerüst herunter](https://github.com/aurelia/skeleton-navigation/releases). Entpacke es und bennene den Ordner um in _navigation-app_.

> **Hinweis:** Um das Navigationsgerüst zu "generieren", kannst Du alternativ [Yeoman](http://yeoman.io) folgendermassen verwenden:
>
>  ```
>  npm install -g yo generator-aurelia
>  yo aurelia
>  ```


Im Ordner findest Du alles Nötige incl. Konfigurationsdateien, build Skripte, styles, etc.

Du kannst das Dir die _index.html_ Datei sowie alle anderen Ressourcen im _src_ Ordner anschauen. Um das Erstellen einer Aurelia Anwendung von Grund auf zu lernen, solltest Du allerdings nun den Inhalt von _src_ löschen.

OK, wir beginnen mit einigen Kommandos:

1. Öffne eine Konsole und wechsle in den Ordner _navigation-app_.
2. Install alle Gulp Plugins, welche im Abschnitt _devDependencies_ des Package Manifest _package.json_ aufgeführt sind: 

  ```shell
  npm install
  ```
3. Als nächstes installieren wir die Aurelia Bibliothek und alle Abhängigkeiten wie Bootstrap und Font-Awesome, welche im Abschnitt _jspm.dependencies_ aufgeführt sind:

  ```shell
  jspm install -y
  ```
Für unseres von Grund auf neu erstelltes Javascript Projekt haben wir uns an einen modernen Node.js Enwicklungsprozess gehalten, es gab bislang nichts Aurelia Spezifisches. Vielleicht bist Du mit dem Umgang dieser Werkzeuge bereits vertraut, und wenn nicht - HERZLICH WILLKOMMEN!

> **Hinweis:** Bootstrap und Font-Awesome sind **keine** Abhängigkeiten von Aurelia. Sie werden in diesem Tutorial nur deshalb verwendet, um mit geringem Aufwand ein modernes Aussehen zu erzielen.


## Aufsetzen der HTML Seite

Nun solltest Du alle notwendigen Bibliotheken, Konfigurationsdateien und Build Werkzeuge installiert haben, um in die faszinierende Welt der Javascript Anwendungen mit Aurelia eintauchen zu können. Als Nächstes muss die _index.html_ Datei im Wurzelverzeichnis des Projektes erstellt werden. Diese Beispiel Datei dient als Vorlage unserer neuen Aurelia basierten Anwendung.

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
So, das war's schon! Das ist die einzige "richtige" HTML Seite unserer Anwendung. Der _head_ Abschnitt ist recht simpel: Wir verlinken auf die font-awesome und unsere eigenen Stylesheets. Der _body_ ist das eigentliche Interessante.

> **Hinweis:** Im Link muss auf den lokalen Ordner für font-awesome verwiesen werden. Möglicherweise hat sich seit Erstellung dieses Tutorials die Version erhöht und der generierte Ordner stimmt nicht mehr mit dem Link überein.

Beginnen wir mit den `script` Tags. Zuerst binden wir _system.js_ ein, unseren ES6 basierten Modullader. Damit werden die Aurelia Bibliotheken sowie der Anwendungscode geladen. In _config.js_ ist die Konfiguration des Modulladers zu finden. Diese wird automatisch beim Ausführen des `jspm` Kommandos erzeugt. Wir empfehlen den Client seitigen Paketmanager `jspm`, weil dieser durch die Integration mit dem SystemJS Modullader für die Aurelia Entwicklung bestens geeignet ist. Genau darunter müssen wir in `System.config` den Ausgabeort für den kompilierten JavaScript Code definieren.

>**Hinweis:** Aurelia ist nicht an jspm oder SystemJS gebunden. Wir unterstützen auch APIs im require Stil wie RequireJS und Dojo. Du kannst auch einen eigenen Modullader implementieren und die Packageverwaltung so handhaben, wie Du es gern hättest. Wie auch immer, wir halten jspm/SystemJS als die aktuell beste ES6 orientierte Lösung, und daher ist dies der von uns empfohlene Weg.

Nach Modullader und dessen Konfiguration binden wir mit einem `System.import` Aufruf das `aurelia-bootstrapper` Modul (das Startprogramm) ein. Dieses inspiziert das HTML document nach _aurelia-app_ Attributen, und findet dies in unserem Fall im _body_. Der Bootstrapper lädt daraufhin das _app_ View-Model und die desses View, welche  entsprechend der Aurelia Konvention in _app.js_ und _app.html_ zu finden sind, und erstellt daraus eine Aurelia Anwendung im DOM.

Moment....Wir haben ja noch gar kein _app_ View-Model und auch keine View. Was nun!?


## Erstellung unseres ersten "Screen"

In Aurelia werden UI Komponenten aus einer _View_ und einem _View-Model_ Paar erstellt. Die _View_ ist in HTML geschrieben und wird in den DOM gerendert. Das _View-Model_ ist Javascript Code und enthält sowohl Daten als auch die Verhaltenslogik der _View_. Aurelia's mächtiges _Data binding_ verknüpft diese beiden Teile und ermöglicht so, dass Datenänderungen automatisch in der _View_ aktualisiert werden und umgekehrt genauso. Diese "Separation of Concerns" (zu deutsch Trennung der Veranwortlichkeiten) ist in vielerlei Hinsicht nützlich: für eine Zusammenarbeit zwischen Entwicklern und Designern, für Wartung, Flexibiltät in der Architektur, und sogar für die Quellcodeverwaltung.

Sehen wir uns an, wie's funktioniert...

Erstelle dazu im _src_ Ordner eine _app.html_ sowie eine _app.js_ Datei. Das ist die app View und das View Model, welches vom Bootstrapper geladen wird. Beginnen wir mit dem  _View Model_. Wir erstellten eine einfache Klasse, welche einen _firstName_ und _lastName_ hält sowie eine berechnete Eigenschaft _fullName_ und eine Methode  "welcome" als Grussformel. So sieht sie aus:

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

Was...ist das etwa Javascript?

Ja. Ja und Ja, es ist Javascript. Tatsächlich ist es ECMAScript 7 (ES7), die folgende der nächsten Version von  Javascript, welche viele neue und spannende Features enthalten wird. Glücklicherweise wurde unser automatisierter Build (Gulp) mit [Babel](https://babeljs.io/) konfiguriert. Babel ist ein Transpiler, der Features aus kommenden Javascript Versionen zurückportiert und somit ermöglicht, dass moderner Code auch in heute aktuellen Browsern ablaufen kann. So kannst Du auch heute schon Module, Klassen, Lambdas, String Interpolation und vieles mehr verwenden. Genial! Aber wie genau muss man nun das _View Model_ erstellen? Wir erstellen einfach eine Klasse und exportieren diese ins Framework.

> **Hinweis:** Du musst nicht Babel oder ES7 Features verwenden, um eine Aurelia Anwendung zu erstellen. Du kannst auch Sprachen wie TypeScript / CoffeeScript oder einfach heute aktuelles ES5 Javascript benutzen. Du musst Dich einzig an die Standardmuster für's Erstellen von Klassen halten und alles ist perfekt. Trotzdem halten wir ES7 für genial und hoffen, Du siehst es genauso. Um mehr über die neueste Version von Javascript und deren geniale Features wie Module und Klassen zu erfahren, empfehlen wir [Babel Learning Guide](http://babeljs.io/docs/learn-es2015/).

OK. Nun haben wir das _View-Model_ mit einigen Basisdaten und etwas Verhaltenslogik implementiert, schauen wir uns nun dessen Gegenspieler an - die _View_.

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

Alle Views werden in ein `template` Tag eingeschlossen. Diese View ist ein einfaches Eingabeformular, welches mit Bootstrap verschönert wurde. Interessant sind die _input_ Eingabefelder. Siehst Du `value.bind="firstName"`? Das verknüpft den Wert (_value_) des Eingabefeldes mit der _firstName_ Eigenschaft unseres View-Models. Jedes Mal, wenn sich die Eigenschaft im Model ändert, wird das Eingabefeld mit dem neuen Wert aktualisiert. Immer, wenn sich der Wert im Eingabefeld ändert, setzt Aurelia den neuen Wert ins View-Model. Wir nennen dies Zwei-Wege Verknüfung (_two-way binding_) - so einfach ist das!

Es gibt noch weitere interessante Dinge in diesem Beispiel: In der letzten _form group_ siehst Du eine weitere bislang nicht spezifizierte Syntax im HTML: `${fullName}`. Das ist eine String Interpolation - eine Einwegverknüfung (_one-way binding_) vom _View-Model_ zur _View_, welche die Daten aus dem View-Model automatisch als String ins Dokument einfügt. Zum Schluss sehen wir und das _form_ Element selbst an: Mit  `submit.delegate="welcome()"` wird ein Ereignis an eine Methode gebunden (event binding). Durch Weiterleitung des Ereignisses (event delegation) bewirkt das _submit_ Ereignis ein Ausführen der _welcome_ Methode immer dann, wenn das Formular versendet wird.

> **Hinweis:** Falls Du noch nie etwas über _event delegation_ gehört hast: Das ist eine Technik, um Browsereignisse effizienter zu verarbeiten, indem auf Dokumentenebenede ein einziger _event handler_ exisitiert, welcher sich um alle Ereignisse eines bestimmeten Types kümmert, anstatt jedem Knoten im DOM einen eigenen  _event handler_ zuzuweisen.

OK, mit dem folgenden Kommando können wir nun die Anwendung zusammenbauen und den Server starten:

```shell
gulp watch
```

Gib in den Browser [http://localhost:9000/](http://localhost:9000/) ein und schau Dir unsere Anwendung an. Fülle die Eingabefelder des Formulars aus - siehst Du, wie sich der _Full Name_ entsprechend ändert?! Hinter dem Button verbirgt sich die Willkommensnachricht.

> **Hinweis:** Wenn es nicht funktioniert, versuche das [Update](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) auf die aktuelle Version von `npm` auszuführen.

> **Hinweis:** Aurelia's einzigartiger und mächtiger databinding Mechanismus verwendet adaptive Techniken, um herauszufinden, welcher Weg der Beste zur Überwachung von Änderungen ist. Wenn Du z.B. einen Browser mit [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) Unterstützung verwendest, werden _firstName_ und _lastName_ mit dieser Strategie überwacht. Falls nicht, werden Getter und Setter generiert, welche die Änderungen in eine Warteschlange (Micro Task Queue) einsteuern, welche dann das Object.observe Verhalten korrekt emuliert. Wenn die berechntete Eigenschaft _fullName_ mit solchen Techniken nicht überwacht werden kann, verwenden wir _dirty checking_, welches periodische Checks / Aktualisierungen vornimmt. Zur Optimierung können optional Abhängigkeiten (in unserem Fall zu Vor- und Nachnamen) deklariert werden. Es wird immer die beste Technik verwendet, Du kannst sogar eigene Überwachungsstrategien implementieren und dem Framework "beibringen". Das ist ziemlich cool.

> **Hinweis:** Das `.bind` Kommando verwendet das default binding Verhalten für jede Eigenschaft. Per default ist _two-way binding_ (Model <--> View) nur für Formular Eingabekomponenten vorgesehen, alles Andere ist _one-way_ (Model zur View). Du kannst dieses Verhalten immer überschreiben, und zwar indem dies explizit spezifiert wird `.one-way`, `.two-way` and `.one-time`. Ebenso ist `.delegate` Standard für _event delegation_, Du kannst dies  aber mit `.trigger` überschreiben, um direkt das Ziel Element anzusprechen.

## Hinzufügen der Navigation

Unser Beispiel soll ja ein Navigationsgrüst sein, und somit sollte es _Screens_ beinhalten, zwischen denen man Client seitig navigieren kann. OK, dazu benennen wir _app.js_ und _app.html_ zu _welcome.js_ respektive _welcome.html_ um. Das wird die erste Seite unserer Anwendung. Nun erstellen wir ein neues _app.js_ und _app.html_ welche als "Layout" und "Master" Seite fungieren. Die View soll die Benutzerschnittstelle unserer Navigation und einen Platzhalter für die jeweils aktuelle Seite enthalten,  das View-Model enthält eine Instanz des Routers, welcher mit unseren Routen konfiguriert ist. Beginnen wir mit dem View-Model, sodass Du sehen kannst, wie der Router aufgesetzt wird:

### app.js

```javascript
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome',  moduleId: './welcome',      nav: true, title:'Welcome' }
    ]);

    this.router = router;
  }
}
```

OK, hier gibt es wieder einige interessante Dinge. Im `configureRouter` callback siehst Duzunächst den _title_, welcher als Titel des Dokumentes gesetzt wird. Dann werden die Routen zugeordnet. Jede Routehat die folgenden Eigenschaften:

* `route`: Dieses beinhaltet ein Muster (regexp patten). Passt es, so wird der Router auf die entsprechende Seite navigieren. Routen können wie im Beispiel gezeigt statisch definiert sein, Du kannst aber auch parametrisierte Routen spezifizieren: `customer/:id`. Wildcards werden ebenso unterstützt wie _query strings_. Die Route kann entweder durch ein Muster (_single string pattern_) oder aber eine Vielzahl an Mustern festgelegt sein.
* `name`: Das ist der Name, welcher innerhalb der Anwendung zur Generierung der URLs verwendet wird.
* `moduleId`: Das ist der relative Pfad zum aktuellen View/View-Model Paar.
* `title`: Optional kann der Titel des Dokumentes angegeben werden.
* `nav`: Falls Du UI Komponenten generieren willst, muss die Route ins _navigation model_ eingefügt werden. Dann ist dieser Parameter auf _true_ zu setzen (bzw. eine Zahl, die die Reihenfolge widerspiegelt).

> **Hinweis:** Hast Du gesehen, wie wir ES6 imports verwendet haben, um bootstrap's Javascript und CSS zu laden?

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
Ensprechend der Konvention werden die Daten der `App` Klasse an die View in _app.html_ gebunden. Ein grosser Teil des Markups behandelt das Aufsetzen der Hauptnavigation. Du hast bereits das einfache Binding und String Interpolation kennen gelernt, darum konzentrieren wir uns nun auf etwas Neues: Sieh Die das navbar-nav `ul` element an. Dessen `li` demonstriert, wie man repeater Ausdrücke `repeat.for="row of router.navigation"` verwendet. Für jeden Eintrag des `router.navigation` arrays wird ein `li` generiert. Die lokale Laufvariable ist _row_ und man sieht sehr schön, wie es im `li` und dessen untergeordneten Elementes Verwendung findet.

> **Hinweis:** Die Eigenschaft `navigation` des Router ist ein array mit alle Routen, welche mit `nav:true` konfiguriert wurden. Aurelia handhabt die `repeat.for` Syntax entsprechend des neuen ES6 Standards `for..of` loop. Man kann es sich also so vorstellen, dass in einer Schleife für jede navigierbare Route eine UI Komponente generiert wird.

Am `li` kann man auch sehen, wie String Interpolation genutzt wird, um dynamisch Klassen hinzuzufügen / zu entfernen. Weiter unten in der View gibt es ein zweites `ul`, dessen `li` binding durch den Ausdruck `if.bind="router.isNavigating"` spezifiziert ist: Durch die Bedingung wird das `li` dynamisch hinzugefügt / entfernt. Gewöhnlicherweise aktualisiert der Router seine `isNavigating` Eigenschaft immer dann, wenn er....navigiert.

Das letzte Puzzleteil, was wir uns anschauen wollen, ist die `router-view` am Ende der View. Diese repräsentiert den Ort im DOM, an dem die aktuelle Seite gerendert wird.

OK, nun können wir fortfahren und den Entwickungs Server mit `gulp watch` erneut starten. Öffne den Browser und Du solltest nun eine Hauptnavigation mit einem einzigen ausgewählten Tab sehen, der unsere "welcome" Route repräsentiert. Die _welcome_ View sollte wie gehabt funktionieren. Öffne die Entwicklungstools des Browsers und sieh die den DOM an. Der Inhalt der _welcome_ View sollte innerhalb des `router-view` Elements liegen .

> **Hinweis:** Falls Du den gulp watch Task laufen lässt, wird die Seite nach jeglichen Änderungen am Quellcode aktualisiert. Das passiert aufgrund von `browser-sync`, welches Teil unserer default Gulp Konfiguration ist.

## Hinzufügen einer zweiten Seite

So, nachdem wir die technische Basis unseres Navigationssgerüstes gelegt haben, sollten wir eine zweite Seite hinzufügen. Kannst Du Dir vorstellen, wie? Ich wette, Du kannst....

Lass uns Bilder von Flickr anzeigen. Um das zu tun, muss zunächst unser Router für eine neue hypotetische Seite konfiguriert werden:

### app.js (updated)

```javascript
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome',  moduleId: './welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',       name: 'flickr',   moduleId: './flickr',       nav: true }
    ]);

    this.router = router;
  }
}
```

Wie Du Dir denken wirst, brauchen wir eine _flickr.js_ und _flickr.html_ Datei. Hier ist der Quellcode:

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

Hier gibt's jede Menge coole Dinge: Zuerst importieren wir Aurelia's `HttpClient`. Damit können wir ganz einfach HTTP Anfragen senden und auswerten. Da dies kein Bestandteil der Aurelia default-Konfiguration ist, müssen wir das Paket mit folgendem Kommando installieren:

```shell
jspm install aurelia-http-client
```

Jetzt hoffe ich, dass Du den Sinn des integrierten Paketmanagers und Modulladers erkennst. Du installierst einfach ein Paket mit jspm und importierst es mit exact dem gleichen Bezeichner. Damit kannst Du alles von `GitHub` und `npm` einbinden.

Siehst Du den ES7 `inject` Decorator? Was ist das? Aurelia erzeugt UI Komponenten nach Bedarf, und zwar direkt beim Rendern der Anwendung. Dies geschieht durch eine [Dependency Injection (DI)](http://en.wikipedia.org/wiki/Dependency_injection) Container, welcher im Constructor die Eigenschaften wie den http Client entsprechend injiziert. Aber woher weiss das DI System, was es injizieren muss? Dazu existiert der ES7 `inject` Decorator, dem eine Liste an Klassen übergeben wird, sodass er Instanzen zunächst erzeugen und dann dem Constructor übergeben kann. Für jeden Constructor Parameter muss ein Argument im inject Decorator vorhanden sein. Im Beispiel oben benötigen wir eine HttpClient Instanz, deshalb spezifizierten wir `HttpClient` als Argument des `inject` decorators und fügen dann den entsprechenden Parameter im Constructor hinzu.

> **Hinweis:** Falls Du keinen decorator verwenden magst, kannst Du auch eine static `inject` Methode verwenden, oder aber einfach eine Property `inject` zur Klasse hinzufügen, welche ein array an Typen beinhaltet, welche injeziert werden sollen.

Falls Du TypeScript in der Version 1.5 oder neuer verwendest, steht Dir der `@autoinject` Decorator zur Verfügung. Damit kannst du auf den inject Aufruf verzichten, aber Du musst die Typen in der Signatur des Constructor korrekt angeben. Das würde folgendermassen aussehen:

```javascript
import {autoinject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@autoinject
export class Flickr{
  ...

  constructor(public http:HttpClient){}

  ...
}
```


## Bonus: Unternavigation mittels Child Router

Hast Du immer noch nicht genug bekommen? Wir haben ein besonderes Bonbon für Dich: Wir fügen eine neue Seite hinzu ... welche ihren eigenen Router mitbringt. Wir nennen dies Child Router oder sub navitation oder Unternavigation. Ein Child router bringt seine eigene Konfiguration seiner Routen mit, und navigiert relativ zu seiner übergeordnetem Router (Parent Router). Mach Dich auf den kompletten Wahnsinn gefasst....

Zuerst aktualsieren wir unsere _app.js_ mit der neuen Konfiguration:

### app.js (updated...again)

```javascript
import 'bootstrap';
import 'bootstrap/css/bootstrap.css!';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome',      moduleId: './welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',       name: 'flickr',       moduleId: './flickr',       nav: true },
      { route: 'child-router', name: 'childRouter',  moduleId: './child-router', nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Hier gibt's nichts Neues. Die interessanten Dinge passieren im  _child-router.js_...

### child-router.js

```javascript
export class ChildRouter{
  heading = 'Child Router';

  configureRouter(config, router){
    config.map([
      { route: ['','welcome'], name: 'welcome',     moduleId: './welcome',      nav: true, title:'Welcome' },
      { route: 'flickr',       name: 'flickr',      moduleId: './flickr',       nav: true },
      { route: 'child-router', name: 'childRouter', moduleId: './child-router', nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Was!? Das ist ja die gleiche Konfiguration wie in der `App`? Was? Warum? Hmmm... Normalerweise solltest Du so etwas niemals machen, aber ... es ist ziemlich cool. Das, meine Freunde, ist ein rekursiver Router und wir machen das, weil wir es können.

Der Vollständigkeit halber, ist hier die View:

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

Starte die die Anwendung und bewundere die Magie ... und bete, dass das Universum nicht explodiert.


## Schluss

Mit dem starken Bezug auf die Bedürfnisse von Entwicklern bietet Dir Aurelia nicht nur einen neuen Ansatz zum Schreiben moderner Anwendungen sondern auch Freunde beim Entwickeln. Aurelia's Design basiert auf einigen wenigen, simplen Konventionen, sodass Du keine Zeit mit Boilerplate Code und sich wiederholenden oder ähnlichen Konfigurationen verplemperst. Trotzdem ist es nicht restriktiv und behindert Dich auch nicht in Deinem Tun. Es wurde von Beginn an mit Rücksicht auf Anpassbarkeit und Erweiterbarkeit entworfen.

Danke, dass Du Dir die Zeit genommen hast, diese Anleitung bis zu Ende zu lesen. Wir hoffen, Du bist nun motviert, die Dokumentation anzugehen und etwas Grossartiges entstehen zu lassen. Wir freuen uns zu sehen, was Du bauen wirst.
