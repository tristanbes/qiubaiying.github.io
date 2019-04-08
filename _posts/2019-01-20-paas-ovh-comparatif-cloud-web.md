---
layout: post
title: Les PaaS Français sont sur un bateau &#58; OVH tombe à l'eau
subtitle:  Comparatif d'offres PaaS
categories:
- blog
catalog: true
date:       2019-01-20
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1495757450029-09dbedacbc36?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2089&q=80
tags:
    - PaaS
    - Performances
    - OVH
---

Ce billet s'inscrit dans une série d'articles visant à faire un retour d'expérience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ WordPress.

Dans la série :

{% include sommaire-paas.html %}

---




## OVH et son offre Cloud Web

[Accéder au résumé si vous êtes pressés](#résumé-ovh-cloud-web-tldr)



OVH est le dernier arrivé dans le monde du PaaS avec son [offre Cloud Web](https://www.ovh.com/fr/hebergement-web/cloud-web.xml), ce n'est pas son domaine d'expertise, et ça se ressent vite, très vite.

Tout d'abord, première mauvaise surprise, il est impossible de commander une offre Cloud Web pour moins de 12 mois.

Si vous ne souhaitez pas commander un nom de domaine avec l'offre Cloud Web (car vous en avez déjà un), il est impossible de sauter cette étape lors de la commande de l'offre par l'interface OVH. Il est cependant possible de contourner cette étape si vous commandez l'offre par l'API d'OVH.

A priori, les équipes travailleraient à lever ces contraintes, mais de source interne, ça ne sera pas traité avant de longs mois (sans garanties). Mais soyons clairs, certains concurrents proposent de la **facturation à la minute**, donc débourser ~490€ TTC pour faire quelques tests sur du Cloud Web 3, ou même si l'on se projette sur notre parc de Wordpress de 120+ projets, cela reviendrait à un ticket d'entrée de ~**60,000€** sans garantie que nos clients restent un an chez nous.

Je suis rentré en contact avec une personne de chez OVH pour lui faire remonter ces problèmes. On m'a proposé de m'offrir l'offre Cloud Web 3 pour 1 an, en échange de feedbacks sur le produit. Merci pour cette proposition.

Dès que j'ai eu accès au produit, j'ai voulu mettre PHP 7.3 et créer mes variables d'environnement afin de pouvoir configurer le wordpress et faire mes tests de performances. Mais je me suis heurté à des problèmes, dont un qui m'a empêché toute utilisation du produit :

- PHP 7.3 n'est pas disponible, il est pourtant sorti le 6 Décembre 2018, soit depuis plus d'un mois lors de la création de cet article
- Pas de gestion de permissions (autre que pour le gestionnaire/contact technique) pour donner par exemple accès à la gestion des variables d'environnement pour un ensemble de personnes d'une équipe
- J'ai créé des variables d'environnement, certaines se sont créées en quelques secondes/minutes, et d'autres, rien après 15 minutes de "En cours de création". Je me suis mis à fouiller l'UI avant de découvrir que la tâche de création de certaines variables était en erreur (sans plus de détails)
- Pas de gestion de "bulk insert" des variables d'environnement. J'ai dû répéter les opérations de création de variable 20 fois à cause de ce manque

<br />
{% include image.html width="610" url="/img/ovh-environment-variables-bug.png" description="La création de ces variables d'environnement sur OVH sont 'en cours' depuis 14 jours sans possibilité de les supprimer ou de les modifier" %}

<br />

#### Le support d'OVH

Sans possibilité de modifier ou de supprimer les variables "fautives", **je suis bloqué** dans ma configuration, je me tourne donc vers le support.

- Le 8 janvier 2019, ouverture du ticket au support pour signaler que je ne peux pas utiliser le produit à cause de mon problème de variables d'environnement
- Le 21 janvier 2019, soit **13 JOURS après**, je reçois la première réponse du support qui peut être résumé à : "Nous sommes désolés pour la prise en charge tardive du ticket, des administrateurs sont en train de vérifier votre demande."
- Le 23 janvier 2019, on m'informe que les opérations en erreur ont été annulées, et on m'invite à réessayer sans m'expliquer ce qu'il s'est passé.



#### Résumé OVH Cloud Web (TLDR)

#### 👎 Inconvénients

- Engagement annuel
- Pas de paiement mensuel
- Impossible de commander sans domaine associé par l'interface web
- Documentation très pauvre et incomplète
- Pas de déploiement par Git
- Pas de déploiement automatique via app Github/Gitlab
- Pas de scaling horizontal, vertical
- Pas [d'auto-scaling](https://en.wikipedia.org/wiki/Autoscaling)
- Le support d'OVH : à proscrire
- Pas de gestion de permissions pour donner les droits d'accès à une équipe sur les variables d'environnement par exemple
- UX/DX pauvres sur la gestion des variables, en plus d'être buggées

#### 👍 Avantages

- Backup automatisés
- Intégration avec let's encrypt
- Réseau OVH
- C'est à peu près tout... 🤷‍♂️😱

<br />

#### ⛔️ **VERDICT : ELIMINÉ** ⛔️

Difficile d'émettre un jugement définitif quant aux offres PaaS d'OVH tant que l'on ressent qu'elles ont besoin de maturer encore afin de répondre au besoin du marché. Honnêtement, j'étais frustré et déçu sur le fait de ne pas avoir pu benchmarker la performance de leur offre Cloud Web 3.

Mes aprioris sur le support se sont malheureusement confirmés, de ce fait, je ne peux conseiller à personne cette offre dans le cadre professionnel, où au moindre souci, vous attendez des semaines la réponse du support (à moins de les harceler sur Twitter), et ce, même si c'est critique pour le service.

Quant à une utilisation personnelle, qui est capable de sortir ~490€ d'un coup pour héberger son "side project" personnel pour une offre PaaS ? 🙊


## Lire la partie III

  * Part3. [Comparatif PaaS : Clever Cloud, le bateau prend l'eau]({% post_url 2019-01-21-paas-clever-cloud-comparatif %})

<br />








