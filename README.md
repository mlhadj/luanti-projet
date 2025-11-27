# =============================================
# MINETEST + LXC DASHBOARD - INSTALLATION COMPLÈTE
# =============================================

# ----------------------------
# 1. INSTALLATION MINETEST (dans chaque conteneur LXC)
# ----------------------------
apt update
apt install minetest-server
mv minetest.conf world.mt /etc/minetest/
chown -R Debian-minetest:games /etc/minetest
systemctl restart minetest-server

# ----------------------------
# 2. CONFIGURATION DNAT (sur l'hôte)
# ----------------------------
# Remplace 10.0.3.10 et 30000 par tes valeurs
iptables -A PREROUTING -t nat -p udp -m udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000

# ----------------------------
# 3. INSTALLATION DASHBOARD (sur l'hôte)
# ----------------------------
apt update
apt install apache2 php php-cli php-common libapache2-mod-php
mkdir -p /var/www/minetest
mv index.php /var/www/minetest/
chown -R www-data:www-data /var/www/minetest
systemctl restart apache2

# ----------------------------
# 4. CONFIGURATION APACHE
# ----------------------------
# Édite /etc/apache2/sites-available/000-default.conf et ajoute :
# DocumentRoot /var/www/minetest
systemctl restart apache2

# ----------------------------
# 5. SCRIPTS & SERVICES
# ----------------------------
mv *.sh /usr/bin/
mv *.service /etc/systemd/system/
chmod +x /usr/bin/*.sh
systemctl daemon-reload
