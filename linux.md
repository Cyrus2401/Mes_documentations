# Guide des Commandes Linux Essentielles

## Table des matières
- [Navigation et Gestion des Fichiers](#navigation-et-gestion-des-fichiers)
- [Gestion des Utilisateurs et Permissions](#gestion-des-utilisateurs-et-permissions)
- [Visualisation et Manipulation de Fichiers](#visualisation-et-manipulation-de-fichiers)
- [Compression et Archives](#compression-et-archives)
- [Téléchargement et Transfert](#téléchargement-et-transfert)
- [Système et Réseau](#système-et-réseau)
- [Gestion des Processus](#gestion-des-processus)
- [Utilitaires et Informations Système](#utilitaires-et-informations-système)
- [Astuces et Personnalisation](#astuces-et-personnalisation)
- [Recherche de Fichiers](#recherche-de-fichiers)

## Navigation et Gestion des Fichiers

### Commande ls (Lister les fichiers)
```bash
ls          # Affiche le contenu du répertoire courant
ls -a       # Affiche tous les fichiers, y compris les fichiers cachés
ls -l       # Format détaillé (permissions, propriétaire, taille, date)
ls -h       # Tailles lisibles (Ko, Mo, Go)
ls -R       # Affiche récursivement les sous-répertoires
ls -S       # Trie par taille décroissante
ls -t       # Trie par date de modification
ls -r       # Ordre inverse
```

#### Filtres avancés pour ls
```bash
ls -d /etc/s*          # Fichiers commençant par 's' dans /etc
ls -d /etc/????        # Fichiers avec exactement 4 caractères
ls -d /etc/[abcd]*     # Fichiers commençant par a, b, c ou d
```

### Navigation
```bash
pwd         # Affiche le répertoire courant
cd [chemin] # Change de répertoire
cd ..       # Remonte d'un niveau
cd ~        # Retourne au répertoire personnel
cd -        # Retourne au répertoire précédent
```

### Manipulation de fichiers
```bash
cp [source] [destination]   # Copie de fichiers
cp -r                      # Copie récursive (pour les dossiers)
cp -v                      # Mode verbeux (affiche les détails)
mv [source] [destination]  # Déplace ou renomme
rm [fichier]              # Supprime un fichier
rm -r [dossier]           # Supprime un dossier et son contenu
rm -i                     # Demande confirmation avant suppression
```

### Copie Serveur vers PC & PC vers Serveur
```bash
# Copie du serveur vers le PC (à exécuter sur le PC ou sur le serveur)
scp -r usernameServer@ipServer:/chemin/sur/serveur /chemin/sur/PC

# Copie du PC vers le serveur (à exécuter sur le PC)
scp -r /chemin/sur/PC usernameServer@ipServer:/chemin/sur/serveur
```

## Gestion des Utilisateurs et Permissions

### Utilisateurs et Groupes
```bash
sudo                       # Exécute une commande en tant que super utilisateur
su [utilisateur]          # Change d'utilisateur
whoami                    # Affiche l'utilisateur actuel
passwd [utilisateur]      # Change le mot de passe
passwd -S [utilisateur]   # État du mot de passe
```

### Gestion des groupes
```bash
groups                              # Affiche les groupes de l'utilisateur actuel
usermod -aG sudo [utilisateur]      # Ajoute un utilisateur au groupe sudo
gpasswd -a [utilisateur] [groupe]   # Ajoute un utilisateur à un groupe
gpasswd -d [utilisateur] [groupe]   # Retire un utilisateur d'un groupe
```

### Permissions
```bash
chmod [permissions] [fichier]       # Modifie les permissions
chmod 755 script.sh                 # Exemple : permissions en notation octale
chmod +x script.sh                  # Rend exécutable

chown [utilisateur]:[groupe] [fichier]  # Change le propriétaire
chown user:group fichier.txt            # Exemple
```

## Visualisation et Manipulation de Fichiers

### Lecture de fichiers
```bash
cat [fichier]        # Affiche le contenu
head [fichier]       # Affiche les 10 premières lignes
head -n 5           # Affiche les 5 premières lignes
tail [fichier]      # Affiche les 10 dernières lignes
tail -f             # Suit les modifications en temps réel

grep [motif] [fichier]  # Recherche un motif
grep -i                 # Insensible à la casse
grep -r                 # Recherche récursive
grep -v                 # Inverse la recherche
```

### Manipulation de contenu
```bash
echo "texte" > fichier.txt     # Écrit dans un fichier (écrase)
echo "texte" >> fichier.txt    # Ajoute à la fin du fichier
echo "" > fichier.txt          # Vide le contenu du fichier
```

## Système et Réseau

### Commandes système
```bash
shutdown [option] [message]    # Arrêt du système
shutdown now                   # Arrêt immédiat
shutdown +10                   # Arrêt dans 10 minutes
reboot                        # Redémarre le système
ps                           # Liste les processus actifs
ps -e                        # Tous les processus système
top                         # Moniteur système interactif
```

### Réseau
```bash
ifconfig            # Configuration réseau
ip a                # Alternative moderne à ifconfig
ping [hôte]         # Test de connectivité
netstat             # Statistiques réseau
ss                  # Alternative moderne à netstat
```

## Utilitaires et Informations Système

### Informations système
```bash
uname -a            # Informations système complètes
df -h               # Espace disque disponible
free -h             # Mémoire disponible
date                # Date et heure actuelles
cal                 # Calendrier
cal 1 2024          # Calendrier janvier 2024
```

### Documentation
```bash
man [commande]      # Manuel d'utilisation
info [commande]     # Documentation détaillée
whatis [commande]   # Description courte
whereis [commande]  # Localisation des fichiers
```

## Astuces et Personnalisation

### Historique
```bash
history             # Historique des commandes
history 5           # 5 dernières commandes
!n                  # Exécute la commande n de l'historique
!!                  # Répète la dernière commande
```

### Création d'alias
```bash
alias ll='ls -la'   # Crée un alias permanent (à mettre dans .bashrc)
```

### Tips pour les débutants
> **Note :** Pour exécuter un script, ajoutez `./` devant le nom :
```bash
./script.sh
```

> **Important :** Pour les noms de fichiers avec espaces, utilisez des guillemets :
```bash
cd "Mon Dossier"
ls "Nom avec espaces"
```

## Compression et Archives

### tar (archivage)
```bash
# Création d'archive
tar -cvf archive.tar dossier/     # Crée une archive tar
tar -czvf archive.tar.gz dossier/ # Crée une archive tar compressée en gzip
tar -cjvf archive.tar.bz2 dossier/ # Crée une archive tar compressée en bzip2

# Extraction d'archive
tar -xvf archive.tar              # Extrait une archive tar
tar -xzvf archive.tar.gz          # Extrait une archive tar.gz
tar -xjvf archive.tar.bz2         # Extrait une archive tar.bz2

# Options communes
# -c : créer une archive
# -x : extraire une archive
# -v : mode verbeux
# -f : spécifier le nom du fichier
# -z : compression gzip
# -j : compression bzip2
```

### zip et unzip
```bash
zip -r archive.zip dossier/       # Crée une archive zip
unzip archive.zip                 # Extrait une archive zip
unzip -l archive.zip             # Liste le contenu sans extraire
```

### gzip et gunzip
```bash
gzip fichier                      # Compresse un fichier (créé .gz)
gunzip fichier.gz                 # Décompresse un fichier .gz
gzip -d fichier.gz               # Alternative pour décompresser
```

## Téléchargement et Transfert

### wget (téléchargement web)
```bash
wget URL                          # Télécharge un fichier
wget -c URL                       # Reprend un téléchargement interrompu
wget -O nom_fichier URL          # Télécharge avec un nom spécifique
wget --limit-rate=1m URL         # Limite la vitesse à 1MB/s
wget -b URL                      # Télécharge en arrière-plan
```

### curl (transfert de données)
```bash
curl -O URL                       # Télécharge un fichier
curl -o nom_fichier URL          # Télécharge avec un nom spécifique
curl -C - URL                    # Reprend un téléchargement
curl -L URL                      # Suit les redirections
```

### scp (copie sécurisée)
```bash
# Copie locale vers distant
scp fichier.txt user@serveur:/chemin/destination/

# Copie distant vers local
scp user@serveur:/chemin/fichier.txt /chemin/local/

# Copie récursive (dossier)
scp -r dossier/ user@serveur:/chemin/destination/
```

### rsync (synchronisation)
```bash
rsync -av source/ destination/    # Synchronise avec archive et verbose
rsync -avz --progress source/ user@serveur:/destination/  # Avec compression
```

## Gestion des Processus

### Surveillance des processus
```bash
top                              # Affiche les processus en temps réel
htop                             # Version améliorée de top
ps aux                          # Liste tous les processus
ps aux | grep nom_process       # Recherche un processus spécifique
```

### Gestion des tâches
```bash
commande &                       # Exécute en arrière-plan
Ctrl + Z                        # Suspend le processus actuel
bg                              # Passe le processus en arrière-plan
fg                              # Ramène le processus au premier plan
jobs                           # Liste les tâches en cours
kill PID                        # Termine un processus
killall nom_processus          # Termine tous les processus du nom
```

### Priorité des processus
```bash
nice -n 10 commande             # Lance avec priorité modifiée
renice +10 PID                  # Modifie la priorité d'un processus
```

## Recherche de Fichiers

### find (recherche approfondie)
```bash
find . -name "*.txt"            # Recherche par nom
find . -type f -size +100M      # Recherche par taille (>100MB)
find . -mtime -7                # Fichiers modifiés ces 7 derniers jours
find . -type f -exec chmod 644 {} \;  # Exécute une commande sur résultats
```

### locate (recherche rapide)
```bash
locate fichier.txt              # Recherche dans la base de données
updatedb                        # Met à jour la base de données locate
```

## Gestion des Disques et du Stockage

### Espace disque
```bash
df -h                           # Affiche l'espace disque
du -sh *                        # Taille des fichiers/dossiers
du -h --max-depth=1            # Taille avec profondeur limitée
ncdu                           # Navigateur d'utilisation disque
```

### Montage de systèmes de fichiers
```bash
mount                           # Liste les systèmes montés
mount /dev/sdb1 /mnt/usb       # Monte une partition
umount /mnt/usb                # Démonte une partition
```

### Gestion des services
```bash
systemctl start service         # Démarre un service
systemctl stop service         # Arrête un service
systemctl restart service      # Redémarre un service
systemctl status service       # État d'un service
systemctl enable service       # Active au démarrage
systemctl disable service      # Désactive au démarrage
```

## Astuces et Personnalisation

### Variables d'environnement
```bash
echo $PATH                      # Affiche le PATH
export VAR="valeur"            # Définit une variable
echo $VAR                      # Affiche une variable
```

### Redirections et Pipes
```bash
commande > fichier             # Redirige la sortie (écrase)
commande >> fichier           # Redirige la sortie (ajoute)
commande 2> erreurs.log       # Redirige les erreurs
commande1 | commande2         # Pipe (chaîne les commandes)
```

### Gestion des paquets (Debian/Ubuntu)
```bash
apt update                     # Met à jour la liste des paquets
apt upgrade                    # Met à jour les paquets installés
apt install paquet            # Installe un paquet
apt remove paquet             # Désinstalle un paquet
apt search terme              # Recherche un paquet
```

> **Note :** Pour les systèmes RPM (Fedora/RHEL), remplacez `apt` par `dnf`.