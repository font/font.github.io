<!--
.. title: Pac-Man NGINX PHP MongoDB Application
.. slug: pacman-nginx-app
.. date: 2017-02-06 16:35:14 UTC-08:00
.. tags: kubernetes,nginx,mongodb,pacman,game,gce,google compute engine
.. category: kubernetes
.. link:
.. description:
.. type: text
-->

## Intro

I've started working on a Kubernetes reference application for demonstration purposes. This reference application will continue to evolve
to also work with cloud federation capabilities in Kubernetes. I will provide updates as the application evolves.

## Pac-Man Game Architecture Brief Overview

### Pac-Man

The Pac-Man game is a slightly modified version of the open source Pac-Man game written in HTML5 with Javascript. You can get the modified
[Pac-Man game source code here](https://github.com/font/pacman-canvas). The modifications are particularly around the backend PHP API. The original
game used SQLite3 as the storage backend for reading and writing high scores. This change replaces SQLite3 with [MongoDB](https://www.mongodb.com/)
as the storage mechanism.

### NGINX + PHP FPM

[NGINX](https://www.nginx.com/) is used as the web server to host the Pac-Man game application. It is configured with [PHP FPM](https://php-fpm.org/) support for the backend PHP API.

### PHP

PHP FPM is used for the PHP API to receive read and write requests from clients and perform database operations. The
[PHP MongoDB driver extension](http://php.net/manual/en/set.mongodb.php) contains the minimal API for core driver functionality. In addition, the
[PHP library for MongoDB](http://php.net/manual/en/mongodb.tutorial.library.php) provides the higher level APIs. Both are needed.

### MongoDB

[MongoDB](https://www.mongodb.com/) is used as the backend database to store the Pac-Man game's high score user data.

## Conclusion

For a detailed overview of the Kubernetes reference application and how to set it up yourself, see the
[README](https://github.com/font/k8s-example-apps/tree/master/pacman-nginx-app) in the repository.

### Have fun!

<img src="/images/pacman.png">
