# TP Scripting BASH

## 0. Introduction

Lire le petit [cours sur le scripting](bash_scripting.md).

## 1. Menu pour utilisateur lambda

Il s'agit de créer un script permettant à un utilisateur non familiarisé d'utiliser des commandes shell. Un tel script va permettre à l'utilisateur d'exécuter des commandes sans vraiment les connaître. L'utilisateur se voit proposé un menu.

Le script devra proposer les options suivantes :

1. Afficher les utilisateurs qui sont connectés
2. Afficher les informations système (`uname -a`)
3. Afficher le fichier le plus volumineux dans le répertoire courant
4. Afficher les infos CPU (`lscpu`, mais seulement les premières lignes significatives)
5. Afficher l'heure système
6. Sortir du programme

Vous utiliserez une boucle pour que le menu soit de nouveau présenté à l'utilisateur après chaque commande.

Une première entrée incorrecte doit être signalée : « Merci d'entrer un chiffre entre 1 et 6 ». Les entrées incorrectes suivantes devront être sanctionnées d'un « Entre 1 et 6, grosse quiche ».

Le script se termine quand l'option 6 est choisie.

## 2. Rapport automatique

Les scripts sont souvent utilisés pour effectuer des vérification automatiques et/ou des rapports sur le système.

Créez un script qui va rechercher dans tout le système les fichiers suivants :

- fichiers SUID
- fichiers SGID (pas les répertoires)
- fichiers et répertoires qui ont les droits d'écriture pour `others` (mais pas les liens symboliques)

Ce script va écrire son rapport dans le répertoire `/var/reports`. Le rapport va inclure toutes les informations citées et le nom du fichier devra être **rapport-fichiers-_date_**, où _date_ sera remplacé par la date courante (exemple `rapport-fichiers-10-02-2021`).

Testez ce script en tant que root.

Faites en sorte que le script s'exécute sur votre système toutes les nuits à 4 h 30 (renseignez-vous sur la `crontab` et le répertoire `/etc/cron.d`).

QUESTION : pourquoi voudrait-on effectuer de tels rapports ? Pourquoi serait-il même souhaitable que les rapports soient automatiquement comparés avec le précédent ?

## 3. Sauvegarde des répertoires utilisateurs

Les utilisateurs de l'organisation se plaignent que, lorsqu'ils ont besoin d'accéder à une sauvegarde de leurs fichiers, cela prend trop de temps au support IT pour intervenir et restaurer les fichiers voulus. Ils souhaiteraient avoir accès eux-mêmes à au moins une sauvegarde de leurs propres données. Il existe plusieurs solutions à ce problème, l'une d'entre elles étant les _snapshots de stockage_, sur lesquels vous pouvez vous renseigner.

Ici, on va écrire un script `sauvegardeusers` qui va permettre de sauvegarder tous les fichiers de chaque répertoire utilisateur (à l'exception de root) au format tar. On supposera la structure classique dans laquelle tous les utilisateurs ont leur répertoire personnel situé dans `/home`. Les sauvegardes seront enregistrées dans un répertoire nommé `/var/sauvegardes/nomuser`, où `nomuser` sera remplacé par le nom effectif de l'utilisateur (ces répertoires devront être créés s'ils n'existent pas déjà). Chaque utilisateur devra avoir un accès complet à son propre répertoire de sauvegarde (il devra en être propriétaire, et avec permissions `rwx------`). Chaque fichier de sauvegarde devra être nommé `nomuser_AAAAMMJJ.tar`, où `nomuser` sera remplacé par le nom effectif de l'utilisateur et `AAAAMMJJ` représente la date de la sauvegarde. Le fichier tar devra appartenir à l'utilisateur correspondant (avec les permissions `rw-------`).

Exemple pour l'utilisateur francis :

- répertoire personnel : `/home/francis`
- répertoire de sauvegarde (propriétaire : francis, permissions : `rwx------`) : `/var/sauvegardes/francis`
- fichier de sauvegarde (propriétaire : francis, permissions : `rw-------`) : `/var/sauvegardes/francis/20210630.tar` pour une sauvegarde datant du 30 juin 2021, contenant l'ensemble des fichiers (y compris sous-répertoires) du répertoire `/home/francis` à cette date

> Usage du script : `sauvegardeusers [OPTION]`
>
> Sauvegarde toutes les données de chaque répertoire utilisateur.
>
> Option :
>
> `-d` si spécifiée, supprimera tous les fichiers tar éventuellement présents dans chaque répertoire avant la nouvelle sauvegarde

Modifiez ensuite la crontab pour lancer cette commande (sans option) toutes les semaines.
