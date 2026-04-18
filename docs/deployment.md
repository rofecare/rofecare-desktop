# Guide de déploiement en clinique

Ce guide s'adresse aux responsables IT ou administrateurs qui déploient Rofecare Desktop pour la première fois dans une clinique ou un hôpital.

## Vue d'ensemble du déploiement

Un déploiement Rofecare Desktop typique suit ces étapes :

```
Phase 1 : Préparation cloud (1h)
    └─► Créer le group Rofecare
    └─► Créer les comptes utilisateurs
    └─► Configurer les rôles et permissions

Phase 2 : Préparation réseau (2h)
    └─► Vérifier le WiFi / LAN
    └─► Configurer les firewalls
    └─► Tester la connectivité

Phase 3 : Installation des postes (1-4h)
    └─► Poste principal (mini-PC ou PC admin)
    └─► Postes secondaires (médecins, infirmiers, accueil)
    └─► Vérification du mesh

Phase 4 : Formation (1-3j)
    └─► Former le personnel
    └─► Saisir les données initiales
    └─► Période de rodage

Phase 5 : Production (ongoing)
    └─► Support utilisateur
    └─► Monitoring de la sync
```

## Phase 1 — Préparation cloud

### Créer le group Rofecare

Contacter l'équipe Rofecare (`sales@rofecare.com`) pour créer votre group (hôpital) dans le cloud. Vous recevrez :

