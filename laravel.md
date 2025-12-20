# Guide Laravel : Référence Rapide

## Création de projet : webapp & api

### Création de projet
```bash
# Créer un nouveau projet Laravel complet (WebApp & API)
# C'est la commande standard pour tout type de projet.
composer create-project laravel/laravel nom-projet

# Via l'installateur global Laravel (Alternative plus rapide)
laravel new nom-projet

# Installation spécifique pour API (Laravel 11+)
# Laravel est "API ready" par défaut, mais pour installer les fichiers de route API :
php artisan install:api

# Installation des dépendances après un clonage Git
composer install
npm install && npm run dev
```

### Serveur de développement
```bash
# Lancer le serveur de développement local
php artisan serve

# Lancer le serveur sur un port spécifique (ex: 8080)
php artisan serve --port=8080
```

## Commandes de base

### Génération de Code (Make)
Utilisez ces commandes pour créer rapidement les composants de votre application.

```bash
# Créer un Modèle Eloquent (Représentation d'une table BDD)
php artisan make:model Product

# Créer un Modèle + Fichier de Migration associé
php artisan make:model Product -m

# Créer un Modèle + Migration + Contrôleur
php artisan make:model Product -mc

# Créer la totale : Modèle, Migration, Contrôleur Resource, Requests, Policy
php artisan make:model Product --all

# Créer un Contrôleur (Gère la logique HTTP)
php artisan make:controller ProductController

# Créer un Contrôleur de type "Resource" (avec méthodes index, create, store...)
php artisan make:controller ProductController --resource

# Créer une Vue Blade (Template HTML)
php artisan make:view products.index

# Créer une FormRequest (Validation des données entrantes)
php artisan make:request StoreProductRequest

# Créer un Middleware (Filtrage des requêtes HTTP)
php artisan make:middleware EnsureIsAdmin

# Créer une Policy (Gestion des autorisations utilisateur)
php artisan make:policy ProductPolicy

# Créer un Seeder (Remplissage de la BDD avec des données)
php artisan make:seeder ProductsSeeder

# Créer une Factory (Génération de fausses données pour tests)
php artisan make:factory ProductFactory

# Créer un Observer (Écoute les événements d'un modèle : created, updated...)
php artisan make:observer ProductObserver

# Créer une Commande Artisan personnalisée
php artisan make:command SendEmails

# Créer un Job (Tâche de fond pour les files d'attente)
php artisan make:job ProcessOrder

# Créer une classe Mailable (Pour l'envoi d'emails)
php artisan make:mail OrderShipped

# Créer une Notification (Email, SMS, Slack...)
php artisan make:notification InvoicePaid

# Créer une API Resource (Transformation de modèles en JSON)
php artisan make:resource ProductResource

# Créer un Test (Pest ou PHPUnit)
php artisan make:test UserTest
```

### Modifications de table

#### Gestion des Migrations
```bash
# Créer un fichier de migration pour créer une table
php artisan make:migration create_products_table

# Créer un fichier de migration pour modifier une table existante
php artisan make:migration add_votes_to_users_table --table=users

# Exécuter les migrations en attente (Mise à jour de la BDD)
php artisan migrate

# Annuler (Rollback) la dernière migration exécutée
php artisan migrate:rollback

# Tout supprimer et tout relancer (Reset complet de la BDD)
php artisan migrate:fresh

# Rafraîchir les migrations (Rollback tout + Migrate)
php artisan migrate:refresh

# Rafraîchir et relancer les seeders (Idéal pour reset en dev)
php artisan migrate:refresh --seed
```

#### Exemples de Schema Builder
À utiliser dans la méthode `up()` de vos fichiers de migration.

