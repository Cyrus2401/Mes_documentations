# Guide Symfony : Référence Complète

Ce guide est une référence pour le développement moderne avec Symfony 6.4 / 7+. Il privilégie l'injection de dépendances, Doctrine et les bonnes pratiques.

## Table des matières
- [Démarrage d'un projet](#démarrage-dun-projet)
- [Architecture et Concepts](#architecture-et-concepts)
- [Base de Données et Doctrine](#base-de-données-et-doctrine)
- [Authentification et Sécurité](#authentification-et-sécurité)
- [Organisation de la Logique Métier](#organisation-de-la-logique-métier)
- [Fonctionnalités avancées](#fonctionnalités-avancées)
- [Commandes utiles](#commandes-utiles)
- [Bonnes pratiques](#bonnes-pratiques)
- [Tests](#tests)
- [Déploiement](#déploiement)

## Démarrage d'un projet

### Création via Symfony CLI (Recommandé)
```bash
# Installation de l'outil CLI
curl -sS https://get.symfony.com/cli/installer | bash

# Créer une application Web complète
symfony new mon-projet --webapp

# Créer un micro-service / API (minimal)
symfony new mon-projet
```

### Serveur Local
```bash
# Lancer le serveur avec support TLS
symfony server:ca:install
symfony server:start
```

## Architecture et Concepts

### Injection de Dépendances (DI) & Autowiring
C'est le cœur de Symfony. Vous n'instanciez presque jamais vos classes avec `new`.
Le conteneur de services injecte automatiquement les classes type-hintées.

```php
// src/Service/InvoiceGenerator.php
class InvoiceGenerator
{
    private $mailer;

    // Autowiring : Symfony sait qu'il doit injecter le service MailerInterface
    public function __construct(MailerInterface $mailer)
    {
        $this->mailer = $mailer;
    }
}
```

### Configuration (services.yaml)
Grâce à l'autowiring, la configuration manuelle est rare.
```yaml
# config/services.yaml
services:
    # Configuration par défaut
    _defaults:
        autowire: true      # Injection automatique
        autoconfigure: true # Tags automatiques (ex: Command, EventSubscriber)

    # Rendre toutes les classes de src/ disponibles comme services
    App\:
        resource: '../src/'
        exclude:
            - '../src/DependencyInjection/'
            - '../src/Entity/'
            - '../src/Kernel.php'
```

## Base de Données et Doctrine

Doctrine est l'ORM par défaut. On utilise désormais les **Attributs PHP 8** pour le mapping.

### Création d'Entités & Migrations
```bash
# Assistant interactif pour créer/modifier une entité
php bin/console make:entity Product

# Créer le fichier de migration
php bin/console make:migration

# Exécuter la migration
php bin/console doctrine:migrations:migrate
```

### Entité Moderne (PHP 8 Attributes)
```php
// src/Entity/Product.php
#[ORM\Entity(repositoryClass: ProductRepository::class)]
class Product
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    #[Assert\NotBlank]
    private ?string $name = null;

    #[ORM\ManyToOne(inversedBy: 'products')]
    #[ORM\JoinColumn(nullable: false)]
    private ?Category $category = null;

    // Getters & Setters...
}
```

### Repositories & QueryBuilder
Ne pas écrire de SQL brut, utiliser le QueryBuilder dans les repositories.

```php
// src/Repository/ProductRepository.php
public function findExpensiveProducts(float $price): array
{
    return $this->createQueryBuilder('p')
        ->andWhere('p.price > :price')
        ->setParameter('price', $price)
        ->orderBy('p.price', 'DESC')
        ->getQuery()
        ->getResult();
}
```

## Authentification et Sécurité

Géré par le **SecurityBundle**.

### Configuration (security.yaml)
```yaml
security:
    password_hashers:
        Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface: 'auto'

    providers:
        app_user_provider:
            entity:
                class: App\Entity\User
                property: email

    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
        main:
            lazy: true
            provider: app_user_provider
            form_login:
                login_path: app_login
                check_path: app_login
            logout:
                path: app_logout

    access_control:
        - { path: ^/admin, roles: ROLE_ADMIN }
```

### Voters (Permissions Fines)
L'équivalent des Policies de Laravel.
```bash
php bin/console make:voter PostVoter
```

```php
// src/Security/PostVoter.php
protected function voteOnAttribute(string $attribute, mixed $subject, TokenInterface $token): bool
{
    $user = $token->getUser();
    /** @var Post $post */
    $post = $subject;

    return match($attribute) {
        'EDIT' => $user === $post->getAuthor(),
        'VIEW' => true,
        default => false,
    };
}
```

Utilisation dans le contrôleur :
```php
#[IsGranted('EDIT', subject: 'post')]
public function edit(Post $post): Response { ... }
```

## Organisation de la Logique Métier

### Services
Toute logique métier complexe doit être dans un Service, pas dans le contrôleur.

```php
// src/Service/OrderManager.php
class OrderManager 
{
    public function validateAndSave(Order $order): void
    {
        // Validation complexe
        // Calcul de taxes
        // Sauvegarde BDD
        // Envoi email
    }
}
```

### Symfony Messenger (Queues & Async)
Pour traiter des tâches en arrière-plan (envoi d'emails, traitement d'images).

```bash
php bin/console make:message SmsNotification
php bin/console make:message-handler SmsNotificationHandler
```

Envoi du message :
```php
public function index(MessageBusInterface $bus)
{
    $bus->dispatch(new SmsNotification('Hello!'));
}
```

Le Handler (exécuté par le worker) :
```php
#[AsMessageHandler]
class SmsNotificationHandler
{
    public function __invoke(SmsNotification $message)
    {
        // Envoyer le SMS
    }
}
```

Lancer le worker :
```bash
php bin/console messenger:consume async
```

## Fonctionnalités avancées

### Event Dispatcher
Découpler le code via des événements.

```php
// Créer l'event
class UserRegisteredEvent extends Event {
    public function __construct(public User $user) {}
}

// Subscriber
class EmailSubscriber implements EventSubscriberInterface
{
    public static function getSubscribedEvents(): array
    {
        return [UserRegisteredEvent::class => 'onUserRegistered'];
    }

    public function onUserRegistered(UserRegisteredEvent $event): void
    {
        // Envoyer email de bienvenue
    }
}
```

### Commandes Console
Créer ses propres commandes CLI.
```bash
php bin/console make:command app:create-user
```

```php
#[AsCommand(name: 'app:create-user', description: 'Crée un nouvel utilisateur')]
class CreateUserCommand extends Command
{
    protected function execute(InputInterface $input, OutputInterface $output): int
    {
        // Logique...
        $io = new SymfonyStyle($input, $output);
        $io->success('User created!');
        return Command::SUCCESS;
    }
}
```

## Commandes utiles

### Cache & Debug
```bash
php bin/console cache:clear        # Vider le cache (dev & prod)
php bin/console debug:router       # Lister les routes
php bin/console debug:autowiring   # Voir les services injectables
php bin/console debug:container    # Voir tous les services
```

### Maker Bundle (Génération de code)
```bash
php bin/console make:controller
php bin/console make:form
php bin/console make:test
php bin/console make:crud
```

## Bonnes pratiques

### Le Contrôleur Mince
Un contrôleur ne doit faire que :
1. Recevoir la Request.
2. Appeler un Service / Form.
3. Retourner une Response.

### Validation
Utilisez le composant Validator dans vos entités ou DTOs.
```php
#[Assert\NotBlank]
#[Assert\Email]
public string $email;
```

### Variables d'Environnement
Ne stockez jamais de secrets dans le code. Utilisez `.env.local` pour vos secrets en local.
```bash
# Afficher les vars réelles
php bin/console debug:dotenv
```

## Tests

Symfony s'intègre parfaitement avec PHPUnit.

```bash
php bin/console make:test
```

### Test Fonctionnel (WebTestCase)
```php
class LoginTest extends WebTestCase
{
    public function testLoginSuccessful(): void
    {
        $client = static::createClient();
        $crawler = $client->request('GET', '/login');

        $form = $crawler->selectButton('Sign in')->form([
            'email' => 'user@example.com',
            'password' => 'password',
        ]);

        $client->submit($form);
        $this->assertResponseRedirects('/dashboard');
    }
}
```

## Déploiement

### Checklist Production
1.  **Environment** : `APP_ENV=prod`.
2.  **Optimisation Composer** : `composer install --no-dev --optimize-autoloader`.
3.  **Cache** :
    ```bash
    php bin/console cache:clear
    php bin/console cache:warmup
    ```
4.  **Base de données** : `php bin/console doctrine:migrations:migrate --no-interaction`.
5.  **Assets** : `php bin/console asset-map:compile` (si AssetMapper) ou `npm run build` (si Webpack Encore).

### Variables d'environnement de Prod
Générez un fichier `.env.local.php` pour la performance :
```bash
composer dump-env prod
```
