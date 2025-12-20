# Guide Complet des Extensions PHP

## Table des matières
- [Extensions de Base](#extensions-de-base)
- [Extensions de Base de Données](#extensions-de-base-de-données)
- [Extensions de Gestion de Fichiers](#extensions-de-gestion-de-fichiers)
- [Extensions de Sécurité](#extensions-de-sécurité)
- [Extensions de Session et Cache](#extensions-de-session-et-cache)
- [Extensions pour Frameworks](#extensions-pour-frameworks)
- [Extensions de Développement](#extensions-de-développement)
- [Extensions de Chiffrement](#extensions-de-chiffrement)
- [Extensions pour API](#extensions-pour-api)
- [Extensions de Performance](#extensions-de-performance)
- [Autres Extensions Utiles](#autres-extensions-utiles)
- [Installation](#installation)
- [Cas d'Usage Spécifiques](#cas-dusage-spécifiques)

## Extensions de Base

Les extensions fondamentales nécessaires pour la plupart des projets PHP :

```bash
php-common     # Composants de base PHP
php-cli        # Interface ligne de commande
php-fpm        # FastCGI Process Manager
php-curl       # Requêtes HTTP
php-json       # Manipulation JSON
php-mbstring   # Support multi-octets (UTF-8)
php-opcache    # Cache de bytecode
php-xml        # Manipulation XML
php-zip        # Gestion archives ZIP
```

### Utilisation courante
```php
// Exemple avec curl
$ch = curl_init('https://api.exemple.com');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$response = curl_exec($ch);
curl_close($ch);

// Exemple avec json
$data = json_decode($response, true);
$json = json_encode(['status' => 'success']);
```

## Extensions de Base de Données

Extensions pour la connectivité aux bases de données :

```bash
php-mysql      # MySQL/MariaDB (PDO)
php-pgsql      # PostgreSQL
php-sqlite3    # SQLite
php-mongodb    # MongoDB
php-redis      # Redis
php-memcached  # Memcached
```

### Exemples de connexion
```php
// MySQL avec PDO
try {
    $pdo = new PDO(
        'mysql:host=localhost;dbname=test',
        'user',
        'password',
        [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
    );
} catch (PDOException $e) {
    echo "Erreur de connexion : " . $e->getMessage();
}

// Redis
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
```

## Extensions de Gestion de Fichiers

Pour le traitement des fichiers et des images :

```bash
php-fileinfo   # Types MIME
php-exif       # Métadonnées images
php-gd         # Manipulation images
php-imagick    # ImageMagick
php-intl       # Internationalisation
```

### Exemples d'utilisation
```php
// Vérification type MIME
$finfo = finfo_open(FILEINFO_MIME_TYPE);
$mimeType = finfo_file($finfo, 'image.jpg');
finfo_close($finfo);

// Manipulation d'image avec GD
$image = imagecreatefromjpeg('source.jpg');
$resized = imagescale($image, 800, 600);
imagejpeg($resized, 'resized.jpg');
```

## Extensions de Sécurité

Extensions essentielles pour la sécurité :

```bash
php-bcmath     # Calculs précis
php-openssl    # Cryptographie SSL/TLS
php-soap       # Services web SOAP
```

### Exemples de sécurité
```php
// Hachage sécurisé
$hash = password_hash('motdepasse', PASSWORD_BCRYPT);
$verify = password_verify('motdepasse', $hash);

// Chiffrement OpenSSL
$data = "données sensibles";
$cipher = "AES-256-CBC";
$key = openssl_random_pseudo_bytes(32);
$iv = openssl_random_pseudo_bytes(openssl_cipher_iv_length($cipher));
$encrypted = openssl_encrypt($data, $cipher, $key, 0, $iv);
```

## Extensions de Session et Cache

Gestion des sessions et du cache :

```bash
php-session    # Sessions PHP
php-redis      # Cache Redis
php-memcached  # Cache Memcached
```

### Exemples de cache
```php
// Session
session_start();
$_SESSION['user_id'] = 123;

// Redis comme cache
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
$redis->set('key', 'value', ['EX' => 3600]); // Expire dans 1h
```

## Extensions pour Frameworks

Extensions requises par les frameworks modernes :

```bash
php-tokenizer  # Parse de code
php-ctype      # Vérification caractères
php-iconv      # Conversion caractères
php-simplexml  # XML simple
php-dom        # Manipulation DOM
php-pdo        # Accès base de données
```

## Extensions de Développement

Outils de développement et debug :

```bash
php-xdebug     # Débogage et profiling
```

### Configuration Xdebug
```ini
; php.ini ou xdebug.ini
xdebug.mode = debug
xdebug.client_host = 127.0.0.1
xdebug.client_port = 9003
xdebug.start_with_request = yes
```

## Installation Complète

### Ubuntu/Debian
```bash
sudo apt install php8.2 \
    php8.2-cli \
    php8.2-fpm \
    php8.2-common \
    php8.2-curl \
    php8.2-json \
    php8.2-mbstring \
    php8.2-opcache \
    php8.2-xml \
    php8.2-zip \
    php8.2-mysql \
    php8.2-pgsql \
    php8.2-sqlite3 \
    php8.2-intl \
    php8.2-gd \
    php8.2-redis \
    php8.2-imagick \
    php8.2-ldap \
    php8.2-bcmath \
    php8.2-soap \
    php8.2-ctype \
    php8.2-tokenizer \
    php8.2-dom \
    php8.2-fileinfo
```

### Vérification des extensions
```bash
php -m          # Liste toutes les extensions
php --ini       # Affiche la configuration
```

## Cas d'Usage Spécifiques

### Génération de QR Code
```php
// Nécessite l'installation de la librairie
// composer require endroid/qr-code

use Endroid\QrCode\QrCode;

$qrCode = new QrCode('https://example.com');
$qrCode->setSize(300);
$qrCode->setMargin(10);

// Génère et sauvegarde l'image
$qrCode->writeFile('qrcode.png');

// Ou affiche directement
header('Content-Type: '.$qrCode->getContentType());
echo $qrCode->writeString();
```

### Optimisation des Performances
```ini
; Configurations recommandées pour opcache
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=4000
opcache.revalidate_freq=60
opcache.fast_shutdown=1
opcache.enable_cli=1
```

## Bonnes Pratiques

1. **Sécurité**
   - Toujours utiliser les dernières versions des extensions
   - Configurer correctement les extensions de sécurité
   - Mettre à jour régulièrement

2. **Performance**
   - Activer opcache en production
   - Configurer le cache approprié (Redis/Memcached)
   - Optimiser les extensions GD/Imagick pour le traitement d'images

3. **Développement**
   - Utiliser Xdebug uniquement en développement
   - Configurer error_reporting appropriément
   - Maintenir les extensions à jour

## Dépannage Courant

```bash
# Vérifier le statut de PHP-FPM
systemctl status php8.2-fpm

# Vérifier les logs PHP
tail -f /var/log/php8.2-fpm.log

# Tester la configuration PHP-FPM
php-fpm8.2 -t

# Redémarrer PHP-FPM
systemctl restart php8.2-fpm
```

## Écosystème Modern & Outils

### Composer (Gestionnaire de dépendances)
Indispensable pour tout projet PHP moderne.
```bash
composer init                        # Initialiser un projet (crée composer.json)
composer require vendor/package      # Installer une librairie
composer require vendor/package --dev # Installer une dépendance de dév (tests, etc.)
composer install                     # Installer les dépendances du composer.lock
composer update                      # Mettre à jour les dépendances (selon composer.json)
composer dump-autoload -o            # Optimiser l'autoloader (pour la prod)
```

### Nouveautés PHP 8+ (À connaître)
```php
// 1. Named Arguments (PHP 8.0)
// Plus besoin de respecter l'ordre ou de mettre null pour les optionnels
htmlspecialchars($string, double_encode: false);

// 2. Match Expression (PHP 8.0)
// Version améliorée et plus stricte du switch
$status = match($code) {
    200, 300 => 'success',
    400, 500 => 'error',
    default => 'unknown',
};

// 3. Nullsafe Operator (PHP 8.0)
// Évite les erreurs "Call to member function on null"
$country = $session?->user?->getAddress()?->country;

// 4. Enums (PHP 8.1)
enum Status: string {
    case DRAFT = 'draft';
    case PUBLISHED = 'published';
}
```

### Outils de Qualité de Code (Static Analysis)
Ces outils sont exécutés via la CLI pour garantir un code propre.

```bash
# PHP CS Fixer : Formate votre code selon les standards (PSR-12)
vendor/bin/php-cs-fixer fix src/

# PHPStan / Psalm : Analyse statique (trouve les bugs sans exécuter le code)
vendor/bin/phpstan analyse src/ --level=max

# Rector : Mise à jour automatique de votre code (ex: upgrade PHP 7.4 -> 8.2)
vendor/bin/rector process src/
```

### Configuration PHP.ini critique (Production)
```ini
memory_limit = 256M           ; Suffisant pour la plupart des apps web
upload_max_filesize = 64M     ; Selon vos besoins d'upload
post_max_size = 64M           ; Doit être >= upload_max_filesize
max_execution_time = 30       ; Éviter les processus infinis
expose_php = Off              ; Masquer la version de PHP (Sécurité)
display_errors = Off          ; Jamais d'erreurs à l'écran en PROD
log_errors = On               ; Toujours loguer les erreurs
error_log = /var/log/php_errors.log
```