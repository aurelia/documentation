# Erste Schritte

**Herzlich Willkommen bei Aurelia!** In dieser kleinen Anleitung wird die Implementierung einer Beispielanwendung mittels Aurelia gezeigt und dabei die grundlegenden Ideen und Konzepte kurz vorgestellt. Wir gehen davon aus, dass Du bereits mit den JavaScript, HTML und CSS vertraut bist. Für einen kurzen Überblick kannst Du die folgenden Abschnitte überspringen und zu "Einrichten der HTML-Seite" springen. Wenn Du dann in der Lage bist, ein Projekt zu erstellen, fahre mit "Konfiguration der Systemumgebung" und "Aufsetzen der Projekt Struktur und der Build-Prozess". Das Ergebnis des Tutorials findest Du in unserem Projekt [Navigationsgerüst](https://github.com/aurelia/skeleton-navigation/releases).

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
