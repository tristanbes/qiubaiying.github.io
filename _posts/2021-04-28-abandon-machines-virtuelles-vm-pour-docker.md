---
layout: post
title: Un environnement de développement sain en 2021, bye bye les machines virtuelles (VM), bonjour Docker en mode hybride
subtitle: 
categories:
- blog
catalog: true
date:       2021-04-28
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1491555103944-7c647fd857e6?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1950&q=80
tags:
    - VM
    - Docker
    - Machine Virtuelles
    - PHP
    - environnement de développement
---



## Contexte

Depuis mai 2015, j'ai abandonné le modèle "tout installer à la main en local" pour une solution basée sur une machine virtuelle, provisionnée à l'aide de playbook [Ansible](https://www.ansible.com/) afin qu'en une commande, l'env soit opérationnel en quelques minutes.

Le pré-requis pour faire tourner les projets Symfony était aussi simple que d'installer en local 5 éléments:

* Make, afin de lancer les commandes
- [VirtualBox 6.0.8+](https://www.virtualbox.org/wiki/Downloads), pour la VM
- [Vagrant 2.2.5+](https://www.vagrantup.com/downloads.html), afin de piloter la VM
- [Vagrant Landrush](https://github.com/vagrant-landrush/landrush), un DNS pour accéder aux URL de développement
- [mkcert](https://github.com/FiloSottile/mkcert), pour générer le certificat TLS local

L'utilisateur était invité alors à exécuter dans son terminal `make setup`, puis une vingtaine de minutes plus tard, le projet était complètement opérationnel, les données de la base de données importés et toute l'équipe sur les mêmes versions majeures de PHP, Nginx, PostgreSQL.

Seulement voilà, nous sommes en 2021 et il y a eu du changement par rapport aux années 2014/2015, ce qui nous a poussé à abandonner ce modèle de machines virtuelles (VM).

## Pourquoi s'éloigner du modèle VM ?

Plusieures raisons m'ont poussé à nous éloigner du modèle de VM: 
* Docker qui devient de plus en plus mature et qui a un bon taux d'adoption sur les environnements de dev dans la communauté.
* Les performances un peu en retrait (surtout sur Linux si j'en crois mon équipe) par rapport à du local.
* Le poids des machines virtuelles (compter plusieurs Go par projets)

Était-ce suffisant pour faire arrêter une date butoire de transition vers autre chose ? Former les équipes ? Faire évoluer nos dizaines de projets vers une autre environnement de développement basé sur du Docker ? Pas tout à fait... surtout quand on entend que les performances avec Docker sur un hôte MacOS est désastreuses au pire, pas folle au mieux, lors d'opérations sur des volumes partagés.

L'élément qui à permis de faire basculer la balance vers "autre chose" est [l'annonce discrète par l'équipe de VirtualBox](https://forums.virtualbox.org/viewtopic.php?f=8&t=98742#wrap) qui ne pouvait techniquement pas porter VirtualBox sur un processeur ARM. VirtualBox, qui fait tourner la VM, est un hyperviseur, et non un émulateur. VirtualBox ne peut donc pas émuler un processeur x86. Le portage de VirtualBox ne peut donc pas s'effectuer sur un Mac avec un processeur sous ARM.

En quoi ça nous concerne ? Et bien peut-être avez vous raté [l'annonce d'Apple](https://nr.apple.com/dE7O5p9q0t) de leur plan de transition vers une architecture ARM en 2 ans environ avec leur puce "M1" (qui envoie du lourd).

Je vois venir les troll d'Apple au loin, mais dans mon équipe, j'ai toujours eu à coeur de proposer aux développeurs/euses le choix entre du Linux et du MacOS. Donc merci de rediriger vos troll vers les seuls qui le méritent: les devs PHP/JS sous Windows.


## Une solution hybride basée sur Docker et du local

Une solution basé uniquement sur Docker est compromise tant que le problème de performance soit définitivement reglé sur Mac et qu'il n'y à pas besoin d'un Bac+10 Docker/Linux pour implémenter la solution.

Nous avons choisi d'adopter une solution hybride afin d'éviter les volumes partagés (et donc pas de problème de performances). 

Sur la machine hôte (MacOS):
* PHP
* NodeJS
* Docker Desktop 2.2.0+
* Binaire Manala (Permettra de générer votre `docker-compose.yaml` voir [Aller plus loin](#aller-plus-loin))
* Symfony CLI. Ce dernier offre:
  *  la sélection de la [bonne version de PHP](https://symfony.com/doc/current/setup/symfony_server.html#selecting-a-different-php-version)
  *  un serveur web en Go
  *  [la gestion du TLS](https://symfony.com/doc/current/setup/symfony_server.html#enabling-tls), 
  *  un proxy pour la gestion des [noms de domaines en local](https://symfony.com/doc/current/setup/symfony_server.html#local-domain-names)
  *  une [intégration avec Docker](https://symfony.com/doc/current/setup/symfony_server.html#docker-integration). Il injecte les variables d'environnements tel que `DATABASE_URL`ou `REDIS_URL` dans votre application

{% include image.html width="688" url="/img/vm-docker/symfony-cli.jpg" description="Illustration du binaire Symfony CLI, sur la gauche, différentes étapes pour booter le projet, sur la droite le serveur web lancé par `symfony serve` qui traitera vos requêtes" %}


Avec Docker: 
* Base de données MySQL
* Serveur Redis

{% include image.html width="688" url="/img/vm-docker/docker-containers.jpg" description="Liste des containeurs Docker" %}

## Aller plus loin

Je n'aborde pas trop la technique dans cet article. Hugo Alliaume à repris le POC que j'ai réalisé en Août 2020, puis boudé, ainsi que la problématique à bras le corps et a mené le projet à son exécution sur tous les projets de l'équipe R&D. 

Il à traité des parties un peu plus techniques dans son article: [Blog d'Hugo](https://hugo.alliau.me/)
Je vous invite à lire son article, si vous avez besoin de plus de détail, ou même si vous voulez adopter un environement de développement similaire.

## Installation sous MacOS

Ce chapitre s'adresse aux utilisateurs sous MacOS ❤️

### Prérequis

Installation de [`brew`](https://brew.sh/index_fr) nécessaire pour installer d'autres dépendances (gestionnaire de paquets pour MacOS)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Installation de [`Zsh`](https://ohmyz.sh/#install)

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```


### Installation de PHP

```bash 
brew install php # installe la dernière version stable de PHP, soit la version 8.0.x à l'heure ou j'écris cet article.
brew install php@7.4 # installe php 7.4
brew install php@7.3 # ...
```

Si vous avez des projets sous différentes versions de PHP, pas de soucis, installez toutes les versions de PHP nécessaires à vos projets, Symfony utilisera la bonne version grâce au fichier `.php-version` déposé à la racine de votre projet.

{% include image.html width="688" url="/img/vm-docker/symfony-cli-php-version.jpg" description="Détection des différentes versions de PHP installées sur votre machine tel que vu par le binaire Symfony" %}

⚠️ Toutes les commandes PHP doivent être executés via le binaire `symfony` 

```bash
# Ne pas utiliser
php bin/console assets:install
php bin/php-cs-fixer fix ...

# Utiliser à la place
symfony console assets:install
symfony php bin/php-cs-fixer fix ....
```

### Installation de NodeJS

```bash 
brew install node # installe la dernière version stable de NodeJS, soit la version 16.0.x à l'heure ou j'écris cet article.
brew install node@14 # installe la nodeJS 14
brew install node@12 # ...

brew install yarn # si vous avez besoin du gestionnaire de dépandance JS Yarn
```

Sur le même schéma, si vous avez des projets sous différentes versions de NodeJS, installez toutes les versions nécessaires à vos projets, nous utiliserons `nvm` qui permet d'utiliser la bonne version de node selon le projet (la version étant contenue dans le fichier à la racine du projet `.nvmrc`).

```bash 
brew install nvm
```

Enfin lorsque vous lancerez votre `yarn install` ou `yarn dev-server` pour éviter de faire un `nvm use` pour sélectionner la bonne version de nodejs, vous pouvez ajouter ce bloc dans votre `~/.zshrc` (puis relancer votre terminal, ou executez `source ~/.zshrc`). Cela aura pour effet de regarder si dans votre chemin courant, un `.nvmrc` existe avec une version de node spécifique au projet, et l'utilise, si elle est installée.

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "/usr/local/opt/nvm/nvm.sh" ] && . "/usr/local/opt/nvm/nvm.sh"  # This loads nvm
[ -s "/usr/local/opt/nvm/etc/bash_completion.d/nvm" ] && . "/usr/local/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion

# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```


## Remerciements

Je tiens à remercier les différents contributeurs au projet [manala.io]([manala.io](https://www.manala.io/)) porté par mon ancienne agence web, [Elao](https://www.elao.com/) qui maintiennent l'image linux de base de la VM et les différents [rôles Ansible](https://github.com/manala/ansible-roles) pour permettre l'installation et la configuration par exemple de php, nginx... 