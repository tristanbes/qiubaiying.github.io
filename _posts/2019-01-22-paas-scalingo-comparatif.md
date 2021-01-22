---
layout: post
title: Les PaaS Français sont sur un bateau &#58; Scalingo reste à flot
subtitle:  Comparatif d'offres PaaS
categories:
- blog
catalog: true
date:       2019-01-22
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1495757450029-09dbedacbc36?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2089&q=80
tags:
    - PaaS
    - Performances
    - Scalingo
    - Hosting
    - Cloud
---


Cette série de billets a pour but de faire un retour d'expérience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ WordPress.

Dans la série :

{% include sommaire-paas.html %}

---

## Scalingo

[Accéder au résumé si vous êtes pressés](#résumé-clever-cloud-tldr)

[Scalingo](https://sclng.io/r/dfa62f9bc47ec843) est un service PaaS dont je n'avais jamais entendu parler (ni sur ma timeline Twitter), c'est un peu à reculons que je l'ai ajouté dans ma liste de "candidats".

Ici, l'onboarding est très simple, je n'ai pas dû aller négocier un budget pour tester le service : **tout est facturé à la minute**. De ce fait, tous les services qui facturent au mois prennent un gros coup de vieux.

Si vous possédez un site qui passe sur Capital, vous pourrez commander XX scalers supplémentaires afin que l'application tienne la charge, et n'être facturé que sur les 20 minutes durant lesquelles le reportage aura parlé de vous. Et ce même pour la base de données, afin d'augmenter les fameuses limites de connexions simultanées (même si dans ce cas, il serait préférable de passer le site en lecture seule, et mettre un reverse proxy) et de RAM.

L'interface ne colle plus aux tendances actuelles : elle fait un peu vieillotte, mais elle est agréable à l'utilisation et très réactive. Il vaut mieux ça qu'une interface très "belle", mais mal pensée et lente.

#### Build de l'application sur Scalingo

Un fichier `.buildpacks` doit être créé à la racine du projet contenant l'URL d'un dépôt git afin de dire au PaaS comment construire l'application. Dans notre cas c'était : `https://github.com/Scalingo/php-buildpack`

Le `composer.json` est alors détecté, et **bonus**, l'édition de Wordpress Bedrock étant supportée chez eux, je n'ai rien eu à faire pour configurer le Vhost NGINX, ça a fonctionné de manière automatique ([liste des framework PHP auto-détectés](https://github.com/Scalingo/php-buildpack/tree/master/frameworks)), ce qui n'a pas été le cas par exemple chez Clever Cloud où il a fallu indiquer où était le front controller de l'application (`web/index.php`).

L'application se build rapidement et j'ai pu procéder aux tests.

#### Performance chez Scalingo

Les tests parleront d'eux-mêmes dans le chapitre dédié. Notez que [Scalingo](https://sclng.io/r/dfa62f9bc47ec843) écrase Clever Cloud qui ne passe même pas certains tests à tarif équivalent.

Quelques fois, nous avons obtenu des performances bien meilleures lors de tests réalisés à des dates différentes chez Scalingo. Après avoir contacté le support, cela s'expliquerait par le voisinage. En effet, les scalers peuvent bouger de machine en machine selon leur charge. Si nous avons des voisins qui possèdent des scalers avec une très forte priorité CPU (donc qui payent plus cher) et qui sont très actifs sur le moment alors moins de temps CPU sera alors alloué à nos containers avec une priorité plus faible.

* Part7. [Comparatif PaaS : Les performances, Clever Cloud VS Scalingo VS Hidora]({% post_url 2019-01-24-clevercloud-vs-scalingo %})


### Résumé Scalingo (TLDR)

#### 👎 Inconvénients

- UI pas très sexy sans être catastrophique non plus.
- Autoscaling (en BETA fermée) qui demande encore d'être un peu ajusté
- Interface de gestion des logs qui pourrait être améliorée visuellement
- Pas de support de Github Server (pour l'app de déploiement)
- Pas (encore) de vision "d'ensemble" pour les portefeuilles avec beaucoup de projets
- Pas de reverse proxy (ex : Varnish) facilement configurable depuis un `.vcl ` déposé à la racine de l'application afin d'augmenter les performances applicatives en tirant parti du cache HTTP
- **EDIT 12/20** Au moins 2 incidents majeurs de la plateforme sur 2020 suite à des attaques [DDOS](https://scalingo.com/blog/mitigating-massive-ddos)

#### 👍 Avantages

- Tarifs compétitifs
- Tout est facturé à la "minute", même la base de données
- Auto-scaling horizontal
- Support réactif. **EDIT 3/12/20** Toujours très réactif, et souvent un développeur est à l'autre bout qui comprends tout de suite la problématique et apporte une solution rapidement. C'est très appréciable.
- Interface agréable à utiliser et réactive (UX/DX)
- Gestion facilitée du `pm.max_children` depuis l'UI (variable `MAX_CONCURRENCY`)
- Gestion d'environnements différents pour une même application (prod/préprod...), mais pas aussi facile que ce que peut proposer Platform.sh par exemple
- Gestion simple et assez complète des règles de monitoring de vos scalers sur différents cannaux (slack, mail...)
- **EDIT 3/12/20** Environnement plus flexible que certains PaaS sur certaines extensions: compatible avec Blackfire, Datadog...


#### ✅ **VERDICT : RETENU** ✅

[Scalingo](https://sclng.io/r/dfa62f9bc47ec843) a été pour moi **LA** bonne surprise de ce comparatif. Les tarifs sont compétitifs par rapport au marché, sans compromis sur les performances. Les principales features que l'on peut attendre d'un PaaS sont là et fonctionnent bien !


## Lire la partie V

* Part5. [Comparatif PaaS : Platform.sh]({% post_url 2019-01-23-paas-platform-sh-comparatif %})

<br />

---

<u>Disclaimer :</u> les liens Scalingo sont câblés sur un système de parrainage rattaché à mon compte personnel. Si les autres PaaS testés ici avaient eu ce même système de parainage, j'aurais aussi utilisé ce système sur leurs liens (pour faire des futurs POC ou petits projets persos).
