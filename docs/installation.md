# Installation de Rofecare Desktop

Ce guide vous accompagne pour installer Rofecare Desktop sur votre système.

## Configuration requise

Avant d'installer, vérifiez que votre machine répond aux prérequis :

### Tous systèmes
- **4 GB RAM** minimum, 8 GB recommandé
- **2 GB d'espace disque libre**
- **Chiffrement du disque système activé** (obligatoire pour des raisons de sécurité médicale)

### macOS
- macOS 11 Big Sur ou supérieur
- Intel x86_64 ou Apple Silicon (ARM64)
- **FileVault activé** — Préférences Système → Sécurité et confidentialité → FileVault

### Linux
- Ubuntu 22.04+, Debian 12+, Fedora 38+ ou distribution équivalente
- WebKit2GTK 4.1 (`libwebkit2gtk-4.1-0`)
- **LUKS ou équivalent** activé sur le disque système

### Windows
- Windows 10 version 1809 ou supérieur, Windows 11
- **BitLocker activé**
- WebView2 Runtime (installé automatiquement par l'installeur si absent)

## Télécharger

Les binaires officiels sont disponibles dans les [Releases GitHub](https://github.com/rofecare/rofecare-desktop/releases/latest).

## Installation par plateforme

### macOS

1. Télécharger **Rofecare_<version>_universal.dmg**
2. Ouvrir le fichier DMG
3. Glisser **Rofecare.app** dans le dossier **Applications**
4. Lancer depuis Applications ou Spotlight
5. **Premier lancement** : si macOS affiche "Rofecare ne peut pas être ouverte car le développeur ne peut pas être vérifié" :
   - Ouvrir le **Terminal**
   - Exécuter : `xattr -dr com.apple.quarantine /Applications/Rofecare.app`
   - Relancer Rofecare

> **Note** : nous travaillons à obtenir une signature Apple Developer pour supprimer cet avertissement.

### Linux

#### AppImage (recommandé, fonctionne partout)

```bash
wget https://github.com/rofecare/rofecare-desktop/releases/latest/download/rofecare_0.3.0_amd64.AppImage
chmod +x rofecare_0.3.0_amd64.AppImage
./rofecare_0.3.0_amd64.AppImage
```

Pour intégration au menu système, utiliser [AppImageLauncher](https://github.com/TheAssassin/AppImageLauncher).

#### Debian / Ubuntu (.deb)

```bash
wget https://github.com/rofecare/rofecare-desktop/releases/latest/download/rofecare_0.3.0_amd64.deb
sudo dpkg -i rofecare_0.3.0_amd64.deb
sudo apt-get install -f    # pour résoudre les dépendances manquantes éventuelles
```

Lancer depuis le menu Applications ou via `rofecare` en terminal.

#### Fedora / RHEL (.rpm)

```bash
wget https://github.com/rofecare/rofecare-desktop/releases/latest/download/rofecare-0.3.0-1.x86_64.rpm
sudo rpm -i rofecare-0.3.0-1.x86_64.rpm
```

### Windows

#### Installeur NSIS (recommandé)

1. Télécharger **Rofecare_<version>_x64-setup.exe**
2. Double-cliquer pour lancer l'installeur
3. Si Windows SmartScreen affiche un avertissement : cliquer **Informations complémentaires** → **Exécuter quand même**
4. Suivre les étapes de l'installeur
5. Rofecare est accessible depuis le Menu Démarrer

#### Package MSI (pour déploiement par IT)

```powershell
msiexec /i Rofecare_0.3.0_x64_en-US.msi /qn
```

## Vérification de l'installation

Au premier lancement, Rofecare va :

1. Vérifier que le chiffrement disque est activé
2. Initialiser la base de données locale PostgreSQL (~30 secondes)
3. Démarrer le serveur Spring Boot local
4. Démarrer le hub Mercure de synchronisation
5. Afficher l'écran de login

**Écran de login attendu** : logo Rofecare + champs email/mot de passe.

## Premier démarrage

Consultez le guide **[Premiers pas](./getting-started.md)** pour la configuration initiale.

## Problèmes courants

Voir le guide de **[Dépannage](./troubleshooting.md)**.

## Désinstallation

### macOS
1. Glisser `Rofecare.app` depuis `/Applications` vers la Corbeille
2. Supprimer les données : `rm -rf ~/Library/Application\ Support/com.rofecare.desktop/`

### Linux (AppImage)
Supprimer simplement le fichier `.AppImage` et les données : `rm -rf ~/.local/share/rofecare/`

### Linux (.deb)
```bash
sudo apt remove rofecare
rm -rf ~/.local/share/rofecare/
```

### Windows
1. Panneau de configuration → Programmes → Désinstaller Rofecare
2. Supprimer `%APPDATA%\Rofecare\` pour les données

> ⚠ **Attention** : supprimer les données efface votre copie locale. Assurez-vous que tout a été synchronisé avec le cloud au préalable.
