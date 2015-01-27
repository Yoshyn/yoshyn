---
layout: post
title:  "Ruby On rails, gems intéressantes (fr)"
date:   2014-06-10 12:07:56
categories: article
tags: rails
---

Il n'est pas toujours possible d'utiliser les outils que l'on souhaite sur une application en production. Surtout quand cette application est ancienne et traine d'anciennes technos (ou versions).

C'est mon cas dans mon travail actuellement.

Voici donc un ensemble de gems intéressantes glanées ici ou la qui valent le coup d'etre testées plus en profondeur :

### Des gems à integrer à un projet :

  * [http://ankane.github.io/searchkick/](http://ankane.github.io/searchkick/)
    * De la recherche intelligente avec elastic search. Permet notamment de l'auto-complétion et des suggestions..

  * [http://ankane.github.io/searchjoy/](http://ankane.github.io/searchjoy/)
    * Analyse sur les recherches effectuées sur un site web.

  * [http://ankane.github.io/ahoy/](http://ankane.github.io/ahoy/)
    * Tracking simple pour rails (ne remplace pas Google analytic mais simple pour les devs):

  * [http://chartkick.com/](http://chartkick.com/)
    * Des graphiques Javascript en quelques lignes. Utile avec ahoy par exemple.

  * [https://github.com/ankane/groupdate](https://github.com/ankane/groupdate)
    * Grouper des informations par date. Utile avec chartkick.

  * [https://github.com/ankane/barkick](https://github.com/ankane/barkick)
    * Gestion de multiples barcodes et de leurs équivalences.

  * [https://github.com/miks/audited](https://github.com/miks/audited)
    * Permet de logguer les changements sur un modele rails

  * [https://github.com/collectiveidea/awesome_nested_set](https://github.com/collectiveidea/awesome_nested_set)
    * Gestion de nested set avec active record (utile pour des catégories par exemple).

  * [https://github.com/rubychan/coderay](https://github.com/rubychan/coderay)
    * Syntax highlighting pour ruby facilement


### Quelques outils pour le dev :

  * [https://github.com/preston/railroady](https://github.com/preston/railroady)
    * Géneration de diagrammes UML sur les modeles et les controlleurs d'une application RoR existante

  * [https://github.com/flyerhzm/bullet](https://github.com/flyerhzm/bullet)
    * Outil permettant de tracker les requêtes améliorables sur une BD.

  * [https://github.com/MiniProfiler/rack-mini-profiler](https://github.com/MiniProfiler/rack-mini-profiler)
    * Un profileur sur le temps de chargement des pages
