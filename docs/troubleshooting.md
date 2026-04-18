# Dépannage Rofecare Desktop

Solutions aux problèmes courants rencontrés avec Rofecare Desktop.

## L'application ne démarre pas

### macOS : "Rofecare est endommagée et ne peut pas être ouverte"

Ce message vient de Gatekeeper. Solution :

```bash
xattr -dr com.apple.quarantine /Applications/Rofecare.app
```

Puis relancer.

### Windows : SmartScreen bloque l'exécution

Cliquer sur **Informations complémentaires** puis **Exécuter quand même**. L'avertissement ne réapparaîtra plus pour cette version.

### Linux : erreur `libwebkit2gtk-4.1.so.0`

Installer la dépendance :

```bash
# Debian/Ubuntu
sudo apt install libwebkit2gtk-4.1-0

# Fedora
sudo dnf install webkit2gtk4.1
```

Alternative : utiliser l'**AppImage** qui embarque toutes les dépendances.

### "Chiffrement disque non activé"

Rofecare refuse de démarrer si le disque système n'est pas chiffré. Activez :

- **macOS** : Préférences Système → Sécurité et confidentialité → FileVault → Activer
- **Windows** : Paramètres → Mise à jour et sécurité → Chiffrement de l'appareil (ou BitLocker)
- **Linux** : réinstaller le système avec LUKS activé, ou chiffrer le home avec `ecryptfs`

## Problèmes de connexion

### "Première connexion nécessite internet"

Le premier login sur un appareil nécessite obligatoirement une connexion internet, pour contacter `api.rofecare.com` et récupérer votre clé de group.

**Solution** : se connecter à internet (même temporairement) pour le premier login, puis vous pourrez travailler offline.

### "Identifiants invalides" alors que je suis sûr

- Vérifier la casse du mot de passe
- Vérifier qu'il n'y a pas d'espaces superflus
- Tester sur `app.rofecare.com` avec les mêmes identifiants
- Si OK sur web mais pas sur desktop : contacter le support

### "Compte désactivé"

Votre administrateur a révoqué votre accès. Contactez-le.

## Synchronisation

### "Synchronisation cloud échoue"

Vérifier :
1. Connexion internet (`ping api.rofecare.com`)
2. Pas de proxy/firewall bloquant HTTPS vers `api.rofecare.com`
3. Date/heure système correcte (les tokens JWT dépendent de l'heure)

### Mode lecture seule après 7 jours

Si vous voyez un bandeau rouge "Synchronisation cloud obligatoire" :

- Vous n'avez pas eu de sync cloud depuis plus de 7 jours
- Connectez-vous à internet
- La sync se fera automatiquement
- Après succès, le mode lecture-écriture revient

### Les autres postes ne sont pas détectés

Dans une clinique avec plusieurs postes :

1. Vérifier que tous les postes sont sur le **même réseau local** (même WiFi ou LAN filaire)
2. Vérifier que mDNS/Bonjour n'est pas bloqué par le firewall
3. Vérifier que tous les postes utilisent le **même group** (le même hôpital)
4. Redémarrer Rofecare sur les postes concernés

**Test de connectivité mDNS** :

- **macOS / Linux** : `dns-sd -B _rofecare-mercure._tcp local`
- **Windows** : utiliser **Bonjour Browser**

## Base de données locale

### "Erreur connexion PostgreSQL"

1. Fermer Rofecare
2. Vérifier qu'aucun autre PostgreSQL ne tourne sur le port 5432
3. Vérifier les logs : `<data_dir>/postgres/log/postgresql.log`
4. Si corruption suspectée : sauvegarder le dossier data et contacter le support

**Emplacement data par OS** :
- macOS : `~/Library/Application Support/com.rofecare.desktop/postgres/`
- Linux : `~/.local/share/rofecare/postgres/`
- Windows : `%APPDATA%\Rofecare\postgres\`

### Espace disque insuffisant

Rofecare purge automatiquement les données > 90 jours déjà synchronisées avec le cloud.

Pour forcer une purge manuelle :
- Menu **Paramètres** → **Stockage** → **Purger les anciennes données**

Pour modifier la durée de rétention :
- Menu **Paramètres** → **Stockage** → **Conserver** → choisir 30/60/90/180 jours ou "Jamais"

## Performance

### Démarrage lent

Premier démarrage : ~60 secondes (normal, initialisation DB + sidecars).
Démarrages suivants : ~15-20 secondes.

Si plus lent :
- Vérifier l'espace disque libre
- Vérifier les logs `<data_dir>/logs/rofecare-server.log`
- Redémarrer la machine

### L'app consomme beaucoup de RAM

Rofecare utilise ~500 MB à ~1 GB de RAM (PostgreSQL + JVM Spring Boot + WebView). C'est normal pour un monolithe embarqué.

Si vous êtes sur une machine avec < 8 GB RAM, fermez les autres applications.

## Logs et diagnostics

### Où sont les logs ?

- **macOS** : `~/Library/Application Support/com.rofecare.desktop/logs/`
- **Linux** : `~/.local/share/rofecare/logs/`
- **Windows** : `%APPDATA%\Rofecare\logs\`

Fichiers principaux :
- `rofecare-server.log` — serveur Spring Boot
- `postgres.log` — base de données
- `mercure.log` — hub de synchronisation

### Page de diagnostics intégrée

Menu **Aide** → **Diagnostics** affiche l'état en temps réel :

- Statut des sidecars (PostgreSQL, Spring Boot, Mercure)
- Liste des peers découverts sur le LAN
- Dernière synchronisation cloud
- Nombre de changements en attente de sync
- Drift days (jours depuis dernière sync cloud)

## Envoyer un rapport de bug

Si votre problème persiste après avoir suivi ce guide :

1. Ouvrir une issue : https://github.com/rofecare/rofecare-desktop/issues/new/choose
2. Joindre :
   - Version Rofecare (menu Aide → À propos)
   - OS + version
   - Logs pertinents (`rofecare-server.log` des 10 dernières minutes)
   - Étapes de reproduction

Pour les problèmes urgents : support@rofecare.com
