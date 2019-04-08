---
layout: post
title: Le benchmark PaaS de Clever Cloud vs Scalingo
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
---

Cette série de billets a pour but de faire un retour d'expérience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ WordPress.

Dans la série :

{% include sommaire-paas.html %}

---

# Les performances mesurées

Sont retenus pour ce benchmark :
- ✅ Clever Cloud
- ✅ Scalingo
- ⛔️ OVH Cloud Web (impossible de faire tourner le projet, voir Partie II)
- ⛔️ Platform.sh (trop cher pour notre contexte Worpress)

Les tests ont été réalisés dans des conditions réelles. J'ai pris 2 Wordpress "représentatifs" déjà en production chez nous. Il ne s'agit pas de tests sur un wordpress sans plugins qui ne comporte qu'une page vierge.

**Les tests chez les différents PaaS ont été réalisés dans les même conditions** : Même versions de wordpress et des plugins, même page testée.


J'ai effectué plus de **80 tests** avec un outil de test de montée de charge ([loader.io](https://loader.io/)) avec des scénarios différents :

- XX clients par seconde pendant 2 minutes
- 10 clients par seconde pendant 10 minutes
- XX clients [progressifs et maintenus](https://support.loader.io/article/16-test-types#maintain-load), pendant 2 minutes


Avant de consigner les résultats obtenus, j'ai passé quelques temps à trouver la valeur optimale pour la configuration `pm.max_children` qui permet de dire aux scalers combien de requêtes PHP concurrentes il est capable de traiter.

Une valeur trop haute, et ce sont les performances qui sont plombées à cause du manque de RAM (qui swap). Une valeur trop faible, et le scaler est alors bridé, et vos performances aussi.


⚠️ Note sur les pourcentages de réponse du serveur : notez que sur les tests de type XX clients par seconde sur Y minutes, loader.io peut envoyer un peu moins de clients. Quand le résultat est à 95% on peut estimer que c'est justement cette différence de clients qui s'exprime. Par contre, quand on obtient 20%, pas de doute, là, c'est bien le serveur qui n'a pas pu gérer toutes les requêtes, et n'a répondu qu'à 2 requête sur 10. ⚠️

⏲ Loader.io ne parse pas l'HTML, ni ne télécharge le JS, CSS ou les images. Le temps de réponse correspond donc uniquement à la génération de la page envoyée par le serveur. ⏲


### Résultat des tests (extraits)

#### 5 clients par seconde sur 2 minutes

|              | Prix/mois | Détail  | BDD  | Moyenne (ms) | % de réponse du serveur |
| ------------ | --------- | ------- | ---- | ------------ | ----------------------- |
| Scalingo     | 21,60€    | 1x S    | 1G   | 764          | 100%                    |
| Clever Cloud | 51,00€    | 1x nano | M    | 3,185        | 53,2%                   |
| Clever Cloud | 73,80€    | 1x S    | M    | 1,088        | 98,9%                   |

<br />

#### 10 clients par seconde sur 2 minutes

|              | Prix/mois | Détail | BDD  | Moyenne (ms) | % de réponse du serveur |
| ------------ | --------- | ------ | ---- | ------------ | ----------------------- |
| Scalingo     | 21,60€    | 1x S   | 1x1G | 1,246        | 98,3%                   |
| Scalingo     | 28,80€    | 2x S   | 1x1G | 930          | 99,9%                   |
| Clever Cloud | 59,40 €   | 1x XS  | 1xM  | 6,121        | 29,2%                   |
| Clever Cloud | 275,40 €  | 8x M   | 1xM  | 977          | 98,8%                   |

<br />

#### 10 clients par seconde sur 10 minutes

| 10 clients/s sur 10 minutes | Prix/mois | Détail | BDD  | Moyenne (ms) | % de réponse du serveur |
| --------------------------- | --------- | ------ | ---- | ------------ | ----------------------- |
| Scalingo                    | 28,80€    | 1x M   | 1x1G | 813          | 100%                    |
| Clever Cloud                | 189,00 €  | 2x M   | 1xM  | 1,151        | 99,5%                   |

<br />

Pour voir l'exhaustivité des tests conduits, je vous mets à disposition ma feuille de calcul google sheet qui contient 2 onglets : **[exhaustivité des ~80 tests du comparatif de performances de PaaS entre Scalingo et Clever Cloud](https://docs.google.com/spreadsheets/d/1hrny3Rf4RB7qqezy8jHQ_DbZXGzBK293sttXRlmxUMA/edit?usp=sharing)**



#### Configuration des tests

- Edition Wordpress [Bedrock](https://roots.io/bedrock/) afin que WP soit compatible avec un hébergement PaaS, en plus de proposer des outils modernes (gestion de la conf par variables d'environnement, gestion des plugins wordpress par `composer`...)
- PHP 7.3.x
- Site 1
  - Wordpress 5.0.1 + Woocommerce
  - Liste des dépendances et plugins : [composer.json](https://gist.github.com/tristanbes/c5223abc49c4feb2bdde6e495762c31b)
- Site 2 (non testé sur Clever Cloud, à cause des performances qu'il a obtenu lors des tests du Site 1)
  - Wordpress 4.9.2
  - Liste des dépendances et plugins : [composer.json](https://gist.github.com/tristanbes/bd2684b4b90fc1a182540ce89290fdc8)
  - Site "peu performant" sur la homepage
- `pm.max_children` est à `10` pour les scalers S et `20` pour du M chez Scalingo / et `10` pour du XS et `20` pour du S chez Clever Cloud (valeur rapportée dans la colonne "Remarque" du google sheets)


