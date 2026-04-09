# 🔐 Sécurisation d’un VPS (Checklist simple)

## 1. Mettre à jour le système

**Utilité :** Corriger les failles de sécurité connues

**Exemples :** APT, YUM, unattended-upgrades

---

## 2. Créer un utilisateur sécurisé

**Utilité :** Éviter l’utilisation du compte root

**Exemples :** User Linux avec sudo

---

## 3. Désactiver les accès dangereux

**Utilité :** Réduire la surface d’attaque

**Exemples :** PermitRootLogin no, PasswordAuthentication no, services inutiles

---

## 4. Changer le port SSH par défaut

**Utilité :** Réduire le bruit dans les logs et éviter les bots basiques

**Exemples :** Port 2222 ou port élevé (>1024)

---

## 5. Utiliser une authentification forte

**Utilité :** Sécuriser l’accès au serveur

**Exemples :** Clés SSH, 2FA

---

## 6. Configurer un firewall

**Utilité :** Contrôler les accès réseau

**Exemples :** UFW, iptables

---

## 7. Bloquer les attaques automatiques

**Utilité :** Empêcher les tentatives de brute-force

**Exemples :** Fail2Ban, CrowdSec

---

## 8. Limiter les tentatives de connexion PAM

**Utilité :** Verrouiller les comptes localement après plusieurs échecs

**Exemples :** faillock, pam_tally2

---

## 9. Sécuriser le trafic web

**Utilité :** Protéger les données échangées

**Exemples :** HTTPS, Certbot, Let’s Encrypt

---

## 10. Configurer un serveur web sécurisé

**Utilité :** Éviter les mauvaises configurations

**Exemples :** Nginx, Apache

---

## 11. Surveiller les activités

**Utilité :** Détecter les comportements suspects

**Exemples :** Logs système, GoAccess

---

## 12. Automatiser les mises à jour

**Utilité :** Prévenir les nouvelles vulnérabilités sans intervention manuelle

**Exemples :** unattended-upgrades, dnf-automatic

---

## 13. Scanner les vulnérabilités

**Utilité :** Identifier les failles

**Exemples :** Lynis, OpenVAS

---

## 14. Protéger les applications web

**Utilité :** Bloquer les attaques web (XSS, SQLi…)

**Exemples :** Cloudflare, WAF

---

## 15. Gérer les permissions

**Utilité :** Limiter les accès aux fichiers sensibles

**Exemples :** Permissions Linux, .env sécurisé

---

## 16. Mettre en place des sauvegardes

**Utilité :** Éviter la perte de données

**Exemples :** Snapshots, rsync, Règle 3-2-1 (3 copies, 2 supports, 1 distant)

---

## 17. Ajouter une sécurité avancée

**Utilité :** Renforcer la protection globale

**Exemples :** WireGuard, Port Knocking, VPN/Bastion, auditd, IDS/IPS
