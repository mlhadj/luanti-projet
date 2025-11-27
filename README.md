#!/bin/bash
# ██████████████████████████████████████████████████████████████████████████████
#  🎮  MINETEST + LXC DASHBOARD  --  INSTALLATION AUTOMATISÉE
# ██████████████████████████████████████████████████████████████████████████████

################################################################################
#  1. INSTALLATION MINETEST (dans chaque conteneur LXC)
################################################################################
echo "📦 Installation de Minetest..."
apt update
apt install -y minetest-server
mv minetest.conf world.mt /etc/minetest/
chown -R Debian-minetest:games /etc/minetest
systemctl restart minetest-server
echo "✅ Minetest installé et configuré !"

################################################################################
#  2. CONFIGURATION DNAT (sur l'hôte)
################################################################################
echo "🌍 Configuration DNAT..."
# Remplace 10.0.3.10 et 30000 par tes valeurs !
iptables -A PREROUTING -t nat -p udp -m udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000
echo "✅ Règle DNAT ajoutée !"

################################################################################
#  3. INSTALLATION DASHBOARD (sur l'hôte)
################################################################################
echo "🖥️ Installation du dashboard..."
apt update
apt install -y apache2 php php-cli php-common libapache2-mod-php
mkdir -p /var/www/minetest
mv index.php /var/www/minetest/
chown -R www-data:www-data /var/www/minetest
systemctl restart apache2
echo "✅ Dashboard installé !"

################################################################################
#  4. CONFIGURATION APACHE
################################################################################
echo "📝 Configuration Apache..."
# Édite /etc/apache2/sites-available/000-default.conf et ajoute :
# DocumentRoot /var/www/minetest
systemctl restart apache2
echo "✅ Apache configuré !"

################################################################################
#  5. SCRIPTS & SERVICES
################################################################################
echo "⚙️ Installation des scripts..."
mv *.sh /usr/bin/
mv *.service /etc/systemd/system/
chmod +x /usr/bin/*.sh
systemctl daemon-reload
echo "✅ Scripts et services prêts !"

################################################################################
#  🎉 INSTALLATION TERMINÉE !
################################################################################
echo "🎉 Tout est prêt ! Accède à ton dashboard via http://$(hostname -I | awk '{print $1}')"
