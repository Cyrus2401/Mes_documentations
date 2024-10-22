# Guide Laravel : Installation et Configuration

## Table des matières
- [Démarrage d'un projet](#démarrage-dun-projet)
- [Migrations et Base de données](#migrations-et-base-de-données)
- [Installation des dépendances front-end](#installation-des-dépendances-front-end)
- [Authentification et Sécurité](#authentification-et-sécurité)
- [Fonctionnalités avancées](#fonctionnalités-avancées)
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
# Installation basique
composer create-project laravel/laravel nom-projet

# Installation avec version spécifique
composer create-project laravel/laravel:^8.0 nom-projet

cd nom-projet
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

## Installation des dépendances front-end

### jQuery
```bash
npm install jquery-ui --save-dev

# Dans resources/js/app.js
import $ from 'jquery';
window.$ = window.jQuery = $;
import 'jquery-ui/ui/widgets/datepicker.js';

# Dans resources/sass/app.scss
@import '~jquery-ui/themes/base/all.css';
```

### Tailwind CSS
```bash
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
npx tailwindcss init

# Configuration tailwind.config.js
module.exports = {
  purge: [
    './resources/**/*.blade.php',
    './resources/**/*.js',
    './resources/**/*.vue',
  ],
  // ...
}

# Dans resources/css/app.css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Alpine.js
```bash
npm install alpinejs

# Dans resources/js/app.js
import 'alpinejs';
import Alpine from 'alpinejs'
window.Alpine = Alpine 
Alpine.start()
```

## Authentification et Sécurité

### Laravel Fortify
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

### Gates et Policies
```php
// Dans AuthServiceProvider.php
Gate::define('isAdmin', function($user) {
    return $user->role == "admin";
});

// Création d'une Policy
php artisan make:policy PostPolicy --model=Post
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
DB_URI=mongodb://localhost:27017
```

## Commandes utiles

### Maintenance
```bash
php artisan down  # Mode maintenance
php artisan up    # Quitter mode maintenance
```

### Storage
```bash
php artisan storage:link  # Créer lien symbolique
```

### Serveur de développement
```bash
php artisan serve --port=3000  # Démarrer sur port spécifique
```

## Bonnes pratiques

### Gestion des routes
```php
Route::group(['middleware' => ['auth']], function() {
    Route::get('/home', function() { 
        return view('home'); 
    })->name('home');
    // autres routes...
});
```

### Requêtes Eloquent courantes
```php
// Exemples de where
Model::where('column', '=', 'value')->get();
Model::whereBetween('column', [1, 100])->get();
Model::whereNotBetween('column', [1, 100])->get();
Model::whereIn('column', [1, 2, 3])->get();
Model::whereNotIn('column', [1, 2, 3])->get();
Model::whereNull('column')->get();
Model::whereNotNull('column')->get();
Model::whereDate('created_at', '=', '2019-01-01')->get();
Model::whereMonth('created_at', '=', 1)->get();
Model::whereDay('created_at', '=', 15)->get();
Model::whereYear('created_at', '=', 2020)->get();
Model::whereColumn('first_name', 'last_name')->get();

// Pagination
$posts = Post::paginate(5);
```

### Sécurité
- Utilisez toujours les variables $fillable ou $guarded dans les modèles
- Validez les entrées utilisateur via FormRequest
- Utilisez les Gates et Policies pour l'autorisation
- Activez CSRF protection
- Configurez correctement les headers de sécurité

### Ressources additionnelles
- [Laravel Debugbar](https://github.com/barryvdh/laravel-debugbar)
- [Laravel IDE Helper](https://github.com/barryvdh/laravel-ide-helper)
- [Documentation sur la sécurité](https://saasykit.com/blog/12-top-security-best-practices-for-your-laravel-application)
- [Guide Multi-Role CMS](https://techsolutionstuff.com/post/building-a-multi-role-cms-with-custom-policies-in-laravel-11)
- [Génération de documentation](https://saasykit.com/blog/how-to-generate-documentation-for-your-laravel-project)

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

### Tâches planifiées
Dans `app/Console/Kernel.php`:
```php
protected function schedule(Schedule $schedule)
{
    // Exécuter toutes les minutes
    $schedule->command('emails:send')->everyMinute();
    
    // Exécuter tous les jours à minuit
    $schedule->command('backup:clean')->daily();
    
    // Tâche personnalisée
    $schedule->call(function () {
        DB::table('recent_users')->delete();
    })->daily();
}
```

Configuration Cron:
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

Log::emergency($message);
Log::alert($message);
Log::critical($message);
Log::error($message);
Log::warning($message);
Log::notice($message);
Log::info($message);
Log::debug($message);
```

Configuration dans `config/logging.php`:
```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily'],
    ],
    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 14,
    ],
],
```

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
REDIS_PASSWORD=null
REDIS_PORT=6379
```

### Queues
```bash
# Créer un job
php artisan make:job ProcessPodcast

# Exécuter les workers
php artisan queue:work
php artisan queue:listen

# Superviser avec Supervisor
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/forge/app.com/artisan queue:work --sleep=3 --tries=3
autostart=true
autorestart=true
user=forge
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log
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
./vendor/bin/phpunit
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

LOG_CHANNEL=stack
LOG_LEVEL=error
```

### Surveillance et maintenance
```bash
# Installer Telescope pour le développement
composer require laravel/telescope
php artisan telescope:install
php artisan migrate

# Horizon pour la gestion des queues
composer require laravel/horizon
php artisan horizon:install
```

### Ressources additionnelles
- [Laravel Debugbar](https://github.com/barryvdh/laravel-debugbar)
- [Laravel IDE Helper](https://github.com/barryvdh/laravel-ide-helper)
- [Documentation sur la sécurité](https://saasykit.com/blog/12-top-security-best-practices-for-your-laravel-application)
- [Guide Multi-Role CMS](https://techsolutionstuff.com/post/building-a-multi-role-cms-with-custom-policies-in-laravel-11)
- [Génération de documentation](https://saasykit.com/blog/how-to-generate-documentation-for-your-laravel-project)