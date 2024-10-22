# Guide Complet Git & GitHub 🚀

## Table des matières
- [Introduction à Git & GitHub](#introduction-à-git--github)
- [Concepts de Base](#concepts-de-base)
- [Commandes Essentielles](#commandes-essentielles)
- [Gestion des Branches](#gestion-des-branches)
- [Workflows Git](#workflows-git)
- [Collaboration](#collaboration)
- [Correction des Erreurs](#correction-des-erreurs)
- [Configuration SSH](#configuration-ssh)
- [Commandes Avancées](#commandes-avancées)
- [Bonnes Pratiques](#bonnes-pratiques)

## Introduction à Git & GitHub

### Qu'est-ce que Git ?
Git est un système de contrôle de version décentralisé qui offre trois fonctionnalités principales :
- ✨ Restauration de versions antérieures du code
- 📝 Suivi détaillé de l'évolution du code
- 👥 Collaboration sans conflit entre développeurs

### Git vs GitHub
- **Git** : Système de gestion de versions local
- **GitHub** : Plateforme en ligne hébergeant les dépôts Git (dépôts distants)

## Concepts de Base

### Dépôts (Repositories)
- **Dépôt Local** : Stockage virtuel sur votre machine
- **Dépôt Distant** : Stockage en ligne (ex: sur GitHub)

### Les Trois États de Git
- **Working Directory** : Répertoire de travail où vous modifiez vos fichiers
- **Staging Area** : Zone d'index où vous préparez les fichiers pour le commit
- **Repository** : Base de données Git où sont stockés les commits

### Avantages des Dépôts
- 📊 Historique complet du projet
- 👥 Facilitation du travail en équipe
- 🌐 Participation aux projets open source
- 🔍 Traçabilité des modifications
- 📝 Documentation des changements

## Commandes Essentielles

### Configuration Initiale
```bash
# Configurer son identité
git config --global user.name "Votre Nom"
git config --global user.email "votre.email@exemple.com"

# Configurer l'éditeur par défaut
git config --global core.editor "code"  # Pour VS Code
```

### Initialisation et Configuration
```bash
# Initialiser un nouveau dépôt
git init

# Vérifier l'état des fichiers
git status

# Cloner un dépôt existant
git clone url_du_depot
```

### Gestion des Fichiers
```bash
# Indexer un ou plusieurs fichiers
git add nomFichier
git add fichier1 fichier2
git add .  # Indexer tous les fichiers modifiés

# Créer une version (commit)
git commit -m "Description des modifications"

# Envoyer les modifications vers GitHub
git push -u origin main

# Récupérer les modifications du dépôt distant
git pull
```

### Inspection et Comparaison
```bash
# Voir l'historique des commits
git log
git log --oneline  # Version condensée

# Voir les différences
git diff  # Modifications non indexées
git diff --staged  # Modifications indexées
```

## Gestion des Branches

### Commandes de Base
```bash
# Afficher les branches
git branch
git branch -v  # Avec le dernier commit

# Créer une nouvelle branche
git branch nomDeLaBranche
git checkout -b nomDeLaBranche  # Créer et basculer

# Changer de branche
git checkout nomDeLaBranche
git switch nomDeLaBranche  # Nouvelle syntaxe

# Fusionner une branche
git merge nomDeLaBranche

# Supprimer une branche
git branch -d nomDeLaBranche    # Suppression simple
git branch -D nomDeLaBranche    # Suppression forcée
```

### Gestion des Modifications Temporaires (Stash)
```bash
# Sauvegarder temporairement les modifications
git stash
git stash save "message descriptif"

# Appliquer les modifications sauvegardées
git stash apply
git stash pop  # Applique et supprime le stash

# Lister les stash
git stash list

# Appliquer un stash spécifique
git stash apply stash@{0}

# Supprimer un stash
git stash drop stash@{0}
```

## Workflows Git

### GitFlow
Structure de branches recommandée pour les grands projets :
- `main` : Code en production
- `develop` : Développement courant
- `feature/*` : Nouvelles fonctionnalités
- `release/*` : Préparation des versions
- `hotfix/*` : Corrections urgentes

### GitHub Flow
Workflow simplifié :
- Une branche `main` principale
- Des branches de fonctionnalités
- Utilisation intensive des Pull Requests

## Collaboration

### Fork et Pull Request
1. Fork du projet sur GitHub
2. Clone de votre fork
```bash
git clone url_de_votre_fork
```
3. Création d'une branche de fonctionnalité
```bash
git checkout -b nouvelle-fonctionnalite
```
4. Push et création de Pull Request

### Synchronisation avec le Dépôt Original
```bash
# Ajouter le dépôt original comme remote
git remote add upstream url_depot_original

# Synchroniser avec le dépôt original
git fetch upstream
git merge upstream/main
```

### Gestion des Conflits
1. Identifier les fichiers en conflit
```bash
git status
```
2. Ouvrir les fichiers et résoudre les conflits
3. Marquer comme résolu
```bash
git add fichier_resolu
```
4. Finaliser le merge
```bash
git commit -m "Résolution des conflits"
```

## Correction des Erreurs

### Modification des Commits
```bash
# Modifier le dernier message de commit
git commit --amend -m "Nouveau message"

# Ajouter des fichiers oubliés au dernier commit
git add fichierOublie.txt
git commit --amend --no-edit
```

### Annulation des Modifications
```bash
# Annuler les modifications (branche publique)
git revert HEAD^

# Annuler les modifications (branche privée)
git reset HEAD

# Annuler les modifications dans le working directory
git checkout -- fichier
git restore fichier  # Nouvelle syntaxe
```

### Types de Reset
```bash
# Reset complet (supprime tout l'historique après)
git reset commitCible --hard

# Reset en conservant les modifications
git reset --mixed    # Conserve les fichiers modifiés
git reset --soft     # Conserve tout en mémoire
```

## Configuration SSH

### Création et Installation d'une Clé SSH
1. Générer la clé :
```bash
ssh-keygen -t rsa -b 4096 -C "votre.email@example.com"
```

2. Localisation des clés :
- Windows : `C:\Users\VotreNomUtilisateur\.ssh`
- Linux/MacOS : `~/.ssh/`
- La clé publique est dans le fichier `id_rsa.pub`

3. Configuration sur GitHub :
- Aller dans Settings > SSH and GPG keys
- Cliquer sur "New SSH Key"
- Coller le contenu de votre clé publique

## Commandes Avancées

### Tags et Versions
```bash
# Créer un tag
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"

# Pousser les tags
git push --tags
```

### Recherche et Debug
```bash
# Chercher dans l'historique
git log -S "terme_recherche"

# Trouver qui a modifié une ligne
git blame fichier

# Voir l'historique d'un fichier
git log -p fichier
```

### Nettoyage et Maintenance
```bash
# Nettoyer les fichiers non suivis
git clean -n  # Simulation
git clean -f  # Suppression réelle

# Compresser le dépôt
git gc

# Vérifier l'intégrité
git fsck
```

## Bonnes Pratiques

### Messages de Commit
- Utiliser l'impératif présent
- Première ligne : résumé court (50 caractères max)
- Ligne vide puis description détaillée si nécessaire
- Exemple :
```
Add user authentication feature

- Implement JWT token generation
- Add password hashing
- Create user registration endpoint
```

### Organisation du Travail
- 🔄 Faire des commits atomiques (une modification = un commit)
- 📝 Écrire des messages de commit descriptifs
- 🔍 Vérifier régulièrement `git status` et `git diff`
- 🌿 Créer une branche par fonctionnalité
- 📊 Maintenir un historique propre
- 🔒 Ne jamais modifier l'historique public
- 📚 Documenter les changements importants

## Notes Importantes
- 🔄 `git reset` revient à l'état précédent sans créer de commit
- ➕ `git revert` crée un nouveau commit d'annulation
- 💡 Pensez à faire régulièrement des commits avec des messages clairs
- 🔍 Utilisez `git status` fréquemment pour vérifier l'état de votre dépôt
- ⚠️ Faites toujours un backup avant les opérations délicates
- 🔒 Ne stockez jamais de secrets dans Git