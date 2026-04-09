# Rofecare Desktop

Application desktop officielle de **Rofecare** — système de gestion hospitalière multi-spécialités conçu pour l'Afrique centrale.

## ✨ Caractéristiques

- **Fonctionnement hors-ligne** — base de données et serveur embarqués, aucune connexion internet requise en utilisation quotidienne
- **Synchronisation automatique** — sync avec le cloud Rofecare dès qu'une connexion est détectée
- **Mesh local** — plusieurs postes d'une même clinique partagent leurs données en temps réel via le réseau local
- **Sécurité** — chiffrement des données, isolation multi-tenant, authentification cloud
- **Multi-plateforme** — macOS, Linux, Windows
- **Mises à jour automatiques** — signées cryptographiquement

## 📥 Télécharger

| Plateforme | Format | Lien |
|------------|--------|------|
| 🍎 **macOS** (Intel + Apple Silicon) | DMG | [Rofecare.dmg](https://github.com/rofecare/rofecare-desktop/releases/latest) |
| 🐧 **Linux** (Ubuntu, Debian) | AppImage | [Rofecare.AppImage](https://github.com/rofecare/rofecare-desktop/releases/latest) |
| 🐧 **Linux** (Debian/Ubuntu) | .deb | [rofecare.deb](https://github.com/rofecare/rofecare-desktop/releases/latest) |
| 🐧 **Linux** (Fedora/RHEL) | .rpm | [rofecare.rpm](https://github.com/rofecare/rofecare-desktop/releases/latest) |
| 🪟 **Windows 10/11** | Installeur | [Rofecare-setup.exe](https://github.com/rofecare/rofecare-desktop/releases/latest) |
| 🪟 **Windows 10/11** | MSI | [Rofecare.msi](https://github.com/rofecare/rofecare-desktop/releases/latest) |

> **Taille** : environ 250-300 MB par installeur (embarque PostgreSQL, JRE 21, serveur Spring Boot et hub de synchronisation Mercure).

Consultez toutes les versions : [**Releases**](https://github.com/rofecare/rofecare-desktop/releases)

## 📚 Documentation

- **[Installation](./docs/installation.md)** — Comment installer Rofecare Desktop sur votre système
- **[Premiers pas](./docs/getting-started.md)** — Configuration initiale, premier login, import des données
- **[Réseau local](./docs/lan-setup.md)** — Comment partager une base de données entre plusieurs postes de la clinique
- **[Synchronisation](./docs/sync.md)** — Comment fonctionne la sync cloud + mesh LAN
- **[Dépannage](./docs/troubleshooting.md)** — Problèmes courants et solutions
- **[FAQ](./docs/faq.md)** — Questions fréquentes

Toute la documentation est également disponible dans le [Wiki](../../wiki).

## 🔒 Sécurité

Rofecare Desktop suit des pratiques de sécurité strictes pour protéger les données médicales :

- Chiffrement obligatoire du disque système (FileVault, BitLocker, LUKS)
- Authentification centralisée via le cloud Rofecare
- Isolation stricte multi-tenant (cloison entre hôpitaux)
- Tokens JWT à durée limitée
- Signature cryptographique des mises à jour (Minisign)

Pour signaler une vulnérabilité : [security@rofecare.com](mailto:security@rofecare.com)

## 🐛 Signaler un bug

Merci de signaler les bugs rencontrés :

1. Consultez d'abord le [Dépannage](./docs/troubleshooting.md) et les [issues existantes](../../issues)
2. Si votre problème n'y figure pas : [ouvrir une nouvelle issue](../../issues/new/choose)
3. Précisez : version Rofecare, OS, étapes de reproduction, logs si disponibles

## 💡 Proposer une fonctionnalité

Les suggestions d'amélioration sont bienvenues : [nouvelle feature request](../../issues/new?template=feature_request.md).

## 🔄 Mises à jour automatiques

Rofecare Desktop vérifie automatiquement les mises à jour au démarrage et toutes les 4 heures. Les mises à jour sont signées cryptographiquement — impossible d'installer une mise à jour corrompue ou altérée.

## 🤝 Déploiement en clinique

Pour un déploiement dans une clinique :

1. **Un hôpital = un tenant** dans Rofecare cloud
2. Créer les comptes utilisateurs via l'interface admin sur `rofecare.com`
3. Installer Rofecare Desktop sur chaque poste de la clinique
4. Le **premier démarrage de chaque poste nécessite une connexion internet** (pour attacher l'utilisateur au tenant)
5. Ensuite, les postes se découvrent automatiquement sur le LAN de la clinique et partagent leurs données

Voir le [guide de déploiement](./docs/deployment.md) pour plus de détails.

## 📋 Configuration requise

### macOS
- macOS 11 (Big Sur) ou supérieur
- 4 GB RAM minimum, 8 GB recommandé
- 2 GB d'espace disque libre
- **FileVault activé** (obligatoire)

### Linux
- Ubuntu 22.04+, Debian 12+, Fedora 38+ ou équivalent
- WebKit2GTK 4.1
- 4 GB RAM minimum, 8 GB recommandé
- 2 GB d'espace disque libre
- **LUKS ou équivalent activé** (obligatoire)

### Windows
- Windows 10 version 1809 ou supérieur, Windows 11
- WebView2 Runtime (installé automatiquement si absent)
- 4 GB RAM minimum, 8 GB recommandé
- 2 GB d'espace disque libre
- **BitLocker activé** (obligatoire)

## 🌍 Langues

Rofecare supporte 13 langues dont français, anglais, portugais, swahili, lingala, et d'autres langues africaines.

## 📜 Licence

- **Documentation, supports, issue templates** : [MIT License](./LICENSE)
- **Code source de l'application** : Propriétaire — © 2026 Rofecare. Tous droits réservés.

Le code source de l'application desktop n'est pas public. Ce dépôt contient uniquement les **ressources de distribution** (binaires, documentation, issues).

## 🔗 Liens

- **Site officiel** : https://rofecare.com
- **Application web** : https://app.rofecare.com
- **Documentation technique** : https://docs.rofecare.com
- **Support** : support@rofecare.com
- **Sécurité** : security@rofecare.com

---

**Made with ❤️ in Kinshasa, for Africa.**
