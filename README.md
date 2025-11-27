📋 Présentation

Ce projet vous guide pour installer un serveur Minetest dans des conteneurs LXC et configurer un tableau de bord interactif pour faciliter la gestion.

Prérequis

Avant de commencer, assurez-vous d'avoir les éléments suivants :

Des conteneurs LXC configurés (adresses IP et autres paramètres de réseau)

Un système Debian/Ubuntu installé sur chaque conteneur

Installation de Minetest
1. Installer Minetest et les dépendances

Tout d'abord, mettez à jour vos paquets et installez Minetest ainsi que les dépendances nécessaires :

apt update
apt install minetest-server iptables iptables-persistent fail2ban

2. Transférer les fichiers de configuration

Déplacez les fichiers de configuration nécessaires vers les répertoires appropriés :

minetest.conf de chaque carte vers /etc/minetest/ sur le conteneur

world.mt dans /var/games/minetest-server/.minetest/worlds/world

Le fichier sudoers dans /etc/

Le filtre et la jail minetest-auth.conf dans /etc/fail2ban

Les fichiers du Dashboard et du Web dans /var/www/

3. Définir les permissions

Attribuez les bonnes permissions aux répertoires et fichiers pour que Minetest puisse y accéder correctement :

chown -R Debian-minetest:games /etc/minetest
chown -R Debian-minetest:games /usr/share/games/minetest
chown -R Debian-minetest:games /var/games/minetest-server

4. Redémarrer le service Minetest

Pour appliquer les modifications, redémarrez le serveur Minetest :

systemctl restart minetest-server

5. Configuration du DNAT

Afin de rendre chaque carte accessible depuis l'extérieur, configurez des règles DNAT sur votre serveur principal. Exemple :

iptables -A PREROUTING -t nat -p udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000
iptables -A PREROUTING -t nat -p udp --dport 30001 -j DNAT --to-destination 10.0.3.15:30000


Note : Adaptez les ports et les adresses IP en fonction de votre configuration.

Installation du Dashboard
1. Installer Apache et PHP

Installez Apache et PHP pour le tableau de bord sur votre serveur principal :

apt update
apt install apache2 php php-cli php-common libapache2-mod-php

2. Configurer le Dashboard

Modifiez la configuration d'Apache pour pointer vers le répertoire du Dashboard :

DocumentRoot /var/www/minetest


Attribuez les bonnes permissions sur les fichiers du Dashboard :

chown -R www-data:www-data /var/www/minetest


Ensuite, redémarrez Apache pour appliquer les changements :

systemctl restart apache2


Répétez ces étapes pour le fichier index.html dans le répertoire Web si nécessaire.

3. Personnalisation du Dashboard

Modifiez les fichiers index.php et index.html selon vos préférences pour personnaliser l'apparence et la fonctionnalité du tableau de bord.

Installation des Scripts

Déplacez les scripts .sh dans le répertoire /usr/bin/ :

mv script.sh /usr/bin/


Déplacez les fichiers .service dans /etc/systemd/system/ :

mv script.service /etc/systemd/system/


Rechargez systemd pour qu'il prenne en compte les nouveaux fichiers de service :

systemctl daemon-reload


Appliquez les droits d'exécution pour l'utilisateur www-data sur les scripts :

chmod +x /usr/bin/nom_du_script

Informations importantes

Ce tableau de bord est compatible avec les configurations suivantes :

LXC (Linux Containers)

Apache2

PHP
