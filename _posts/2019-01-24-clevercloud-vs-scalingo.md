---
layout: post
title: Le benchmark PaaS de Clever Cloud vs Scalingo vs Hidora
subtitle:  QUID des performances par rapport au prix ?
categories:
- blog
catalog: true
date:       2019-01-24
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1495757450029-09dbedacbc36?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2089&q=80
tags:
    - PaaS
    - Clever Cloud
    - Performances
    - Scalingo
    - Hosting
    - Cloud
---

Cette série de billets a pour but de faire un retour d'expérience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ WordPress.

Dans la série :

{% include sommaire-paas.html %}

---

**EDIT 01/21** Les tests de performances ont été ré-actualisés. La première fournée de tests en 2019, accessibles ici: **[exhaustivité des ~80 tests du comparatif 2019 de performances de PaaS entre Scalingo et Clever Cloud](https://docs.google.com/spreadsheets/d/1hrny3Rf4RB7qqezy8jHQ_DbZXGzBK293sttXRlmxUMA/edit?usp=sharing)** contenait 80 tests. 
En 2021, j'ai été beaucoup plus concis sur les tests, disponibles sur [cette feuille de calcul](https://docs.google.com/spreadsheets/d/1Lv9eyBcLTh2hEt7-9YxrTPGz5QUB1OQ2GbOVl-xmahA/edit?usp=sharing).

# Les performances mesurées

Sont retenus pour ce benchmark :
- ✅ Clever Cloud
- ✅ Scalingo
- ✅ Hidora
- ⛔️ OVH Cloud Web (impossible de faire tourner le projet, voir Partie II)
- ⛔️ Platform.sh (trop cher pour notre contexte WordPress)

Les tests ont été réalisés dans des conditions réelles. J'ai pris un WordPress "représentatif" déjà en production chez nous. Il ne s'agit pas de tests sur un WordPress sans plugins qui ne comporte qu'une page vierge.

J'ai effectué les tests avec un outil de test de montée de charge ([k6.io](https://k6.io/)) depuis un serveur à Paris, FR : 5 visiteurs unique par seconde pendant 2 minutes. 

**Les tests chez les différents PaaS ont été réalisés dans les même conditions** : Même versions de wordpress et des plugins, même page testée. Aucune optimisation apportée excepté la variable d'ajustement `pm.max_children` pour gérer la concurrence des requêtes à `php-fpm`.

Avant de consigner les résultats obtenus, j'ai passé quelques temps à trouver la valeur optimale pour la configuration `pm.max_children` qui permet de dire aux scalers combien de requêtes PHP concurrentes il est capable de traiter.

Une valeur trop haute, et ce sont les performances qui sont plombées à cause du manque de RAM (qui swap). Une valeur trop faible, et le scaler est alors bridé, et vos performances aussi.

⚠️ Notez que le nombre de visiteur unique est variable, même si l'on règle le test sur 5 visiteurs uniques par secondes pendant 2 minutes. Je n'ai jamais obtenu 5x60x2 = 600 requêtes. Pensez donc à regarder le nombre de requête que le serveur à eu à traiter. ⚠️


### Résultat des tests (extraits)

#### 5 clients par seconde sur 2 minutes

|              | Prix/mois | Détail  | BDD  | Moyenne (ms) | Nombre de requêtes      |
| ------------ | --------- | ------- | ---- | ------------ | ----------------------- |
| Scalingo     | 10,8€    | 1x S    | 256M   | 492          | 335                    |
| Scalingo     | 18,0€    | 1x M    | 256M   | 451          | 342                    |
|      |       |    |           |                     |
| Clever Cloud | 11,9€    | 1x nano | XXS-Med (512Mb)    | 884        | 279                  |
| Clever Cloud | 20,3€    | 1x XS    | XXS-Med (512Mb)    | 588        | 313                   |
|      |       |    |           |                     |
| Hidora | 15,78€    | 2gb   |  256mb  | 2 042        | 239                   |
| Hidora | 28,70€    | 2gb   |  512mb  | 1110        | 237                   |


#### Configuration des tests

- Edition Wordpress [Bedrock](https://roots.io/bedrock/) afin que WP soit compatible avec un hébergement PaaS, en plus de proposer des outils modernes (gestion de la conf par variables d'environnement, gestion des plugins WordPress par `composer`...)
- PHP 7.4.x
- Site 1
  - Wordpress 5.6.x
  - Liste des dépendances et plugins : [composer.json](https://gist.github.com/tristanbes/b2b1f0ecbc83313cb3d5c1dc18555dfd)
- `pm.max_children` est reglé sur `10`


