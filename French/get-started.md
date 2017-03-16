# Démarrage

**Bienvenue sur Aurelia !** Ce didacticiel va vous aider à créer une application en utilisant Aurelia et en expliquant brièvement ses concepts principaux. Nous assumons que vous êtes déjà familié avec le JavaScript, l'HTML et le CSS. Afin d'avoir un aperçu rapide, nous vous conseillons de directement aller à la section "Créer la page HTML" pour directement voir comment utiliser Aurelia. Par la suite, lorsque vous serez prêt de construire quelque chose, revenez et lisez "Configurer votre environnement" et "Créer la structure du projet et le build". Vous trouverez le résutlat de ce didacticiel dans notre dépôt Github [navigation skeleton project](https://github.com/aurelia/skeleton-navigation/releases).

> **Note :** Ce guide est disponnible dans plusieurs langues. Les traductions sont disponnibles sur notre dépôt Github [documentation repo](https://github.com/aurelia/documentation).
 
## Configurer Votre Environnement
 
Commençons par installer les outils que nous utilisons afin de construire une application JavaScript moderne. Tous nos outils ont été construit avec [Node.js](http://nodejs.org/). Si vous ne l'avez pas déjà installé sur votre machine, vous devriez aller sur [le site officel](http://nodejs.org/), le télécgharger et l'installer. Tous ce que nous avons besoin sera installer directement grâce au gestionnaire de paquet de NodeJS ([npm](https://docs.npmjs.com/getting-started/what-is-npm)). Si vous aviez déjà npm d'installé, vérifiez que vous possédez la [dernière version](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) pour éviter tout problème avec un utilitaire.

Premièrement, commençons par installer [Gulp](http://gulpjs.com/) que nous utiliserons pour compiler automatiquement notre code. Si vous ne l'avez pas déjà, vous pouvez utiliser npm pour l'installer comme suit :

```shell
npm install -g gulp
```

Par la suite, vous devez installer [jspm](http://jspm.io/) (JavaScript Package Manager). Celui-ci est un gestionnaire de paquet du côté client.

```shell
npm install -g jspm
```

> **Note : ** jpsm, comme Bower et Yeoman, requiert [git](http://git-scm.com/) pour fonctionner correctement, vous devez donc l'installer si ce n'est pas déjà fait. Il faut également noter que jpsm envoie des requêtes à Github pour installer les paquets, les requêtes sont envoyées en tant qu'Anonymes et Github possède une limite pour ce type de requête. Vous devez configurer jspm pour fonctionner avec vos identifiants de Github afin d'éviter d'avoir des problèmes. Vous pouvez le faire en exécutant la commande `jspm registry config github` puis en suivant ce qui est écrit à l'écran. Si vous ne voulez pas utiliser jspm tous les paquets d'Aurelia sont disponibles via [Bower](http://bower.io/) également.
 
## Configurer la Structure du Projet et la Compilation
