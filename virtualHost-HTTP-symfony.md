# 📗 Configuration d'un VirtualHost Apache en HTTP pour un projet Symfony (port 9000)

Cette documentation explique **pas à pas** comment configurer un **VirtualHost Apache en HTTP** pour un projet **Symfony**, en utilisant une **adresse IP** et le **port 9000**.

> 🧩 Contexte

* OS : Linux (Debian / Ubuntu)
* Serveur Web : Apache 2.4+
* Framework : Symfony (DocumentRoot = `/public`)
* HTTP (sans SSL)
* Accès via : `http://IP:9000`

---

## 📋 Prérequis

* Apache installé 
* Accès root ou sudo
* Projet Symfony déjà déployé sur le serveur
* Le port `9000` doit être libre

---

## 1️⃣ Activer les modules Apache nécessaires
```bash
sudo a2enmod rewrite headers
sudo systemctl restart apache2
```

---

## 2️⃣ Configurer Apache pour écouter le port 9000

Éditer le fichier :
```bash
sudo nano /etc/apache2/ports.conf
```

Ajouter :
```apache
Listen 9000
```

Redémarrer Apache :
```bash
sudo systemctl restart apache2
```

---

## 3️⃣ Créer le VirtualHost HTTP

Créer le fichier de configuration :
```bash
sudo nano /etc/apache2/sites-available/symfony-9000-http.conf
```

### Configuration recommandée (Symfony + HTTP)
```apache
<VirtualHost *:9000>

    ServerName 185.238.1.103
    ServerAlias localhost

    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/FLASH/eQRCode/public

    <Directory /var/www/html/FLASH/eQRCode/public>
        Options FollowSymLinks
        AllowOverride None
        Require all granted

        <IfModule mod_rewrite.c>
            RewriteEngine On
            Options -MultiViews

            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteRule ^ index.php [QSA,L]
        </IfModule>
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/symfony_9000_error.log
    CustomLog ${APACHE_LOG_DIR}/symfony_9000_access.log combined

</VirtualHost>
```

---

## 4️⃣ Vérifier les droits du projet Symfony
```bash
sudo chown -R www-data:www-data /var/www/html/FLASH/eQRCode
sudo chmod -R 755 /var/www/html/FLASH/eQRCode
```

S'assurer que les dossiers `var/cache` et `var/log` sont accessibles en écriture :
```bash
sudo chmod -R 775 /var/www/html/FLASH/eQRCode/var
```

---

## 5️⃣ Activer le site
```bash
sudo a2ensite symfony-9000-http.conf
sudo systemctl reload apache2
```

---

## 6️⃣ Définir le ServerName global (évite les warnings)
```bash
sudo nano /etc/apache2/apache2.conf
```

Ajouter à la fin :
```apache
ServerName 185.238.1.103
```

Puis :
```bash
sudo systemctl restart apache2
```

---

## 7️⃣ Ouvrir le port 9000 dans le firewall

### Avec UFW
```bash
sudo ufw allow 9000/tcp
sudo ufw reload
```

### Avec iptables
```bash
sudo iptables -A INPUT -p tcp --dport 9000 -j ACCEPT
sudo iptables-save
```

---

## 8️⃣ Vérifications

### Tester la configuration Apache
```bash
sudo apache2ctl configtest
```

Résultat attendu :
```
Syntax OK
```

### Vérifier que le port écoute
```bash
sudo ss -tulpn | grep 9000
```

Résultat attendu :
```
LISTEN  0  511  *:9000  *:*  users:(("apache2",pid=...))
```

### Vérifier les logs en temps réel
```bash
sudo tail -f /var/log/apache2/symfony_9000_error.log
sudo tail -f /var/log/apache2/symfony_9000_access.log
```

---

## 🌐 Accès à l'application

Dans le navigateur :
```
http://185.238.1.103:9000
```

Vous devriez voir :
* La page d'accueil Symfony
* Ou votre application si les routes sont configurées

---

## 🧠 Bonnes pratiques Symfony

### 1. Pointer le DocumentRoot vers `/public`

Toujours utiliser le dossier `public` comme racine web :
```apache
DocumentRoot /var/www/html/FLASH/eQRCode/public
```

### 2. Configuration de l'environnement

En développement :
```env
APP_ENV=dev
APP_DEBUG=1
```

En production :
```env
APP_ENV=prod
APP_DEBUG=0
```

### 3. Nettoyage du cache

Après modification de la configuration :
```bash
php bin/console cache:clear && chmod 777 -R var/
```

### 4. Vérifier les routes Symfony
```bash
php bin/console debug:router
```

---

✍️ *Documentation rédigée pour usage personnel et déploiement Symfony*