# Guide Symfony : Référence Rapide

## Création de projet : webapp & api

### Création de projet
```bash
# Installer le binaire Symfony CLI (Recommandé)
curl -sS https://get.symfony.com/cli/installer | bash

# Créer une application Web complète (MVC, Twig, Doctrine, etc.)
symfony new nom-projet --webapp

# Créer un micro-service ou API (Minimal)
symfony new nom-projet

# Alternative via Composer
composer create-project symfony/skeleton nom-projet
```

### Serveur Local
```bash
# Installer le certificat local TLS (HTTPS)
symfony server:ca:install

# Démarrer le serveur Web local
symfony server:start

# Démarrer en arrière-plan
symfony server:start -d

# Ouvrir dans le navigateur
symfony open:local
```

## Commandes de base

### Génération de Code (Maker Bundle)
Symfony utilise `make` pour générer du code squelette.

```bash
# Créer un Contrôleur (Route + Réponse HTTP)
php bin/console make:controller HomeController

# Créer une Entité (Classe PHP mappée en BDD)
php bin/console make:entity Product

# Créer un Formulaire (HTML + Validation)
php bin/console make:form ProductType

# Générer un CRUD complet (Contrôleur + Form + Vues)
php bin/console make:crud Product

# Créer un fichier de Migration (Versionning structure BDD)
php bin/console make:migration

# Créer une Factory (Données de test)
php bin/console make:factory

# Créer des Fixtures (Chargement données initiales)
php bin/console make:fixtures

# Créer une Commande Console personnalisée
php bin/console make:command app:my-command

# Créer un Event Subscriber (Écoute événements)
php bin/console make:subscriber

# Créer un Message & Handler (Queue Messenger)
php bin/console make:message

# Créer un Composant Twig (Vue réutilisable)
php bin/console make:twig-component

# Créer un Voter (Règles de permission avancées)
php bin/console make:voter

# Créer un Test (PHPUnit / Panther)
php bin/console make:test

# Créer un Serializer (Normalisation JSON/XML)
php bin/console make:serializer:normalizer
```

### Modifications de table

#### Workflow Doctrine
1.  **Modifier les entités PHP** (Ajouter des propriétés, attributs, etc.).
    ```bash
    # Utiliser l'assistant interactif pour modifier une entité exisante
    php bin/console make:entity User
    ```
2.  **Générer la migration**.
    ```bash
    # Créer le fichier SQL de différence
    php bin/console make:migration
    ```
3.  **Appliquer la migration**.
    ```bash
    # Exécuter le SQL en base
    php bin/console doctrine:migrations:migrate
    ```

#### Autres commandes Doctrine
```bash
# Voir le SQL qui sera exécuté (Debug)
php bin/console doctrine:schema:update --dump-sql

# Valider que le mapping PHP correspond à la BDD
php bin/console doctrine:schema:validate

# Lister l'historique des migrations
php bin/console doctrine:migrations:list

# Annuler une migration (Rollback)
php bin/console doctrine:migrations:execute --down Version20230101120000

# (Dev) Recréer la base de données de zéro
php bin/console doctrine:database:drop --force
php bin/console doctrine:database:create
php bin/console doctrine:migrations:migrate
php bin/console doctrine:fixtures:load
```

## Commandes utiles

### Debug & Inspection
```bash
# Debugger le routeur
php bin/console debug:router
php bin/console debug:router app_home # Détail d'une route

# Debugger le container de services
php bin/console debug:container
php bin/console debug:autowiring type-hint # Trouver service par type

# Debugger les événements
php bin/console debug:event-dispatcher

# Debugger la configuration
php bin/console debug:config framework
```

### Cache & Maintenance
```bash
# Vider le cache (Environnement actuel)
php bin/console cache:clear

# Vider le cache de production (si on est en dev)
php bin/console cache:clear --env=prod

# Préchauffer le cache (Warmup)
php bin/console cache:warmup
```

### Qualité de code (Linters)
```bash
php bin/console lint:yaml config/
php bin/console lint:twig templates/
php bin/console lint:container
```

### Messenger (Queue)
```bash
# Consommer les messages
php bin/console messenger:consume async -vv

# Voir les transports
php bin/console debug:messenger
```

### Assets (si AssetMapper)
```bash
php bin/console importmap:require bootstrap
php bin/console asset-map:compile
```

## Bonnes pratiques

*   **Injection de dépendances** : Utilisez le **Constructor Injection** et l'**Autowiring**. N'instanciez pas vos services manuellement. Typ-hintez les interfaces plutôt que les classes concrètes quand possible.
    ```php
    public function __construct(private MailerInterface $mailer) {}
    ```
*   **Repositories** : Utilisez le **QueryBuilder** standard. Évitez le SQL brut sauf nécessité absolue. Isolez les requêtes DQL dans le repository, pas dans le contrôleur.
*   **Contrôleurs** : Ils doivent rester minces. Pas de logique métier (calculs, règles complexes) dans le contrôleur. Appelez un Service pour ça.
*   **Environnement** : Tous les paramètres changeants (clés API, credentials DB) vont dans `.env` ou `.env.local`. Les paramètres statiques vont dans `parameters` (`services.yaml`).
*   **Secrets** : Pour les données sensibles en prod, utilisez le coffre-fort de secrets Symfony :
    ```bash
    php bin/console secrets:set CLÉ_API
    ```
*   **Constantes** : Évitez les "magic numbers". Utilisez des constantes de classe ou des Enums PHP 8.1+.

## Performance et Optimisation

### Production Checklist
1.  **Environment** : Définir `APP_ENV=prod` dans `.env.local`.
2.  **Composer** : `composer install --no-dev --optimize-autoloader`.
3.  **Cache** :
    ```bash
    php bin/console cache:clear
    php bin/console cache:warmup
    ```
4.  **Variables d'environment** : Dumper les variables pour éviter le parsing du fichier `.env` à chaque requête.
    ```bash
    composer dump-env prod
    ```

### Doctrine
*   **Lazy Loading** : Attention aux associations. Utilisez `fetch="EXTRA_LAZY"` pour les collections volumineuses si vous n'avez besoin que du `count()`.
*   **Query Cache** : Doctrine met en cache le DQL parsing.

### PHP OpCache
Assurez-vous que l'extension **OpCache** est activée et configurée pour précharger (`opcache.preload`) le fichier `config/preload.php` généré par Symfony.

### Profiling
Utilisez **Blackfire.io** ou le **Symfony Profiler** (en dev uniquement) pour identifier les goulots d'étranglement.
