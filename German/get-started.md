# Erste Schritte

**Herzlich Willkommen bei Aurelia!** In dieser Anleitung werden anhand einer kleinen Beispielanwendung die grundlegenden Ideen und Konzepte von Aurelia kurz vorgestellt. Wir gehen davon aus, dass Du bereits mit  JavaScript, HTML und CSS vertraut bist. Für einen kurzen Überblick kannst Du die folgenden Abschnitte überspringen und zu "Einrichten der HTML-Seite" springen. Wenn Du dann in der Lage bist, ein Projekt zu erstellen, fahre mit "Konfiguration der Systemumgebung" und "Aufsetzen der Projekt Struktur und der Build-Prozess". Das Ergebnis des Tutorials findest Du in unserem Projekt [Navigationsgerüst](https://github.com/aurelia/skeleton-navigation/releases).

> **Hinweis:** Suchst Du diese Anleitung in einer anderen Sprache? Schau in unserem [Dokumentations-Repository](https://github.com/aurelia/documentation).

## Konfiguration der Systemumgebung

Wir beginnen mit dem Installieren unserer Tools für moderne Javascript Anwendungen, welche allesamt auf  [Node.js](http://nodejs.org/) basieren. Alles, was wir benötigen, wird mittels Node's Paketmanager ([npm](https://docs.npmjs.com/getting-started/what-is-npm)) installiert. Fall Node.js bereits auf Deinem System ist - perfekt! Ansonsten besuche [die offizielle Webseite](http://nodejs.org/) und installiere es. Um etwaigen Problemen von vorn herein aus dem Weg zu gehen, stelle bitte sicher, dass Du die [allerneuste stabile Version](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) auf Deinem System hast.

Zuerst installieren wir das Build Automatisierungstool [Gulp](http://gulpjs.com/). Falls Du es noch nicht installiert hast, verwende den `npm` Manager, um es global verfügbar zu machen. (Du solltest mittels `sudo` als root ausführen):

  ```shell
  npm install -g gulp
  ```

Nun ist [jspm](http://jspm.io/) an der Reihe (das ist unser client seitiger Paketmanager). Du kannst ihn folgendermassen einrichten:

  ```shell
  npm install -g jspm
  ```

> **Hinweis:** jspm unterstützt wie Bower und Yeoman [git](http://git-scm.com/). JSPM installiert direkt von GitHub, allerdings schränkt GitHub die Anzahl anonymer Anfragen ein. Um Probleme zu vermeiden, empfehlen wir Dir dringenst, dass Du jspm mit Deinen GitHub Zugangsdaten konfigurierst. Dazu musst Du nur `jspm registry config github` ausführen und die englisch sprachigen Anweisungen befolgen. Falls Du jspm nocht verwenden magst - kein Problem: Alle Aurelia Pakete sind auch via [Bower](http://bower.io/) verfügbar.

## Aufsetzen der Projekt Struktur und der Build-Prozess

Wenn alle Werkzeuge installiert sind, können wir mit dem Aufsetzen unserer Beispielanwendungung beginnen. Dazu laden wir zunächst das [Navigationsgerüst herunter](https://github.com/aurelia/skeleton-navigation/releases). Entpsacke es und bennene den Ordner um in _navigation-app_.

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
3. Als nächstes installieren wir die Aurelia Bibliothek und alle Abhängigkeiten wie bootstrap und font-awesome, welche im Abschnitt _jspm.dependencies_ aufgeführt sind:

  ```shell
  jspm install -y
  ```

Für unseres von Grund auf neu erstelltes Javascript Projekt haben wir uns an einen modernen Node.js Enwicklungsprozess gehalten, es gab bislang nichts Aurelia spezifisches. Vielleicht bist Du damit bereits vertraut, und wenn nicht - HERZLICH WILLKOMMEN in der neuen spannenden Welt!


> **Hinweis:** Bootstrap und Font-Awesome sind **keine** Abhängigkeiten von Aurelia. Sie werden in diesem Tutorial nur deshalb verwendet, um mit geringem Aufwand ein modernes Aussehen zu erzielen.


## Aufsetzen der HTML Seite

Nun solltest Du alle notwendigen Bibliotheken, Konfigurationsdateien und Build werkzeugeinstalliert haben, um faszinierende Javascript Anwendungen mit Aurelia zu implementieren. Als Nächstes muss die _index.html_ Datei im Wurzelverzeichnis des Projektes erstellt werden. Dieses Beispiel-File dient als Vorlage unserer neuen Aurelia basierten Anwendung.

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

So, das ist es! Das ist die einzige "richtige" HTML Seite unserer Anwendung. Der _head_ Abschnitt ist recht simpel: Wir verlinken auf die font-awesome und unsere eigenen Stylesheets. Der _body_ ist das eigentliche Interessante.

> **Hinweis:** Im Link muss auf den lokalen Ordner für font-awesome verwiesen werden. Möglicherweise hat sich seit Erstellung dieses Tutorials die Version erhöht und stimmt nun nicht mehr mit dem generierten Ordner überein.

Beginnen wir mit den _script_ Tags. Zuerst binden wir _system.js_ ein, unseren ES6 basierten Modullader. Damit werden die Aurelia Bibliotheken sowie der Anwendungscode geladen. In _config.js_ ist die Konfiguration des Modulladers zu finden. Diese wird automatisch beim Ausführen des jspm Kommando erzeugt. Wir empfehlen den Client seitige Paketmanager jspm, weil dieser durch die Integration Client seitigen Paketmanagements mit einen ES6 konformer Modulladers für die Aurelia Entwicklung bestens geeignet ist. Genau darunter müssen wir in `System.config` den Ausgabeort für den kompilierten JavaScript Code definieren.

>**Hinweis:** Aurelia ist nicht an jspm oder SystemJS gebunden. Wir unterstützen auch APIs im require Stil wie RequireJS und Dojo. Du kannst auch einen eigenen Lader implementieren und die Packageverwaltung so handhaben, wie Du es gern hättest. Wie auch immer, wir halten jspm/SystemJS als die aktuell beste ES6 orientierte Lösung, und daher ist dies der von uns empfohlene Weg.

Nach Modullader und dessen Konfiguration binden wir mit einem `System.import` Aufruf das `aurelia-bootstrapper` Modul (das Startprogramm) ein. Dieses inspiziert das HTML document nach _aurelia-app_ Attributen, und findet dies in unserem Fall im _body_. Der Bootstrapper lädt daraufhin das _app_ View-Model und die desses View, welche  entsprechend der Aurelia Konvention in _app.js_ und _app.html_ zu finden sind, und erstellt daraus eine Aurelia Anwendung im DOM.

Moment....Wir haben ja noch gar kein _app_ View-Model und auch keine View. Was nun!?

## Erstellung unseres ersten "Screen"

In Aurelia, UI Elemente werden aus einer _View_ und einem _View-Model_ Paar erstellt. Die _View_ ist in HTML geschrieben und wir in den DOM gerendert. Das _View-Model_ ist Javascript Code und enthält sowohl Daten als auch  das Verhalten der _View_. Aurelia's mächtiges _databinding_ verbindet diese beiden Teile und ermöglicht so, dass Änderungen in den Daten automatisch in der _View_ aktualisiert werden und umgekehrt genauso. Diese "Separation of Concerns" (zu deutsch Trennung der Veranwortlichkeiten) ist in vielerlei Hinwicht perfekt: für eine Zusammenarbeit zwischen Entwicklern und Designern, für Wartung, Flexibiltät in der Architektur, und sogar für die Quellcodeverwaltung.

Sehen wir uns, wie's funktioniert...

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

Ja. Ja und Ja, es ist Javascript. Tatsächlich ist es ECMAScript 7 (ES7), die folgende der nächsten Version von  Javascript, welche viele neue spanndene Features enthalten wird. Glücklicherweise wurde unser automatisierter Build (Gulp) mit [Babel](https://babeljs.io/) konfiguriert. Babel ist ein Transpiler, der Features aus kommenden Javascript Versionen zurückportiert und somit ermöglicht, dass moderner Code auch in heute aktuellen Browsern ablaufen kann. Du kannst so auch heute schon Module, Klassen, Lambdas, String interpolation und vieles mehr verwenden. Genial! So, wie genau muss man nun das _View Model_ erstellen? Zuerst erstellen wir eine leere Klasse und exportieren diese ins Framework.

> **Hinweis:** Du musst nicht Babel oder ES7 Features verwenden, um eine Aurelia Anwendung zu erstellen. Du kannst auch Sprachen wie TypeScript / CoffeeScript oder einfach heute aktuelles ES5 Javascript verwenden. Du musst Dich einzig an die Standardmuster für's Erstellen von Klassen halten und alles ist perfekt. Trotzdem halten wir ES7 für genial und hoffen, Du siehst es genauso. Um mehr über die neueste Version von Javascript und deren geniale Features wie Module und Klassen zu erfahren, empfehlen wir [Babel Learning Guide](http://babeljs.io/docs/learn-es2015/).

OK. Nun haben wir das _View-Model_ mit einigen Basisdaten und etwas Verhaltens implementiert, schauen wir uns nun dessen Gegenspieler an - die _View_.

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

Alle Views werden in ein `template` Tag eingeschlossen. Diese View ist ein einfaches Eingabeformular, welches mit bootstrap verschönert wurde. Interessant sind die _input_ Eingabefelder. Siehst Du `value.bind="firstName"`? Das verknüpft den Wert (_value_) des Eingabefeldes mit der _firstName_ Eigenschaft unseres View-Model. Jedes Mal, wenn sich die Eigenschaft im Model ändert, wird das Eingabefeld mit dem neuen Wert aktualisiert. Immer, wenn sich der Wert im Eingabefeld ändert, setzt Aurelia den neuen Wert ins View-Model. Wir nennen dies Zwei-Wege Verknüfung _two-way binding_ - so einfach ist das!

Es gibt noch weitere interessante Dinge in diesem Beispiel: In der letzten _form group_ siehst Du eine weitere bislang nicht spezifizierte Syntax im HTML: `${fullName}`. Das ist eine String Interpolation - eine Einwegverknüfung (one-way binding) vom _View-Model_ zur _View_, welche die Daten aus dem View-Model automatisch als String ins Dokument einfügt. Zum Schluss sehen wir und das _form_ Element selbst an: Mit  `submit.delegate="welcome()"` wird ein Ereignis an eine Methode gebunden (event binding). Durch Weiterleitung des Ereignisses (event delegation) bewirkt das _submit_ Ereignis ein Ausführen der _welcome_ Methode immer dann, wenn das Formular versendet wird.

> **Hinweis:** Falls Du noch nie etwas über _event delegation_ gehört hast: Es ist eine Technik um Browsereignisse effizienter zu verarbeiten, indem auf Dokumentenebenede ein einziger _event handler_ exisitiert, welcher sich um alle Ereignisse eines bestimmeten Types kümmert, anstatt jedem Knoten im DOM einen eigenen  _event handler_ zu zuweisen.

OK,  Mit dem folgenden Kommando können wir nun die Anwendung zusammen bauen und den Server starten:

```shell
gulp watch
```

Gib im Browser [http://localhost:9000/](http://localhost:9000/) ein und schau Dir unsere Anwendung an. Gib etwas in die Eingabefelder des Formulars ein und schau, wie sich der _Full Name_ entsprechend ändert. Klick auf den Button und sieh die Willkommensnachricht.

> **Hinweis:** Wenn es nicht funktioniert, versuche ein [Update](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) auf die aktuelle Version von npm.

> **Hinweis:** Aurelia's einzigartiger und mächtiger databinding Mechanismus verwendet adaptive Techniken, um herauszufinden, welcher Weg der Beste zur Überwachung von Änderungen in Properties ist. Wenn Du z.B. einen Browser mit [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) Unterstützung verwendest, werden _firstName_ und _lastName_ mit dieser Strategie überwacht. Falls nicht, werden Getter und Setter generiert, welche die Änderungen in eine Warteschlange (Micro Task Queue) einsteuern, welche dann das Object.observe Verhalten korrekt emuliert. Wenn die berechntete Eigenschaft _fullName_ mit solchen Techniken nicht überwacht werden kann, verwenden wir _dirty checking_, welches periodische Checks / Aktualisierungen vornimmt. Zur Optimierung können optional Abhängigkeiten (in unserem Fall zu Vor- und Nachnamen) deklariert werden. Es wird immer die beste technik verwendet, Du kannst sogar eigene Überwachungsstrategien implementieren und dem Framework "beibringen". Das ist ziemlich cool.

> **Hinweis:** Das `.bind` Kommando verwendet das default binding Verhalten für jede Eigenschaft. Per default ist two-way binding (Model <--> View) nur für Formular Eingabekomponenten vorgesehen, alles Andere ist one-way (Model zur View). Du kannst dieses Verhalten immer überschreiben, und zwar indem dies explizit spezifierte wird `.one-way`, `.two-way` and `.one-time`. Ebenso ist `.delegate` Standard für _event delegation_, Du kannst dies  aber mit `.trigger` überschreiben, um direkt das Ziel Element anzusprechen.

## Hinzufügen der Navigation
