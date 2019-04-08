---
layout: post
title: How not to be depressed when working with WordPress
subtitle: A 2019 sane vision for a cleaner WordPress architecture
categories:
- blog
catalog: true
date:       2019-04-08
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1519110437047-c6488cf2051d?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1567&q=80
tags:
    - WordPress
    - Architecture
    - composer
    - best practice
---

Ok, so you chose to start a new WordPress project (or someone chose for you) ? Well good news, it’s 2019, it doesn’t have to suck from the start anymore; Here’s what I advise you to follow.

In this article, I'm not going to explain everything in details, that would require me to write a book about it. I will just point out directions: What you can do and how to do it.

In the company I work for, we have 150+ WordPress sites. I made the decision for the new ones to adopt everything I wrote in this article, and slowly migrate the legacy one. Already 15% of them are using this architecture at the time I was writing this article.


---

# Use Bedrock Edition

It's **THE** starting point of this article. Bedrock is a WordPress boilerplate where you can install WordPress as a package via Composer.

The boilerplate, created by [Roots](https://roots.io/), leverages the use of modern development tools and embrace the [12 Factor app methodology](https://12factor.net/).

The main advantages of this boilerplate are:

## Support of environment variables
If you worked with modern software applications, you're familiar with the concept of having all of your application configuration stored as environment variables.

It allows you to ease having multiple environments such as `production`, `staging` and `development` where you can for example only enable sending emails on `production`, without the need of constantly changing values depending on which environment you are.

In 2019, every major frameworks has a concept of environnement variables in the PHP world: Symfony, Laravel, Yii. It's a common practice that exists since decades on Windows and Unix world.

Environement variables can be stored as a file named `.env.example` which you have to commit on your git repository. They look a lot like this:

```
WP_ENV=development
WP_HOME=http://my-awesome-wordpress.vm
WP_SITEURL=${WP_HOME}/wp
```

## Reproductible builds
The concept of [reproductible builds](https://en.wikipedia.org/wiki/Reproducible_builds) can sum up as: no matter when or where i'm going to build and deploy the application, I will always have the same version of the code (and plugins)

Since you're going to be working on you local environment (if it's not already the case 👀), you need to guarantee that what you have on your computer will be exactly the same that what you'll have on your production. Let me take an example on why this is important.

Let's say you've been running a website in production for like a year until there's a nasty hardware crash on you server. Now all your files are lost. Luckily for you you did things right and had your `uploads/` directory and database backup somewhere in the cloud.

The next step will be to re-install WordPress using the database and media backup. Then re-install the 20 plugins you had on that website. In one year, plugins evolve (and it's good), but your theme, or some of your features might not be compliant with the version of 3.5 of WooCommerce. It might result of a broken website

Another example is when you're working as a team on a project, without reproductable builds you will end up with the frontend person working with WordPress 5.1.1 while the developper still has version 4.9.10 and won't be able to reproduce the bug you have with that version.

But how to guarantee that ? Well the answer is just below: use **Composer**.

# Use Composer

Bedrock boilerplate allows you to manage dependencies of your project using [Composer](https://getcomposer.org/) like **any** modern and decent PHP project should do (WordPress, I'm looking at you right now).

Do you want to install `WooCommerce` ? Go inside your terminal and type:

`composer require woocommerce/woocommerce`

It'll fetch the latest version and freeze the version you got on your `composer.lock`. This file is what guarante you to get always the same version of everything no matter where you download your project dependencies, or where you do it. Because of the importance of this file, it has to be commited ⚠️.

All the plugins are also installable via composer; How ? **3 scenarios are possible**:

- The plugin provides a `composer.json`; The developper or company behind the plugin is not locked in a WordPress thing of the past syndrome and has developed modern applications. This would mean that their plugin also ships a [composer.json][https://github.com/woocommerce/woocommerce/blob/master/composer.json] file that allow installation through composer. With this scenario you can search for your plugin inside [packagist.org](https://packagist.org/?query=woocommerce) which is a repository containing all the public PHP packages in the universe ✨.

NOTE FOR PLUGINS AUTHOR !

- The plugin does not provide a `composer.json`; If you don't see the plugin on [packagist.org](https://packagist.org/), then the plugin does not have a `composer.json` file.
Don't worry, **wpackagist** comes to the rescue. Outlandish created this project to offer a way to install 100% of the plugins and themes using the main WordPress repository. So, this particular WordPress plugin cannot be found on packagist.org, so let's use [wpackagist](https://wpackagist.org/) instead:

`composer require wpackagist-plugin/cookie-law-info`

- The plugin is a paid/private one and does not have a `composer.json`. You'll have multiple options from there:
   - [Open a ticket to their support]((https://github.com/elementor/elementor/issues/4042)) or on their Github and ask to provide a way to install their plugin via composer. You are their customer, you are paying for a product that you can't install in a proper way. They should care. Bad players are Advanced Custom Field, Elementor and good player is [Deliciours brains](https://deliciousbrains.com/wp-migrate-db-pro/doc/installing-via-composer/) (Migrate DB Pro, Offload Media...).
   - [Use alternative solutions](https://github.com/PhilippBaschke/acf-pro-installer) when available.
   - Create a new private Github repository (it's free), copy/paste the code there and add a [composer.json like in this example](https://gist.github.com/tristanbes/fbacfb2ce6990e7fdc7411a73715fd92), tag a new release using [Github releases](https://help.github.com/en/articles/creating-releases), and then require this package using a custom repository. All the procedure is [listed in detail on roots.io website](https://roots.io/guides/private-or-commercial-wordpress-plugins-as-composer-dependencies/). I'd advise, if you have the budget for, to use [Private Packagist](https://packagist.com/) to host your private packages.


# Prefer storing your assets on the cloud

Storing your assets on the local filesystem under `uploads/` directory is nice, but at some point, you'll have to synchronize this folder through all your environements, otherwise your local version will a complete different set of medias than the production and vice-versa.

Remember when we talked about reproducable builds ? Wouldn't it be nice if we can store our media remotly so we don't use local filesystem ? Spoiler alert, yes, it's nice.

The one other major advantage of this technique is **allow your website to scale** to recieve a huge pike of traffic load. All you have to do is to add more servers, since you have a reproductible build, the project and plugins will be the same on server 1, 2, 3 ... 23, 24.

Here's a list of free plugins that allow you to store your media on different cloud storage (amazon S3, scaleway, minio, digital ocean space...)

My wish for WordPress is for them to use an filesystem abstraction such as [Flysystem](https://flysystem.thephpleague.com/docs/) so this way, we no longer have to rely on plugins to hack into WordPress to tell it to upload medias and assets elsewhere. Until this happens, you can use:

- [MediaCloud](https://wordpress.org/plugins/ilab-media-tools/) (free)
- [humanmade/s3-uploads](https://github.com/humanmade/S3-Uploads) (free)

# Lint your code

Your theme ship css, javascript and some PHP ? You should lint them

For your own sanity, if you are a developper, please don’t open code you’re downloading from the plugins you use. You might be shocked by the poor code quality you’re dealing with and might end up re-writing a whole lot of WordPress plugins.


# Use a continuous integration (CI) service

Use a CI (Travis, Gitlab, CircleCI)
Here’s our base [`.travis.yml` file]() that ensure our `composer.json` is valid, that our PHP Code is linted and same for our JavaScript file;


```
dist: trusty

language: php
php:
  - '7.3'

cache:
  yarn: true
  directories:
    - ./vendor
    - ./node_modules
    - ~/.composer/cache/files

before_install:
  - |
    if [ -f "package.json" ]; then
        set -e # properly fails Travis if one of the following commands fails
        nvm install v10
        nvm use 10
        node --version
        npm --version
        npm install --global yarn
        set +e # re-enable previous behaviour
    fi

script:
  - composer validate
  - composer install
  - vendor/bin/php-cs-fixer fix -v --dry-run --using-cache=no
  - if [ -f "package.json" ]; then yarn install; fi;
  - if [ -f "package.json" ]; then yarn lint; fi;
  - if [ -f "package.json" ]; then yarn build; fi;
  ```

# Use Timber

What about the templating system ?

PHP is the worst templating system ever. Hard to read, hard to write, and let’s not fool ourselves, we’re no longer in the early 2010, we have tools created especially for each use case. The best PHP templating system is Twig. It is widely use in the Symfony ecosystem (but not only).

So how can we use Twig inside a WordPress project ? Use Timber https://timber.github.io

It adds a View / Controller model that will have a huge benefit on the lisibility and the maintenance of your theme.

Once you start writing your themes using Twig, you won’t go back to plain PHP. Period.
Pro tip, don’t forget to add those 3 lines of code to enable caching for templates. This will avoid recompiling twig files into PHP on each requests and save you precious amount of time.

