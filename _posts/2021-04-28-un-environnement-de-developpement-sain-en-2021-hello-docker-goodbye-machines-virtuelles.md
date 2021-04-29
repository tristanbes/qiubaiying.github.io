---
layout: post
title: Un environnement de développement sain en 2021. Bonjour Docker, bye machines virtuelles
subtitle: 
categories:
- blog
catalog: true
date:       2021-04-28
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1534447677768-be436bb09401?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3000&q=80
tags:
    - VM
    - Docker
    - Machine Virtuelles
    - PHP
    - environnement de développement
---
## Contexte

Depuis mai 2015, le modèle chez Yproximité de "tout installer à la main en local en 1/2 journée" a été abandonné pour une solution basée sur une machine virtuelle, configurée à l'aide de playbook [Ansible](https://www.ansible.com/) afin qu'en une commande, l'environnement de développement soit opérationnel après quelques minutes.

Les prérequis pour faire tourner les **projets Symfony** consistait à installer :

* Make, afin de lancer les commandes
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads), pour la VM
- [Vagrant](https://www.vagrantup.com/downloads.html), afin de piloter la VM
- [Vagrant Landrush](https://github.com/vagrant-landrush/landrush), un DNS pour accéder aux URL de développement
- [mkcert](https://github.com/FiloSottile/mkcert), pour générer le certificat TLS local

L'utilisateur était alors invité alors à exécuter dans son terminal `make setup`, puis une vingtaine de minutes plus tard, le projet était complètement opérationnel, les données de la base de données importés et toute l'équipe sur les mêmes versions mineures de PHP, Nginx, PostgreSQL.

❤️ Je tiens d'ailleurs à remercier les contributeurs du projet [manala.io](https://www.manala.io/) porté par mon ancienne agence web, [Elao](https://www.elao.com/) et notamment [Nervo](https://github.com/nervo) qui maintient les différents [rôles Ansible](https://github.com/manala/ansible-roles). Merci pour leurs contributions open source et d'avoir permis de faire tourner notre usine de développement pendant 6 ans.

Nous sommes maintenant en 2021. Qu'est-ce qui m'a poussé à prévoir ce changement d'usine de développement et à abandonner ce modèle de machines virtuelles (VM) ?

## Pourquoi s'éloigner du modèle machines virtuelles ?

Plusieurs arguments étaient pour l'abandon des VM : 
* Docker qui devient de plus en plus mature et qui a un bon taux d'adoption dans la communauté web sur les environnements de développement.
* Les performances un peu en retrait (surtout sur Linux si j'en crois mon équipe) par rapport à une installation locale du projet.
* Le poids des machines virtuelles (compter plusieurs Go par projets)

Néanmoins, l'idée d'adopter une usine de développement uniquement basée sur Docker m'a énormément freiné. Avec une équipe réduite et sans compétence Docker particulière (notamment pour les problématiques de performances macOS vs Linux) ce choix s'avère risqué pour la stabilité de l'environnement de développement, qui doit être là pour permettre aux développeurs d'être le plus productif, et non pas comme étant une source de frustration, ou de ralentissement.

Je savais que les VM allaient bientôt être poussées vers la sortie, mais était-ce suffisant pour arrêter une date butoire de transition vers autre chose ? Prévoir un plan de formation ? Faire évoluer nos dizaines de projets vers un autre environnement de développement basé sur du Docker ? Pas tout à fait... 

L'élément qui à permis de faire basculer la balance vers "autre chose" est [l'annonce discrète par l'équipe de VirtualBox](https://forums.virtualbox.org/viewtopic.php?f=8&t=98742#wrap) qui dit ne pas pouvoir techniquement porter VirtualBox sur un processeur ARM. VirtualBox, qui fait tourner les VM, est un hyperviseur, et non un émulateur CPU. VirtualBox ne peut donc pas émuler un processeur x86.

En quoi c'est gênant ? Et bien peut-être avez vous raté [l'annonce d'Apple](https://nr.apple.com/dE7O5p9q0t) de leur plan de transition vers une architecture ARM sous 2 ans environ en débutant par leur puce "M1" (qui envoie du lourd), équipant les MacBook Pro 13", le MacBook Air et le Mac Mini. Il semblerait même que devant les retours extrêmement positifs des utilisateurs, la firme ait même acceléré le déploiement de la nouvelle architecture ARM en annoncant il y à quelques jours les iMac 24" et les iPad Pro avec la puce M1. Pour le grand public, il ne reste donc plus que le MacBook Pro 16" et l'iMac 27" à sauter le cap.

{% include image.html width="688" url="/img/vm-docker/apple-m1.jpg" description="Illustration du processeur d'Apple sous architecture ARM baptisé M1" %}


Je vois venir les trolls d'Apple au loin, mais dans mon équipe, j'ai toujours eu à cœur de proposer aux développeurs/euses le choix entre du Linux et du macOS ([voir baromètre AFUP pour les répartions d'OS](https://barometre.afup.org/report/os_developpment?filter%5Bcampaign%5D%5B%5D=6&filter%5Bcampaign%5D%5B%5D=7&filter%5Bcampaign%5D%5B%5D=8&filter%5Bsalary%5D%5Bmin%5D=&filter%5Bsalary%5D%5Bmax%5D=&filter%5Bsubmit%5D=)). Donc merci de rediriger les troll vers les seuls qui le méritent : les devs PHP/JS sous Windows.

## Une solution propulsée par Docker, mais pas que !

Dans mon contexte d'entreprise et d'équipe, une solution basée uniquement sur Docker est compromise tant que le problème de performance soit définitivement reglé sur Mac ou qu'il y ait besoin d'une ressource experte Docker/Linux pour implémenter la solution.

J'ai donc choisi de pousser l'adoption d'une solution hybride afin d'éviter les volumes partagés (et donc pas de problème de performances). Il semblerait, d'après ma mémoire de SymfonyLive que ça soit aussi le choix de [Fabien Potencier](https://twitter.com/fabpot).

Sur la machine hôte (macOS/Linux) :
* PHP et composer
* NodeJS
* Docker Desktop
* Symfony CLI. Ce dernier offre un serveur web avec gestion du TLS, le support docker, et la gestion des versions de PHP spécifiques.
* Binaire Manala (Permettra de générer votre `docker-compose.yaml` d'après un template. Voir [aller plus loin](#aller-plus-loin))

⚠ Malgré tous les avantages qu'offre Symfony CLI, ce dernier n'est pas open source. C'est à noter, surtout si on se repose dessus pour une usine de développement. 

Avec Docker : 
* Base de données (MySQL, PostgreSQL, MariaDB...)
* Serveur Redis

{% include image.html width="688" url="/img/vm-docker/docker-containers.jpg" description="Liste des containeurs Docker" %}

L'expérience de développement avec cette nouvelle stack hybride est vraiment très plaisante et rapide, je retrouve les performances natives de ma machine.

## Aller plus loin

[Hugo Alliaume](https://twitter.com/HugoAlliaume) a repris le POC que j'ai réalisé en Août 2020 et levé tous les points de bloquages. Il s'est saisi de la problématique à bras le corps et a mené le projet de transition à son exécution sur tous les projets de l'équipe R&D.

Il a abordé des parties plus techniques dans sur son blog, notamment :
* Une analyse plus poussée des limites des VM et des workaround nécessaires
* Pourquoi remplacer son serveur web / reverse proxy et gestion DNS local par Symfony CLI 
* Un exemple de `docker-compose.yaml`
* Comment tirer profit de cette stack de développement pour le CI, avec GitHub Actions ?
* Comment aller plus loin avec le binaire `manala` pour faciliter la gestion et la maintenance (ex: `Makefile`)

📖&nbsp; Je vous invite à lire son article : [migration de notre stack de développement vers Docker](https://hugo.alliau.me/2021/04/26/migration-stack-developpement/).

## Installation des prérequis sous macOS

Ce chapitre s'adresse aux utilisateurs sous macOS 🍎 qui souhaiteraient retrouver ici toutes les procédures d'installation des outils sur votre machine. 

### Prérequis

Installation de [`brew`](https://brew.sh/index_fr) le gestionnaire de paquets pour macOS.

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

brew install composer
```

Si vous avez des projets sous différentes versions de PHP, pas de soucis, installez toutes les versions de PHP nécessaires à vos projets, Symfony utilisera la bonne version grâce au fichier `.php-version` déposé à la racine de votre projet.

{% include image.html width="688" url="/img/vm-docker/symfony-cli-php-version.jpg" description="Détection des différentes versions de PHP installées sur votre machine tel que vu par le binaire Symfony" %}

### Installation de NodeJS

```bash 
brew install node # installe la dernière version stable de NodeJS, soit la version 16.0.x à l'heure ou j'écris cet article.
brew install node@14 # installe la nodeJS 14

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

### Bonus avec mux et tmuxinator

Si vous travaillez avec un projet Symfony contenant du JS ayant besoin d'être compilé, vous allez surement avoir besoin de 3 onglet de terminal:
* Une session sur `symfony serve` le webserveur
* Une session à la racine de votre projet
* Une session sur `yarn dev-server` pour compiler votre Javascript quand il est modifié.

Pour gagner en confort, je vous suggère d'utiliser [iTerm2](https://iterm2.com/) avec le multiplexer de terminal `tmux`. Il permettra de scinder vos terminaux en terminaux virtuels et de pouvoir les splitter. Concrètement, quand je lance dans mon terminal `mux start <nom_du_projet>` j'ai automatiquement mes containers qui se lancent, mon split de terminal qui se fait, et `yarn dev-server` dans un onglet virtuel comme ci-dessous.

{% include image.html width="688" url="/img/vm-docker/symfony-cli.jpg" description="Illustration du binaire Symfony CLI, sur la gauche, différentes étapes pour booter le projet, sur la droite le serveur web lancé par `symfony serve` qui traitera vos requêtes" %}


Pour profiter de la même chose: 
```bash
brew install tmux
brew install tmuxinator
```

puis rajouter l'alias dans votre `~/.zshrc`
```
alias mux=tmuxinator
```

puis la configuration d'un projet est assez basique
```
mux open <nom_du_projet>
```

et configurer votre projet à l'aide de la documentation de [tmuxinator](https://github.com/tmuxinator/tmuxinator), comme par exemple : 

```yml
# /Users/t.bessoussa/.config/tmuxinator/<nom_du_projet>.yml

name: <nom_du_projet>
root: ~/workspace/<mon_projet> # Chemin de votre projet

# Run on project exit ( detaching from tmux session )
on_project_exit: make halt

# Run on project stop
on_project_stop: make halt

windows:
  - php:
      layout: main-vertical
      panes:
        - make setup
        - symfony serve
  - js:
    - sleep 70 # on attends un peu vu que mon make setup va lancer un build js de dev qui rentrerait en conflit avec le dev-server
    - yarn dev-server
```

Il ne vous reste plus qu'à retenir `mux start <nom_du_projet>` et `mux stop <nom_du_projet>` et le tour est joué.

Une typo ? L'article est disponible sur mon [dépôt Github](https://github.com/tristanbes/devops-life/blob/gh-pages/_posts/2021-04-28-un-environnement-de-developpement-sain-en-2021-hello-docker-goodbye-machines-virtuelles.md)