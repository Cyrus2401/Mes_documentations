# Guide Laravel : Installation et Configuration

## Table des matières
- [Démarrage d'un projet](#démarrage-dun-projet)
- [Architecture et Concepts](#architecture-et-concepts)
- [Migrations et Base de données](#migrations-et-base-de-données)
- [Authentification et Sécurité](#authentification-et-sécurité)
- [Organisation de la Logique Métier](#organisation-de-la-logique-métier)
- [Fonctionnalités avancées](#fonctionnalités-avancées)
- [API Resources](#api-resources)
- [Commandes utiles](#commandes-utiles)
- [Bonnes pratiques](#bonnes-pratiques)
- [Artisan et CLI](#artisan-et-cli)
- [Gestion des erreurs](#gestion-des-erreurs)
- [Performance et Optimisation](#performance-et-optimisation)
- [Tests](#tests)
- [Déploiement](#déploiement)


## Démarrage d'un projet

### Création d'un nouveau projet
```bash
# Installation basique via Composer
composer create-project laravel/laravel nom-projet

# Installation via Laravel Installer
laravel new nom-projet

# Installation avec version spécifique
composer create-project laravel/laravel:^10.0 nom-projet

cd nom-projet
```

## Architecture et Concepts

### Cycle de vie d'une requête (Simplifié)
1.  **Entrée** : `public/index.php`
2.  **Kernel** : Chargement du framework et des composants de base.
3.  **Service Providers** : Bootstrapping de l'application (Base de données, Routes, etc.).
4.  **Middleware** : Filtrage de la requête (Auth, CSRF).
5.  **Contrôleur** : Traitement de la demande.
6.  **Sortie** : Renvoi de la réponse (Vue ou JSON).

### Service Container et Providers
Le Service Container gère l'injection de dépendances.
```php
// Binding dans un ServiceProvider
$this->app->bind(PaymentGateway::class, function ($app) {
    return new StripePaymentGateway(config('services.stripe.secret'));
});

// Injection automatique dans un contrôleur
public function store(PaymentGateway $paymentGateway) {
    // ...
}
```

## Migrations et Base de données

### Commandes de base
```bash
# Créer une migration et un modèle
php artisan make:model NomModel -m

# Créer migration, modèle et contrôleur
php artisan make:model NomModel -mc

# Exécuter les migrations
php artisan migrate                    # Migration standard
php artisan migrate:fresh              # Réinitialiser et migrer
php artisan migrate:refresh            # Rafraîchir sans truncate
php artisan migrate:rollback           # Annuler dernière migration
php artisan migrate:rollback --step=1  # Revenir à une étape spécifique
```

### Modifications de table
```bash
# Ajouter une colonne
php artisan make:migration add_field_to_table_name --table=table_name

# Supprimer une colonne
php artisan make:migration remove_field_from_table_name --table=table_name

# Modifier une colonne
php artisan make:migration modify_table_name
```

## Authentification et Sécurité

Il existe plusieurs méthodes pour gérer l'authentification dans Laravel.

### 1. Laravel Breeze (Recommandé pour débuter)
Un starter kit simple et léger incluant Login, Register, Password Reset.
```bash
composer require laravel/breeze --dev
php artisan breeze:install
php artisan migrate
npm install && npm run dev
```

### 2. Laravel Sanctum (Pour API et SPA)
Le standard pour authentifier des APIs ou des applications Vue/React.
```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

Utilisation (Token API) :
```php
// Créer un token
$token = $user->createToken('token-name')->plainTextToken;

// Routes protégées (api.php)
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```

### 3. Laravel Fortify (Backend Only)
Moteur d'authentification sans interface graphique (Headless).
```bash
composer require laravel/fortify
php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
php artisan migrate
```

Configuration dans `config/app.php`:
```php
'providers' => [
    // ...
    App\Providers\FortifyServiceProvider::class,
];
```

### Gates et Policies (Autorisation)
Gérez les permissions utilisateur.
```php
// Dans AuthServiceProvider.php
Gate::define('isAdmin', function($user) {
    return $user->role == "admin";
});

// Création d'une Policy liée à un modèle
php artisan make:policy PostPolicy --model=Post
```

Exemple de Policy :
```php
public function update(User $user, Post $post) {
    return $user->id === $post->user_id;
}
```

Usage : `$this->authorize('update', $post);`

## Organisation de la Logique Métier

Pour éviter les "Fat Controllers", séparez votre logique.

### Service Classes
Pour regrouper la logique métier complexe.
```php
// App/Services/OrderService.php
class OrderService {
    public function placeOrder(User $user, array $items) {
        // Logique de calcul, stock, paiement...
    }
}
```

### Actions (Single Action Classes)
Une classe par action, très lisible.
```bash
php artisan make:action CreateUserAction
```
```php
class CreateUserAction {
    public function execute(array $data): User {
        return DB::transaction(function() use ($data) {
            return User::create($data);
        });
    }
}
```

## Fonctionnalités avancées

### Fixtures et Seeders
```bash
# Créer une Factory
php artisan make:factory NomFactory

# Exécuter via Tinker
php artisan tinker
Model::factory()->count(nombre)->create();

# Ou via Seeder
php artisan db:seed
php artisan db:seed --class=UserSeeder
```

### Observers
Écouter les événements d'un modèle (created, updated, deleted).
```bash
php artisan make:observer UserObserver --model=User
```

### Notifications
```bash
php artisan make:notification NomNotification

# Envoyer une notification
$user->notify(new NomNotification($data));
```

### Internationalisation
```bash
composer require laravel-lang/common --dev
```

### MongoDB Integration
```bash
sudo apt install php-mongodb
composer require jenssegers/mongodb

# Configuration .env
DB_CONNECTION=mongodb
DB_HOST=127.0.0.1
DB_PORT=27017
DB_DATABASE=nom_base
```

## API Resources

Transformez vos modèles en réponses JSON formatées.

```bash
php artisan make:resource UserResource
```

```php
// UserResource.php
public function toArray($request)
{
    return [
        'id' => $this->id,
        'name' => $this->name,
        'email' => $this->email,
        'created_at' => $this->created_at->format('d/m/Y'),
        'posts' => PostResource::collection($this->whenLoaded('posts')),
    ];
}
```

Usage dans le contrôleur :
```php
return new UserResource(User::find(1));
return UserResource::collection(User::all());
```

## Commandes utiles

### Maintenance
```bash
php artisan down  # Mode maintenance (site inaccessible)
php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515" # Maintenance avec accès bypass
php artisan up    # Quitter mode maintenance
```

### Nettoyage et Cache
```bash
php artisan optimize:clear # Vider tous les caches
php artisan config:cache   # Mettre en cache la config (PROD)
php artisan route:list     # Lister toutes les routes
```

### Storage
```bash
php artisan storage:link  # Créer lien symbolique public -> storage
```

### Serveur de développement
```bash
php artisan serve --port=3000  # Démarrer sur port spécifique
```

## Bonnes pratiques

### Validation avec FormRequests
Ne validez **jamais** dans le contrôleur. Utilisez une classe dédiée.
```bash
php artisan make:request StorePostRequest
```

```php
public function rules()
{
    return [
        'title' => 'required|max:255',
        'body' => 'required',
    ];
}

// Controller
public function store(StorePostRequest $request)
{
    $validated = $request->validated();
    // ...
}
```

### Enums (PHP 8.1+)
```php
enum UserStatus: string {
    case ACTIVE = 'active';
    case INACTIVE = 'inactive';
}

// Model
protected $casts = [
    'status' => UserStatus::class,
];
```

### Gestion des routes
Utilisez le nommage et le groupement.
```php
Route::group(['middleware' => ['auth']], function() {
    Route::get('/home', [HomeController::class, 'index'])->name('home');
    // autres routes...
});
```

### Requêtes Eloquent courantes
```php
// Exemples de where
Model::where('column', '=', 'value')->get();
Model::whereBetween('column', [1, 100])->get();
Model::whereIn('column', [1, 2, 3])->get();
Model::whereNull('column')->get();
Model::whereDate('created_at', '=', '2019-01-01')->get();

// Scopes (Méthodes réutilisables)
// Dans le modèle : scopeActive($query) { return $query->where('active', 1); }
User::active()->get();

// Pagination
$posts = Post::paginate(5);
```

### Sécurité
- Utilisez toujours les variables `$fillable` ou `$guarded` dans les modèles.
- Activez CSRF protection.
- Configurez correctement les headers de sécurité.

## Artisan et CLI

### Commandes personnalisées
```bash
# Créer une nouvelle commande
php artisan make:command NomCommande

# Structure de base d'une commande
php artisan make:command SendEmails --command=emails:send
```

Exemple de commande personnalisée:
```php
class SendEmails extends Command
{
    protected $signature = 'emails:send {user} {--queue}';
    protected $description = 'Envoyer des emails aux utilisateurs';

    public function handle()
    {
        $userId = $this->argument('user');
        $queue = $this->option('queue');
        // Logique de la commande
    }
}
```

### Tâches planifiées (Scheduler)
Dans `app/Console/Kernel.php`:
```php
protected function schedule(Schedule $schedule)
{
    // Exécuter toutes les minutes
    $schedule->command('emails:send')->everyMinute();
    
    // Exécuter tous les jours à minuit
    $schedule->command('backup:clean')->daily();
    
    // Callback Closure
    $schedule->call(function () {
        DB::table('recent_users')->delete();
    })->daily();
}
```

Configuration Cron serveur :
```bash
* * * * * cd /path-to-project && php artisan schedule:run >> /dev/null 2>&1
```

## Gestion des erreurs

### Pages d'erreur personnalisées
```bash
# Créer les vues d'erreur dans resources/views/errors/
404.blade.php
500.blade.php
503.blade.php
```

### Logging
```php
// Dans un controller
use Illuminate\Support\Facades\Log;

Log::info('User login', ['id' => $user->id]);
Log::error('Something happened');
```

Configuration dans `config/logging.php` (Stack, Daily, Slack, etc.).

### Exceptions personnalisées
```php
class CustomException extends Exception
{
    public function report()
    {
        Log::error('Une erreur personnalisée est survenue');
    }

    public function render($request)
    {
        return response()->view('errors.custom', [], 500);
    }
}
```

## Performance et Optimisation

### Cache
```bash
# Configuration du cache
php artisan cache:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Redis
composer require predis/predis
```

Configuration dans `.env`:
```env
CACHE_DRIVER=redis
REDIS_HOST=127.0.0.1
```

### Queues
Déchargez le traitement lourd.
```bash
# Créer un job
php artisan make:job ProcessPodcast

# Exécuter les workers
php artisan queue:work
```

### Optimisation des images
```php
// Installation intervention/image
composer require intervention/image

// Exemple d'utilisation
use Intervention\Image\ImageManagerStatic as Image;

$img = Image::make('public/foo.jpg');
$img->resize(300, 200);
$img->save('public/foo_modified.jpg');
```

## Tests

### Configuration des tests
```bash
# Créer un test
php artisan make:test UserTest
php artisan make:test UserTest --unit

# Exécuter les tests
php artisan test
```

Exemple de test:
```php
class UserTest extends TestCase
{
    public function test_user_can_be_created()
    {
        $response = $this->post('/api/users', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password'
        ]);

        $response->assertStatus(201);
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com'
        ]);
    }
}
```

### Factories pour les tests
```php
// UserFactory.php
public function definition()
{
    return [
        'name' => fake()->name(),
        'email' => fake()->unique()->safeEmail(),
        'password' => Hash::make('password'),
    ];
}
```

## Déploiement

### Checklist de déploiement
```bash
# Optimisation
composer install --optimize-autoloader --no-dev
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Configuration
php artisan key:generate
php artisan storage:link
php artisan migrate --force

# Permissions
chmod -R 755 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

### Configuration production
Dans `.env`:
```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://votre-domaine.com

CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
```