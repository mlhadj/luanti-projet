Dashboard LXC / Minetest Debian
📋 Présentation

Ce dépôt explique comment installer des serveurs Minetest sur des conteneurs LXC ainsi qu'un dashboard interactif pour les gérer.
Installation de Minetest
Prérequis

    Conteneurs LXC configurés (adresses IP, etc.)
    Debian/Ubuntu sur les conteneurs

Installation

    Installez le serveur Minetest via APT :

   apt update
   apt install minetest-server

    Déplacez les fichiers de configuration de chaque map dans /etc/minetest/ :
        minetest.conf
        world.mt

    Définissez les permissions appropriées :

   chown -R Debian-minetest:games /etc/minetest

    Redémarrez le service :

   systemctl restart minetest-server

Configuration du DNAT

Pour rendre chaque map accessible depuis l'extérieur, configurez des règles DNAT sur votre serveur principal vers chaque conteneur :

iptables -A PREROUTING -t nat -p udp -m udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000

    Note : Adaptez le port et l'adresse IP selon votre configuration.

Installation du Dashboard
1. Installation d'Apache et PHP

Sur votre serveur principal, installez les dépendances nécessaires :

apt update
apt install apache2 php php-cli php-common libapache2-mod-php

2. Configuration du Dashboard

    Créez le dossier pour le dashboard :

   mkdir -p /var/www/minetest

    Déplacez le fichier index.php dans /var/www/minetest

    Modifiez la configuration Apache dans /etc/apache2/sites-available/000-default.conf :

   DocumentRoot /var/www/minetest

    Définissez les permissions :

   chown -R www-data:www-data /var/www/minetest

    Redémarrez Apache :

   systemctl restart apache2

3. Personnalisation

Modifiez le fichier index.php selon vos besoins.
Aperçu
Dashboard - Vue principale Dashboard - Vue détaillée
Installation des Scripts

    Déplacez les scripts .sh dans /usr/bin/
    Déplacez les fichiers .service dans /etc/systemd/system/
    Rechargez systemd :

   systemctl daemon-reload

    Appliquez les droits d'exécution pour l'utilisateur www-data

Informations importantes

Ce dashboard est pleinement compatible avec les distributions disposant de :

    LXC (Linux Containers)
    Apache2
    PHP

