# Docs

Aurelia va être prochainement accompagné d'une documentation convrant tous les aspects du Framework. Celle-ci n'étant pas encore finie, nous avons mis dans ce document les tâches les plus communes que vous pourriez vouloir réaliser. Si vous avez des questions, nous espérons que vous vous joindrez à nous sur notre [channel gitter (EN)](https://gitter.im/aurelia/discuss).

> **Note :** Vous voulez lire ce guide dans une autre langue ? Jettez un coup d'oeil à notre [documentation sur Github](https://github.com/aurelia/documentation).

<h2 id="browser-support"><a href="#browser-support">Browser Support</a></h2>

Aurelia a été initiallement créer pour les navigateurs récents. Ceux-ci sont Chrome, Firefox, IE11 et Safari 8. Toutefois, nous savons comment supporter IE9+. Pour cela, vous devez ajouter un polyfill additionnel (MutationObservers). Il vu suffit de simplement l'installer avec `jspm install github:polymer/mutationobservers`. Par la suite, il vous faut changer l'appel à `aurelia-bootstrapper` comme suivant :

```markup
<script src="jspm_packages/github/polymer/mutationobservers@0.4.2/MutationObserver.js"></script>
<script src="jspm_packages/system.js"></script>
<script src="config.js"></script>
<script>
  // Loads WeakMap polyfill needed by MutationObservers
  System.import('core-js').then( function() {
    // Imports MutationObserver polyfill
    System.import('mutationobservers').then( function() {
      // Ensures start of Aurelia when all required IE9 dependencies are loaded
      System.import('aurelia-bootstrapper');
    })
  });
</script>
```

> **Note :** WeakMap n'est pas requis par Aurelia directement mais est utilisé par MutationsObserver.

<h2 id="startup-and-configuration"><a href="#startup-and-configuration">Startup & Configuration</a></h2>

La plus part des plateformes possèdent un point d'accès principal. Aurelia ne déroge pas à cette règle. Si vous avez déjà lu la page [Get Started](/get-started.html), vous avez dû voir l'attribut HTML `aurelia-app`. Il vous suffit de simplement placer cet attribut dans un élément HTML et Aurelia va automatiquement charger les fichiers `app.js` et `app.html`, les lier puis les injecter dans le DOM où vous avez placé l'attribut.