```php
// Création de table
Schema::create('users', function (Blueprint $table) {
    $table->id();                                   // Auto-increment primary key
    $table->string('name');                         // VARCHAR(255)
    $table->string('email')->unique();              // VARCHAR avec index unique
    $table->text('bio')->nullable();                // TEXT, autorise NULL
    $table->boolean('is_active')->default(true);    // BOOL avec valeur par défaut
    $table->foreignId('role_id')->constrained();    // Clé étrangère vers 'roles'
    $table->timestamps();                           // created_at et updated_at
});

// Modification de table
Schema::table('users', function (Blueprint $table) {
    $table->string('phone')->after('email');        // Ajouter colonne après une autre
    $table->dropColumn('bio');                      // Supprimer colonne
    $table->string('name', 100)->change();          // Modifier type/taille (requiert doctrine/dbal)
    $table->renameColumn('from', 'to');             // Renommer
});
```

## Commandes utiles

### Maintenance & Système
```bash
# Mode maintenance
php artisan down
php artisan down --secret="mon-secret" # Avec accès bypass
php artisan up                         # Sortir du mode maintenance

# Cache & Optimisation
php artisan optimize        # Cache config, routes, etc.
php artisan optimize:clear  # Vider tous les caches
php artisan cache:clear     # Vider cache application
php artisan config:clear    # Vider cache config
php artisan route:clear     # Vider cache routes
php artisan view:clear      # Vider cache vues
```

### Base de données
```bash
php artisan db:seed                     # Lancer tous les seeders
php artisan db:seed --class=UserSeeder  # Lancer un seeder spécifique
php artisan db:wipe                     # Supprimer toutes les tables
```

### Utility & Debug
```bash
# Lister les routes enregistrées
php artisan route:list
php artisan route:list --path=api    # Filtrer par URL

# Console interactive PHP avec contexte Laravel
php artisan tinker

# Générer clé d'application (après clonage)
php artisan key:generate

# Créer le lien symbolique public/storage -> storage/app/public
php artisan storage:link

# Publier les assets des packages
php artisan vendor:publish
```

### Authentification (Starter Kits)
```bash
# Breeze (Recommandé débutants)
php artisan breeze:install

# Sanctum (API Tokens)
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

## Bonnes pratiques

*   **Validation** : Ne validez jamais dans le contrôleur. Utilisez des **FormRequests** (`php artisan make:request`) pour séparer la logique de validation.
    ```php
    public function rules() { return ['title' => 'required|max:255']; }
    ```
*   **Contrôleurs minces** : Déportez la logique métier complexe dans des **Service Classes** ou des **Actions**. Le contrôleur ne doit que passer des données.
*   **Eloquent** :
    *   Utilisez `$fillable` dans vos modèles pour éviter les failles Mass Assignment.
    *   Utilisez des **Scopes** pour les requêtes réutilisables (`scopeActive($query)`).
    *   Utilisez le **Eager Loading** (`User::with('posts')->get()`) pour éviter le problème N+1 requêtes.
*   **DRY (Don't Repeat Yourself)** : Utilisez les **Model Factories** pour vos tests et seeders au lieu de créer des objets manuellement.
*   **Sécurité** :
    *   Échappez toujours les sorties (`{{ $var }}` dans Blade le fait automatiquement).
    *   Utilisez les **Policies** ou **Gates** pour l'autorisation (`$this->authorize('update', $post)`).
*   **Configuration** : Utilisez toujours `config('dossier.cle')` pour accéder aux valeurs, jamais `env()` en dehors des fichiers de config.

## Performance et Optimisation

### Production Checklist
1.  **Dépendances** : `composer install --optimize-autoloader --no-dev`
2.  **Caches** :
    ```bash
    php artisan config:cache
    php artisan route:cache
    php artisan view:cache
    ```
    *Note : Une fois `config:cache` exécuté, la fonction `env()` ne renvoie plus rien. Utilisez `config()`.*

### Queues (Files d'attente)
Déchargez les tâches longues (emails, traitement d'image) vers les queues.
```bash
# Lancer le worker
php artisan queue:work --tries=3
```

### Base de données
*   Indexez vos colonnes fréquemment recherchées dans les migrations.
*   Utilisez `chunk()` pour traiter de grands volumes de données.
    ```php
    User::chunk(100, function ($users) { foreach ($users as $user) { ... } });
    ```

### Cache Driver
Passez de `file` à `redis` ou `memcached` en production pour de meilleures performances.
```env
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
```