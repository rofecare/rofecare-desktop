# FAQ — Rofecare Desktop

## Général

### Qu'est-ce que Rofecare Desktop ?

Rofecare Desktop est l'application de bureau officielle du système hospitalier Rofecare. Elle permet aux cliniques et hôpitaux de gérer leurs patients, prescriptions, facturation, etc., **même sans connexion internet**.

### Quelle différence avec l'application web `app.rofecare.com` ?

| | **Application web** | **Rofecare Desktop** |
|---|---|---|
| Connexion internet | Obligatoire | Optionnelle (sauf premier login) |
| Multi-utilisateurs LAN | Via le cloud | En direct via réseau local |
| Données locales | Non | Oui |
| Installation | Aucune | Installeur à télécharger |
| Mises à jour | Automatiques | Automatiques (auto-updater) |
| Performance | Dépend d'internet | Instantanée (local) |
| Usage recommandé | Bureaux avec internet stable | Cliniques rurales / connectivité instable |

**Les deux applications synchronisent les mêmes données.** Vous pouvez travailler sur l'une et continuer sur l'autre.

### Puis-je utiliser Rofecare Desktop sans compte ?

Non. Rofecare Desktop est réservé aux utilisateurs enregistrés dans un tenant Rofecare (hôpital ou clinique sous contrat).

### Combien coûte Rofecare Desktop ?

L'application est **gratuite** pour les cliniques et hôpitaux abonnés à Rofecare cloud. Le coût est inclus dans votre abonnement Rofecare.

## Installation et configuration

### Sur combien de postes puis-je installer Rofecare Desktop ?

Il n'y a **pas de limite technique**. Vous pouvez installer Rofecare Desktop sur autant de postes que nécessaire pour votre clinique.

### Faut-il un serveur dédié dans la clinique ?

**Non.** Chaque poste Rofecare Desktop est autonome — il embarque sa propre base de données, son propre serveur, son propre hub de synchronisation. Les postes collaborent automatiquement via le réseau local de la clinique sans serveur central.

### Est-ce que je peux installer Rofecare Desktop sur un Raspberry Pi ou un mini-PC ?

Techniquement oui, mais ce n'est pas recommandé pour une utilisation intensive. Nous recommandons un poste avec au minimum 4 GB de RAM et un SSD.

## Synchronisation

### Comment fonctionne la synchronisation ?

Rofecare Desktop synchronise sur deux axes :

1. **Cloud** : toutes les 5 minutes si internet disponible, avec `api.rofecare.com`
2. **LAN** : en temps réel entre postes de la même clinique via le réseau local

Voir le guide de [Synchronisation](./sync.md) pour les détails.

### Que se passe-t-il si plusieurs personnes modifient le même dossier en même temps ?

- **Dans la plupart des cas** : les modifications sont fusionnées automatiquement
- **En cas de conflit critique** (ex. deux médecins modifient la même prescription) : l'app crée une entrée de conflit que vous devez résoudre manuellement dans le menu **Paramètres** → **Conflits de synchronisation**

### Pourquoi Rofecare bloque l'accès après 7 jours sans internet ?

C'est une **mesure de sécurité médicale** pour empêcher la dérive des données entre postes déconnectés sur de longues périodes. Après 7 jours, une synchronisation cloud est obligatoire avant de pouvoir continuer à écrire.

Pendant les 7 jours, l'application fonctionne normalement.

### Puis-je désactiver cette limite des 7 jours ?

Non, elle est imposée par l'application pour des raisons de conformité et de sécurité.

### Quel est le volume de données échangé lors d'une sync cloud ?

Cela dépend de l'activité. Typiquement :
- **Sync incrémentale** (5 min) : < 100 KB
- **Sync après 24h** : ~1-5 MB
- **Sync après 7 jours** : ~10-50 MB

Rofecare optimise en envoyant uniquement les changements, jamais les données complètes.

## Données et stockage

### Où sont stockées mes données ?

- **Localement** : base PostgreSQL dans le dossier de données de l'application (chiffré par l'OS)
- **Dans le cloud** : sur les serveurs sécurisés de Rofecare (Europe / Afrique selon votre contrat)

### Mes données sont-elles chiffrées ?

Oui :
- **Au repos** : chiffrement disque du système (FileVault, BitLocker, LUKS)
- **En transit** : HTTPS/TLS 1.3
- **Entre postes LAN** : TLS (auto-signé) ou chiffrement WPA2/WPA3 du WiFi clinique
- **Tenant isolation** : les données de votre hôpital ne sont accessibles qu'à vos utilisateurs

### Que se passe-t-il si mon laptop est volé ?

- Votre disque est **chiffré** (obligatoire), le voleur ne peut pas accéder aux données sans votre mot de passe système
- Votre admin peut **révoquer à distance** votre accès via `rofecare.com`
- Au prochain lancement, votre session sera automatiquement invalidée

### Mon disque est presque plein

Rofecare purge automatiquement les données > 90 jours (configurable). Si vous voulez :
- **Purger plus agressivement** : menu Paramètres → Stockage → 30 jours
- **Garder tout** : menu Paramètres → Stockage → Jamais (attention au disque)

Les données purgées localement **restent accessibles depuis le cloud** — vous pouvez les re-télécharger à tout moment.

### J'ai besoin d'un vieux dossier qui a été purgé, comment le récupérer ?

Menu **Données** → **Importer depuis le cloud** → choisir la période ou l'aggregate souhaité → **Lancer import**.

Les données importées sont protégées de la purge pendant 30 jours par défaut.

## Sécurité

### Comment fonctionne l'authentification ?

- **Création de compte** : uniquement par votre administrateur via `rofecare.com`
- **Premier login sur un nouvel appareil** : internet obligatoire, validation contre le cloud
- **Logins suivants** : fonctionnent offline grâce au cache local chiffré
- **Révocation** : l'admin peut désactiver un compte à distance, effectif en < 1 seconde si internet

### Quelqu'un peut-il lire mes données si je partage mon ordinateur ?

Uniquement si cette personne a :
- Le mot de passe de votre session OS
- Votre mot de passe Rofecare

Chaque session Rofecare est verrouillée après 15 minutes d'inactivité.

### Les administrateurs Rofecare ont-ils accès à mes données ?

Rofecare (l'équipe) a **uniquement accès à vos données en cas d'intervention technique explicite** de votre part (ticket support). Sinon, vos données sont isolées dans votre tenant.

Les administrateurs de **votre hôpital** (ex. le DG de la clinique) ont accès aux données selon les rôles qu'ils se sont attribués.

## Problèmes courants

### Mes collègues ne voient pas mes modifications

1. Vérifier que vous êtes sur le même LAN (WiFi clinique)
2. Vérifier l'indicateur mesh en bas à droite : "👥 X postes connectés"
3. Si 0 poste détecté, voir [Dépannage](./troubleshooting.md)

### L'application est lente

- Premier démarrage : ~60s (normal)
- Démarrages suivants : ~15-20s
- Si plus lent : voir [Dépannage → Performance](./troubleshooting.md)

### Je dois désinstaller et réinstaller

Avant de désinstaller :
1. Vérifier que la synchronisation cloud est à jour
2. Sauvegarder le dossier de données au cas où
3. Désinstaller (voir [Installation → Désinstallation](./installation.md))
4. Réinstaller et se reconnecter — vos données reviendront via la sync cloud

## Support

- **Bug technique** : [Ouvrir une issue](https://github.com/rofecare/rofecare-desktop/issues/new/choose)
- **Support utilisateur** : support@rofecare.com
- **Sécurité** : security@rofecare.com
- **Site web** : https://rofecare.com
