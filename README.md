<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard LXC / Minetest sur Debian</title>
</head>
<body>

<h1>Dashboard LXC / Minetest sur Debian</h1>

<h2>📋 Présentation</h2>
<p>Ce projet vous guide pour installer un serveur <strong>Minetest</strong> dans des conteneurs <strong>LXC</strong> et configurer un <strong>tableau de bord interactif</strong> pour faciliter la gestion.</p>

<h2>Prérequis</h2>
<p>Avant de commencer, assurez-vous d'avoir les éléments suivants :</p>
<ul>
    <li>Des conteneurs LXC configurés (adresses IP et autres paramètres de réseau)</li>
    <li>Un système Debian/Ubuntu installé sur chaque conteneur</li>
</ul>

<h2>Installation de Minetest</h2>

<h3>1. Installer Minetest et les dépendances</h3>
<p>Tout d'abord, mettez à jour vos paquets et installez Minetest ainsi que les dépendances nécessaires :</p>
<pre><code>apt update
apt install minetest-server iptables iptables-persistent fail2ban</code></pre>

<h3>2. Transférer les fichiers de configuration</h3>
<p>Déplacez les fichiers de configuration nécessaires vers les répertoires appropriés :</p>
<ul>
    <li><code>minetest.conf</code> de chaque carte vers <code>/etc/minetest/</code> sur le conteneur</li>
    <li><code>world.mt</code> dans <code>/var/games/minetest-server/.minetest/worlds/world</code></li>
    <li>Le fichier <code>sudoers</code> dans <code>/etc/</code></li>
    <li>Le filtre et la jail <code>minetest-auth.conf</code> dans <code>/etc/fail2ban</code></li>
    <li>Les fichiers du Dashboard et du Web dans <code>/var/www/</code></li>
</ul>

<h3>3. Définir les permissions</h3>
<p>Attribuez les bonnes permissions aux répertoires et fichiers pour que Minetest puisse y accéder correctement :</p>
<pre><code>chown -R Debian-minetest:games /etc/minetest
chown -R Debian-minetest:games /usr/share/games/minetest
chown -R Debian-minetest:games /var/games/minetest-server</code></pre>

<h3>4. Redémarrer le service Minetest</h3>
<p>Pour appliquer les modifications, redémarrez le serveur Minetest :</p>
<pre><code>systemctl restart minetest-server</code></pre>

<h3>5. Configuration du DNAT</h3>
<p>Afin de rendre chaque carte accessible depuis l'extérieur, configurez des règles <strong>DNAT</strong> sur votre serveur principal. Exemple :</p>
<pre><code>iptables -A PREROUTING -t nat -p udp --dport 30000 -j DNAT --to-destination 10.0.3.10:30000
iptables -A PREROUTING -t nat -p udp --dport 30001 -j DNAT --to-destination 10.0.3.15:30000</code></pre>
<p><em>Note : Adaptez les ports et les adresses IP en fonction de votre configuration.</em></p>

<h2>Installation du Dashboard</h2>

<h3>1. Installer Apache et PHP</h3>
<p>Installez Apache et PHP pour le tableau de bord sur votre serveur principal :</p>
<pre><code>apt update
apt install apache2 php php-cli php-common libapache2-mod-php</code></pre>

<h3>2. Configurer le Dashboard</h3>
<p>Modifiez la configuration d'Apache pour pointer vers le répertoire du Dashboard :</p>
<pre><code>DocumentRoot /var/www/minetest</code></pre>
<p>Attribuez les bonnes permissions sur les fichiers du Dashboard :</p>
<pre><code>chown -R www-data:www-data /var/www/minetest</code></pre>
<p>Ensuite, redémarrez Apache pour appliquer les changements :</p>
<pre><code>systemctl restart apache2</code></pre>
<p>Répétez ces étapes pour le fichier <code>index.html</code> dans le répertoire Web si nécessaire.</p>

<h3>3. Personnalisation du Dashboard</h3>
<p>Modifiez les fichiers <code>index.php</code> et <code>index.html</code> selon vos préférences pour personnaliser l'apparence et la fonctionnalité du tableau de bord.</p>

<h2>Installation des Scripts</h2>

<ul>
    <li>Déplacez les scripts <code>.sh</code> dans le répertoire <code>/usr/bin/</code> :</li>
    <pre><code>mv script.sh /usr/bin/</code></pre>

    <li>Déplacez les fichiers <code>.service</code> dans <code>/etc/systemd/system/</code> :</li>
    <pre><code>mv script.service /etc/systemd/system/</code></pre>

    <li>Rechargez <code>systemd</code> pour qu'il prenne en compte les nouveaux fichiers de service :</li>
    <pre><code>systemctl daemon-reload</code></pre>

    <li>Appliquez les droits d'exécution pour l'utilisateur <code>www-data</code> sur les scripts :</li>
    <pre><code>chmod +x /usr/bin/nom_du_script</code></pre>
</ul>

<h2>Informations importantes</h2>
<p>Ce tableau de bord est compatible avec les configurations suivantes :</p>
<ul>
    <li><strong>LXC</strong> (Linux Containers)</li>
    <li><strong>Apache2</strong></li>
    <li><strong>PHP</strong></li>
</ul>

</body>
</html>