- **URL du group** : ex. `https://app.rofecare.com` (commun à tous)
- **Identifiant admin** : email + mot de passe temporaire
- **Group ID** : UUID unique de votre hôpital (vous n'avez pas à le retenir)

### Créer les utilisateurs

Se connecter à `app.rofecare.com` en tant qu'admin et créer les comptes :

```
Administration → Utilisateurs → Nouveau

Pour chaque employé :
- Email (utilisé comme identifiant)
- Nom complet
- Rôle (médecin, infirmier, pharmacien, admin, etc.)
- Permissions spécifiques
- Envoyer l'email de bienvenue (automatique)
```

Les utilisateurs reçoivent un email avec leurs identifiants et un lien pour changer leur mot de passe.

### Configurer les rôles

Rofecare propose des rôles prédéfinis que vous pouvez personnaliser :

- **ROLE_ADMIN** : tout accès
- **ROLE_DOCTOR** : dossiers médicaux, prescriptions, diagnostics
- **ROLE_NURSE** : soins, observations, dossiers patients
- **ROLE_PHARMACIST** : pharmacie, dispensation
- **ROLE_RECEPTIONIST** : accueil, admissions, RDV
- **ROLE_ACCOUNTANT** : facturation, paiements, rapports financiers
- **ROLE_LAB** : résultats de laboratoire
- **ROLE_READONLY** : consultation uniquement

Personnaliser dans **Administration** → **Rôles**.

## Phase 2 — Préparation réseau

### Tester le réseau clinique

Avant d'installer, vérifier :

```bash
# Depuis n'importe quel poste de la clinique
ping api.rofecare.com           # doit répondre
curl -s https://api.rofecare.com/ping  # doit retourner {"status": "ok"}
```

Si échec : vérifier la connexion internet, le DNS, le firewall.

### Configurer le WiFi clinique

Dans les paramètres du routeur :

- **WPA2** ou **WPA3** obligatoire
- **AP Isolation / Client Isolation** : **DÉSACTIVER** (crucial)
- **Bande** : 5 GHz recommandé pour moins d'interférences
- **Canal** : automatique ou fixe si vous connaissez l'environnement

### Configurer les firewalls des postes

**Autoriser Rofecare Desktop** dans le pare-feu de chaque poste :

- **macOS** : Préférences Système → Sécurité → Pare-feu → Options → Autoriser Rofecare
- **Windows** : Pare-feu Windows Defender → Applications autorisées → Ajouter Rofecare
- **Linux** : si `ufw` ou `firewalld` actif, autoriser les ports 5353/UDP, 3001/TCP, 8080/TCP en LAN

### Vérifier que mDNS fonctionne

Sur deux postes test :

- **Test découverte**
  ```bash
  # macOS / Linux (avghadi)
  avahi-browse -a                    # Linux
  dns-sd -B _services._dns-sd._udp   # macOS
  ```
- Les postes doivent se voir mutuellement

## Phase 3 — Installation des postes

### Choisir le poste principal

Le **poste principal** est celui qui servira de référence pour les autres. Idéalement :

- **Mini-PC ou NUC dédié** (toujours allumé, pas utilisé par une personne spécifique)
- **8+ GB RAM**
- **SSD** de 256 GB minimum
- **Connecté en Ethernet** (plus stable que WiFi)
- **Chiffrement disque** activé

Si pas de mini-PC dédié : choisir le poste de l'administration ou de la réception qui est allumé en continu.

### Installer le poste principal

1. Télécharger Rofecare Desktop depuis https://github.com/rofecare/rofecare-desktop/releases/latest
2. Installer selon le guide [Installation](./installation.md)
3. Au premier lancement, se connecter avec le compte **admin du group**
4. Wizard : choisir **"Group vide (nouveau)"**
5. Attendre l'initialisation (~2 min)
6. Application prête

### Installer les postes secondaires

Pour chaque poste supplémentaire :

1. Installer Rofecare Desktop
2. Premier lancement → login avec le compte de l'utilisateur (médecin, infirmier, etc.)
3. Wizard : l'application détecte le poste principal sur le LAN
4. Choisir **"Importer depuis [poste principal]"**
5. Transfer LAN, quelques minutes
6. Application prête

### Valider le mesh

Sur tous les postes, vérifier :

- Indicateur mesh en bas à droite : `👥 X postes connectés` (X = nombre total de postes - 1)
- Créer un patient test sur un poste
- Vérifier qu'il apparaît sur les autres postes en < 5 secondes

## Phase 4 — Formation

### Sessions de formation recommandées

**Session 1 — Bases (1h)** pour tout le personnel
- Login / logout
- Rechercher un patient
- Créer un nouveau patient
- Naviguer dans les menus

**Session 2 — Rôle spécifique (1h par rôle)**
- Médecins : consultation, prescription, diagnostic
- Infirmiers : soins, observations, admission
- Accueil : RDV, admissions, facturation

**Session 3 — Spécificités desktop (30 min)** pour tous
- Comprendre la sync LAN + cloud
- Que faire quand l'indicateur passe au rouge
- Comment importer des données anciennes depuis le cloud

### Documentation à distribuer

- [Premiers pas](./getting-started.md) à chaque utilisateur
- [FAQ](./faq.md) comme référence
- [Dépannage](./troubleshooting.md) pour l'IT support

## Phase 5 — Production et monitoring

### Monitoring recommandé

Depuis l'app cloud `app.rofecare.com` → **Administration** → **Diagnostics** :

- Nombre de postes connectés par jour
- Dernière sync cloud de chaque poste
- Conflits non résolus
- Erreurs remontées

### Surveillance de la sync

Sur le poste principal, vérifier quotidiennement :

- Menu **Aide** → **Diagnostics** → **Dernière sync cloud** : doit être < 1 heure
- Si > 4 heures : vérifier la connexion internet

### Plan de reprise d'activité (PRA)

Rofecare Desktop intègre automatiquement plusieurs couches de résilience :

1. **Mesh LAN** : même si le poste principal tombe, les autres continuent
2. **Sync cloud** : les données sont répliquées dans le cloud en permanence
3. **Export local** : chaque poste peut exporter sa base via Menu → Paramètres → Exporter
4. **Re-import depuis cloud** : en cas de panne totale, réinstaller et importer depuis le cloud restore toutes les données

### Mises à jour

Les mises à jour sont **automatiques** :
- Vérification au démarrage + toutes les 4h
- Téléchargement en arrière-plan
- Dialog qui demande confirmation
- Installation au prochain redémarrage de l'app

Vous pouvez reporter une mise à jour sans la perdre.

### Support

- **Support technique** : support@rofecare.com
- **Urgence** : [numéro à définir selon votre contrat]
- **Issues GitHub** : https://github.com/rofecare/rofecare-desktop/issues

## Check-list de déploiement

- [ ] Group cloud créé
- [ ] Comptes utilisateurs créés avec rôles
- [ ] WiFi clinique : AP Isolation désactivé, WPA2+ activé
- [ ] Pare-feu des postes : Rofecare autorisé
- [ ] Tests mDNS LAN OK
- [ ] Poste principal installé et opérationnel
- [ ] Postes secondaires installés et connectés au mesh
- [ ] Vérification temps réel mesh LAN OK
- [ ] Formation utilisateurs effectuée
- [ ] Monitoring configuré
- [ ] Documentation distribuée
- [ ] Processus de support en place

## Migration depuis un système existant

Si vous migrez depuis une autre solution de gestion hospitalière, contacter `migration@rofecare.com` pour un accompagnement personnalisé. Nous proposons :

- Analyse des données existantes
- Scripts d'import personnalisés
- Migration progressive (parallel running)
- Formation utilisateurs renforcée
