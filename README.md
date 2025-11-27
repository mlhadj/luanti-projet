🚀 Minetest + LXC Dashboard – Quick Setup
📦 Installation de Minetest (dans chaque conteneur LXC)
Prérequis
✅ Conteneurs LXC configurés (IP, réseau)
✅ Debian/Ubuntu installé
Commandes

# Mise à jour & installation
apt update
apt install minetest-server

# Configuration
mv minetest.conf world.mt /etc/minetest/
chown -R Debian-minetest:games /etc/minetest

# Redémarrage
systemctl restart minetest-server


🌍 Configuration DNAT (sur l’hôte)

iptables -A PREROUTING -t nat -p udp -m udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000

Remplace 10.0.3.10 et 30000 par tes valeurs.

🖥️ Installation du Dashboard (sur l’hôte)
1. Apache + PHP

apt update
apt install apache2 php php-cli php-common libapache2-mod-php

2. Configuration

mkdir -p /var/www/minetest
mv index.php /var/www/minetest/
chown -R www-data:www-data /var/www/minetest
systemctl restart apache2

3. Apache : DocumentRoot
Édite /etc/apache2/sites-available/000-default.conf :

DocumentRoot /var/www/minetest

Puis :

systemctl restart apache2


⚙️ Scripts & Services

mv *.sh /usr/bin/
mv *.service /etc/systemd/system/
chmod +x /usr/bin/*.sh
systemctl daemon-reload
