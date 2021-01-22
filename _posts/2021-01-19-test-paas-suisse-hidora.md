---
layout: post
title: Essai du PaaS Suisse, Hidora
subtitle: 
categories:
- blog
catalog: true
date:       2021-01-19
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1491555103944-7c647fd857e6?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1950&q=80
tags:
    - PaaS
    - Performances
    - Hidora
    - Hosting
    - Cloud
---

Ce billet s'inscrit dans la suite d'une série d'articles visant à faire un retour d'expérience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ WordPress.

Dans la série initiale :

{% include sommaire-paas.html %}

Bien que ma quête pour héberger notre parc de WordPress s'est achevée fin 2019, j'ai décidé de tester Hidora, un PaaS suisse 🇨🇭. 


---


## Hidora


[Accéder au résumé si vous êtes pressés](#résumé-hidora-tldr)


#### Build de l'application sur Hidora

Hidora propose des installations préconçues pour les principaux CMS, néanmoins, il n'est pas possible de choisir l'édition de WordPress désirée et l'unique proposée est l'édition basique, et non [Bedrock](https://roots.io/bedrock/). 

Comment build notre WordPress qui nécessite d'installer les dépendances `composer` et `javascript` ?

Plusieures difficultés se sont posés pour arriver à déployer notre projet avec  WordPress - Bedrock:

- Les environnements PHP chez Hidora n'embarquent aucune runtime `Node.js`, il n'est pas possible d'y faire `yarn install && yarn build` pour générer les fichiers `css` et `js`. Il a donc fallu identifier la distribution sur laquelle tourne le scaler (CentOS), puis installer `Node.js`, puis `yarn` à la main sur le noeud PHP en SSH, en ayant au préalable demandé au support de nous mettre les droits `root` sur notre environnement. 

- Lorsque nous devons personnaliser le script de "post deploy" pour y rajouter notre `composer install`, nous nous attendons à être à la racine du dossier contenant notre application déployée, ce n'est pas le cas. Il n'a pas été facile de savoir ou se rendre pour rentrer dans notre répertoire applicatif (`cd $WEBROOT/app`).

- Il n'y a malheureusement pas de multi-edition des variables d'environnements. J'ai du en saisir 30 à la main, une par une: c'est long. Avoir une zone de texte éditable pour pouvoir coller toutes nos variables aurait été plus rapide.

Je pense que la frustration ressentie face à ces nombreuses problématiques/questions peuvent être réglées par l'édition de "cookbook" pour répondre aux questions que les nouveaux clients doivent se poser quand ils arrivent sur Hidora (ex: Comment déployer une application Symfony, WordPress...). Les seuls guides que j'ai trouvés sont ceux de la solution qu'à choisi Hidora pour exploiter son cloud: [Jelastic](https://jelastic.com/).

Capture d'écran de l'interface d'administration : 
![Interface Hidora UI](/img/paas/hidora-ui.png)



#### Performance chez Hidora

Voilà une des typologies de l'infrastructure faisant tourner le WordPress. Les visuels viennent faciliter la compréhension et les ressources allouées.

![Hidora Typologie Infrastructure WordPress](/img/paas/hidora-typology.png)

Exemple : J'envoie 5 visiteurs uniques par seconde pendant 2 minutes sur la page d'accueil. Pas d'optimisations excepté le `pm.max_children` réglé sur 10.

Pour **10,8€**/mois chez <u>Scalingo</u>, j'obtiens **492ms** de moyenne de temps de réponse.

Pour **15,7€**/mois chez <u>Hidora</u>, j'obtiens **2 048ms** de moyenne de temps de réponse.

Les performances ne sont donc pas bonnes par défaut. Je pense qu'il serait possible d'obtenir de meilleurs performances, mais n'ayant eu à appliquer d'autres améliorations sur les autres plateformes testés, je ne l'ai pas fait ici non plus.

Pour plus de détail, voir :
* Part7. [Comparatif PaaS : Les performances, Clever Cloud VS Scalingo VS Hidora]({% post_url 2019-01-24-clevercloud-vs-scalingo %})


Notons qu'Hidora propose une tarification à la ressource réellement consommée. Nous pouvons reserver 2Ghz/2Gb de RAM, et dire que nous autorisons jusqu'à 6Ghz/6Gb de RAM consommés. Si votre applicatif consomme par moment 3,4Ghz de CPU, vous serez facturé uniquement sur la base de cette consommation, et non la borne maximale (6Ghz) choisie ce qui permet de mieux maitriser les coûts lors de pics de charge.

### Résumé Hidora (TLDR)

####  👎 Inconvénients

- Interface basé sur Jelastic: complexité de l'interface, peu sexy.
- L'interface Jelastic aurait pu être plus orienté UX.
- La flexibilité peut introduire des mauvaises pratiques (= modifications à la main sur le serveurs de fichiers de config)
- PaaS pas vraiment 100% "PaaS": on se retrouve à installer des choses manuellement en `SSH` via `apt`
- Le build de l'application se fait sur la même machine qui éxecute le code. J'ai dû augmenter la RAM disponible de la plateforme juste pour le build, alors que pour le run, il n'y en avait pas réellement besoin.
- Pas de possibilité de déporter facilement (1 click) le build de l'application sur un scaler, plus gros, à part afin de ne payer que la consommation de ce scaler au moment où l'on en a besoin.
- Performances brutes par défaut (sans avoir à faire des optimisations)
- Le support ne propose pas un canal "live chat"
 
#### 👍 Avantages

- Interface basé sur Jelastic: les ressources internes sont dédiées à autre chose que développer l'UI.
- Très flexible (accès `root`, `sFTP`, `ssh`). C'est top si vous avez beaucoup d'intégrations qui ne sont pas supportés par d'autres PaaS.
- Beaucoup de technologies supportés, dont Docker
- Le support qui à pris le temps de répondre a certaines problématiques par visioconférence, en écran partagé.
- Les data sont hébérgées en Suisse.
- Les ressources consommées sont celles réellement facturées lorsque l'on utilise des ressources flexibles.


### Verdict

Je ne suis pas sûr d'être en mesure de recommander Hidora dans le scénario ou un **développeur** serait chargé de configurer et déployer un applicatif (comme on peut le retrouver dans des petites équipes). En effet la couche d'UI fournie par Jelastic est, je trouve, assez complexe à prendre en main et l'expérience nécessite plus de compétences "infra / ops / run" que d'autre hébergeur 100% PaaS que j'ai évoqué dans cette série d'articles.

On peut se retrouver à faire des opérations hybrides entre de la configuration au travers de l'UI, comme l'on peut s'y attendre pour un PaaS. Mais, de manière plus surprenante, on peut aussi avoir à modifier via `sFTP` ou via `SSH` des fichiers de configuration (`php.ini`, `vhost.conf`) ce qui donne l'impression de configurer un serveur dédié ou un VPS.

Pour les clients qui découvrent l'interface Jelastic, utilisé par Hidora, le partage d'écran avec le support est nécessaire pour y voir plus clair.

⚠️ Mon avis sur cet hébérgeur ne concerne que l'utilisation que j'en ai faite. Honnêtement, je ne suis pas sûr que mon cas d'utilisation tire le meilleur parti de cet hébérgeur ⚠️