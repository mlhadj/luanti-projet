# Dashboard LXC / Minetest sur Debian

## 📋 Présentation
Ce projet explique comment installer un serveur Minetest dans des conteneurs LXC, ainsi que la mise en place d'un tableau de bord interactif pour les gérer facilement.

## Prérequis

- Conteneurs LXC configurés (avec adresses IP, etc.)
- Debian/Ubuntu installé sur chaque conteneur

## Installation de Minetest

### 1. Installer Minetest et les dépendances
Mettre à jour les paquets et installer Minetest ainsi que les outils nécessaires :

```bash
apt update
apt install minetest-server iptables iptables-persistent fail2ban

2. Transfert des fichiers de configuration

Déplacez les fichiers nécessaires vers les répertoires appropriés :

    minetest.conf de chaque carte dans /etc/minetest/ du conteneur

    world.mt dans /var/games/minetest-server/.minetest/worlds/world

    Fichier sudoers dans /etc/

    Jail et filtre minetest-auth.conf dans /etc/fail2ban

    Fichiers du Dashboard et Web dans /var/www/

3. Définir les permissions

Attribuez les bonnes permissions sur les répertoires et fichiers :

chown -R Debian-minetest:games /etc/minetest
chown -R Debian-minetest:games /usr/share/games/minetest
chown -R Debian-minetest:games /var/games/minetest-server

4. Redémarrer le service Minetest

systemctl restart minetest-server

5. Configuration du DNAT

Pour rendre chaque carte accessible depuis l'extérieur, configurez des règles DNAT sur votre serveur principal :

iptables -A PREROUTING -t nat -p udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000
iptables -A PREROUTING -t nat -p udp --dport 30001 -j DNAT --to-destination 10.0.3.15:30000

    Note : Adaptez les ports et adresses IP selon votre configuration.

Installation du Dashboard
1. Installer Apache et PHP

Installez les dépendances nécessaires sur votre serveur principal :

apt update
apt install apache2 php php-cli php-common libapache2-mod-php

2. Configurer le Dashboard

Modifiez le fichier de configuration d'Apache (/etc/apache2/sites-available/000-default.conf) :

DocumentRoot /var/www/minetest

Attribuez les permissions nécessaires sur les répertoires :

chown -R www-data:www-data /var/www/minetest

Redémarrez Apache :

systemctl restart apache2

Répétez cette étape pour le fichier index.html dans le dossier Web.
3. Personnalisation

Modifiez les fichiers index.php et index.html selon vos préférences pour personnaliser le tableau de bord.
Installation des Scripts

    Déplacez les scripts .sh dans /usr/bin/

    Déplacez les fichiers .service dans /etc/systemd/system/

    Rechargez systemd :

systemctl daemon-reload

    Appliquez les droits d'exécution pour l'utilisateur www-data.
