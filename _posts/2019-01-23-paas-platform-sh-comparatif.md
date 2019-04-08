---
layout: post
title: Les PaaS Français sont sur un bateau &#58; Platform.sh au mouillage
subtitle:  Comparatif d'offres PaaS
categories:
- blog
catalog: true
date:       2019-01-23
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1495757450029-09dbedacbc36?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2089&q=80
tags:
    - PaaS
    - Platform.sh
    - Performances
---


Ce billet s'inscrit dans une série d'articles visant à faire un retour d'expérience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ WordPress.

Dans la série :

{% include sommaire-paas.html %}

---


## Platform.sh

[Accéder au résumé si vous êtes pressés](#résumé-platformsh-tldr)


Pour nos projets Symfony sur mesure, nous sommes chez [Platform.sh](https://platform.sh/) (~7 projets).

Je l'ai rapidement éliminé dans le contexte d'une offre d'hébergement de plus de 120+ projets Wordpress car le pricing n'est pas du tout adapté à notre contexte.

**40€ pour 0.8GB de mémoire**, sachant qu'elle est partagée entre les containers applicatifs ; donc 800Mo de mémoire divisé entre le container web, et le container de la base de données, ça ne fait pas vraiment rêver....

{% include image.html width="610" url="/img/platformsh-memory.png" description="Répartition de la mémoire sur les plans Platform.sh. Pour un service avec du PHP et une base de données, le plan S ne suffira pas." %}

<br />

Le plan supérieur est à 100€/mois. Et là, on fait x10 sur nos dépenses, sachant que dans notre contexte, la fonctionnalité pour utiliser différents environnements ne nous intéressait pas.

La gestion des variables d'environnement, comment dire... c'est compliqué : on pense que l'onglet variable sur la branche en question suffirait ? En fait non, car elles ne sont accessibles qu'au runtime et pas au build.

Pour rajouter des variables au build, il faut aller cliquer sur une zone de 20 par 20 pixels pas du tout miss en avant et rajouter les variables. On se retrouve souvent alors à rajouter les variables dans la configuration du projet, puis à les dupliquer dans la configuration de l'environnement master afin de bénéficier de l'héritage sur les autres branches.

Notez qu'il n'y a pas d'édition de variables d'environnement en mode "Bulk Edit/Add".

Des efforts sur l'UI et l'UX/DX sont à prévoir car l'interface génère régulièrement des frustrations (en plus d'être lente).

Les incidents sont fréquents (ou alors je n'ai pas de chance et le peu de fois où j'ai besoin de Platform.sh, il y a un incident...), du type "ah tiens, mes builds sont bloqués", "l'application n'est plus auto-deployée, donc mes correctifs que je pensais être déployés en prod il y a 7 jours ne sont jamais arrivés". Heureusement, la prod n'est jamais tombée sur nos projets, mais ces problèmes à répétition sur l'utilisation du PaaS sont usants.

Pour illustrer mes propos, de décembre à janvier, il y a eu 10 incidents répertoriés, certains majeurs, sur leur [statuspage](https://status.platform.sh/history) (hors maintenance). A l’heure où j’écris cet article, mon équipe me signale qu’elle est encore bloquée pour déployer certains environnements.

### Résumé Platform.sh (TLDR)

#### 👎 Inconvénients

- Cher
- Pas de scalabilité automatique
- On ne sait pas combien de ressources on consomme
- Interface très lente et pas sexy
- Gestion des variables d'environnement
- Incidents à repetition
- Hook de déploiement qu'il faut régulièrement supprimer et rajouter pour que le projet continue à se déployer automatiquement
- Aucun outil de monitoring depuis l'interface pour aller créer des règles d'alertes (ex : Poster un message dans Slack si la RAM >90%). Il y a uniquement un check sur l'espace disque
- Backups pas automatisés, ni proposés de manière automatique. Il faut s'occuper soi-même de créer un cron de snapshot

#### 👍 Avantages

- On peut déployer des branches comme environnements pour faire des démos ou de la préprod facilement
- Système d'héritage des variables d'environemment qui facilite la création d'autres environnements
- Copie de la base de données de production lors de la création d'un nouvel environnement

<br />

⛔️  **VERDICT : ELIMINÉ** ⛔️

Aucun benchmark n'a été réalisé avec Wordpress, tant que la projection d'une migration chez eux bloquait au niveau tarifaire.

## Lire la partie VI

* Part6. [Comparatif PaaS : Les performances, Clever Cloud VS Scalingo]({% post_url 2019-01-24-clevercloud-vs-scalingo %})
<br />
