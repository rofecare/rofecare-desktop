# Premiers pas avec Rofecare Desktop

Ce guide vous accompagne lors de votre première utilisation de Rofecare Desktop.

## Prérequis

- Rofecare Desktop installé (voir [Installation](./installation.md))
- Un compte utilisateur Rofecare valide (créé par votre administrateur)
- Une **connexion internet au premier démarrage** (obligatoire)

## Compte utilisateur

Votre compte Rofecare a été créé par l'administrateur de votre hôpital via l'interface web `rofecare.com`. Vous avez dû recevoir :

- Votre **adresse email** (identifiant)
- Un **mot de passe temporaire**
- Un lien pour le changer (optionnel)

Si vous n'avez pas reçu ces informations, contactez votre administrateur.

## Premier lancement

### Étape 1 — Lancement de l'application

Ouvrir Rofecare depuis :
- **macOS** : Applications → Rofecare, ou Spotlight
- **Linux** : menu Applications → Rofecare, ou commande `rofecare`
- **Windows** : Menu Démarrer → Rofecare

Au premier lancement, un écran de chargement s'affiche pendant que :
- La base de données locale PostgreSQL s'initialise
- Le serveur Spring Boot local démarre
- Le hub de synchronisation Mercure démarre

**Durée** : environ 30 à 60 secondes.

### Étape 2 — Écran de connexion

Après le chargement, l'écran de login apparaît :

```
┌────────────────────────────────────┐
│          Rofecare                  │
│     Connexion à votre compte       │
│                                    │
│  Email : [____________________]    │
│  Mot de passe : [______________]   │
│                                    │
│          [Se connecter]            │
│                                    │
│  ℹ Connexion internet requise      │
│     au premier login               │
└────────────────────────────────────┘
```

### Étape 3 — Authentification

Saisissez vos identifiants et cliquez sur **Se connecter**.

**Au premier login**, l'application contacte `api.rofecare.com` pour :

1. Vérifier vos identifiants
2. Identifier votre group (hôpital)
3. Télécharger la clé de chiffrement de votre group
4. Vous "attacher" à ce poste de travail

> ⚠ **Si vous n'avez pas de connexion internet au premier login**, l'application refusera de vous connecter avec le message : "Première connexion nécessite internet". C'est une mesure de sécurité.

### Étape 4 — Import des données initiales

Si vous êtes :

**Le premier utilisateur de votre hôpital à installer Rofecare Desktop**
- L'application vous propose d'importer les données depuis le cloud
- Durée variable selon la taille : de quelques secondes à plusieurs minutes
- Vous pouvez choisir de tout importer, ou seulement une période (ex. les 6 derniers mois)

**Un autre utilisateur de votre hôpital l'a déjà installé**
- L'application détecte automatiquement les autres postes sur votre réseau local
- Une fenêtre vous propose : "Importer depuis [poste-collègue] (rapide) ou depuis le cloud (plus lent) ?"
- Choisir **depuis le poste collègue** est plus rapide (LAN)

### Étape 5 — Fenêtre principale

Une fois la configuration terminée, vous arrivez sur l'écran principal de Rofecare avec les menus de votre rôle.

## Travail quotidien

### Connexion offline

Après le premier login, vous pouvez vous connecter **sans internet**. Rofecare utilise vos identifiants en cache localement.

### Synchronisation automatique

L'application synchronise automatiquement :

- **Avec le cloud** : toutes les 5 minutes si internet disponible
- **Entre postes de la clinique** : en temps réel via le mesh LAN Mercure

Vous n'avez rien à faire, c'est transparent.

### Forcer une synchronisation

Vous pouvez forcer une sync manuellement :

- Menu **Aide** → **Synchroniser maintenant**
- Ou raccourci : `Cmd/Ctrl + Maj + S`

## Découverte automatique des postes

Quand vous êtes connecté au réseau WiFi ou LAN de votre clinique, Rofecare détecte automatiquement les autres postes qui tournent.

**Indicateur visuel** : en bas à droite de la fenêtre, une icône indique combien de collègues sont connectés au mesh :

```
👥 4 postes connectés
```

Cliquer sur l'icône pour voir la liste des postes.

## Contrainte des 7 jours

**Important** : Rofecare impose une **synchronisation cloud au moins une fois tous les 7 jours**.

Si vous n'avez pas de connexion internet pendant plus de 7 jours, Rofecare passe en **mode lecture seule** :

- Vous pouvez toujours consulter les données
- Mais vous ne pouvez plus créer/modifier
- Jusqu'à ce que vous vous reconnectiez à internet et que l'app synchronise

Cette mesure protège contre la dérive des données entre postes déconnectés.

## Que faire si...

- **J'ai oublié mon mot de passe** → demander à votre admin de le réinitialiser via `rofecare.com`
- **Mon compte est désactivé** → voir votre admin
- **L'app ne trouve pas les autres postes** → voir [Dépannage](./troubleshooting.md)
- **La sync cloud échoue** → vérifier votre connexion internet, voir [Dépannage](./troubleshooting.md)

## Prochaines étapes

- [Guide de synchronisation](./sync.md) pour comprendre comment fonctionnent les sync LAN + cloud
- [Configuration LAN](./lan-setup.md) pour déployer plusieurs postes dans une clinique
- [FAQ](./faq.md) pour les questions fréquentes
