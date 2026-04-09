# Configuration réseau local (LAN) — Déploiement multi-postes

Ce guide explique comment déployer Rofecare Desktop sur plusieurs postes dans la même clinique, pour qu'ils partagent leurs données en temps réel via le réseau local.

## Principe

Rofecare Desktop utilise un **mesh peer-to-peer** sur votre réseau local (LAN). Concrètement :

- Chaque poste embarque sa propre base de données
- Les postes se **détectent automatiquement** entre eux via mDNS/Bonjour (sans configuration)
- Les modifications sont **diffusées en temps réel** entre postes via le hub Mercure embarqué
- **Aucun serveur central n'est requis**

## Prérequis réseau

### Tous les postes doivent être sur le même réseau local

- Même **WiFi** clinique (SSID unique, pas de WiFi invité isolé)
- Ou même **LAN filaire** (switch Ethernet)
- Ou combinaison WiFi + filaire dans le même sous-réseau IP

### Ports et protocoles

Votre firewall et routeur clinique doivent autoriser :

| Protocole | Port | Usage |
|-----------|------|-------|
| UDP | 5353 | mDNS (découverte Bonjour) |
| TCP | 3001 | Mercure (pub/sub entre postes) |
| TCP | 8080 | API REST (catch-up et transferts bulk) |

**Tous en localhost + LAN uniquement**, jamais exposés sur internet.

### Configuration WiFi recommandée

- **WPA2 ou WPA3** minimum
- **Isolation client désactivée** (important !) — si activée, les postes WiFi ne peuvent pas se voir entre eux
- Vérifier dans les paramètres du routeur : "AP Isolation" ou "Client Isolation" doit être **OFF**

## Déploiement étape par étape

### Étape 1 — Créer les comptes utilisateurs dans le cloud

Avant d'installer quoi que ce soit sur les postes, l'admin de la clinique doit créer les comptes utilisateurs sur `rofecare.com` :

1. Se connecter à `https://app.rofecare.com` en tant qu'admin tenant
2. Aller dans **Administration** → **Utilisateurs**
3. Créer un compte pour chaque employé (médecins, infirmiers, accueil, etc.)
4. Assigner les rôles appropriés
5. Envoyer les identifiants aux employés (email automatique)

### Étape 2 — Installer Rofecare Desktop sur le premier poste

Choisir un **poste principal** (ex. le PC du bureau administratif, un mini-PC dédié, ou le poste du médecin chef).

1. Télécharger Rofecare Desktop (voir [Installation](./installation.md))
2. Installer sur le poste
3. Lancer l'application
4. **Premier login** (internet requis) avec un compte admin
5. Au wizard d'import initial : choisir **"Tenant vide"** (si nouvelle clinique) ou **"Importer depuis le cloud"** (si migration depuis `app.rofecare.com`)
6. Attendre la fin de l'import (durée variable selon le volume)
7. Vérifier que l'application fonctionne

### Étape 3 — Tester le mesh LAN sur le premier poste

Pendant quelques minutes, utiliser l'application sur le premier poste pour :
- Créer un patient de test
- Vérifier que l'enregistrement est OK
- Noter l'indicateur mesh en bas à droite : `👥 0 postes connectés`

C'est normal — il n'y a qu'un seul poste pour l'instant.

### Étape 4 — Installer sur le deuxième poste

1. Sur un autre ordinateur connecté au **même WiFi/LAN de la clinique**, installer Rofecare Desktop
2. Lancer l'application
3. Premier login avec un autre compte (ex. une infirmière)
4. **Wizard d'import** : l'application détecte le premier poste sur le LAN
5. Choisir **"Importer depuis [premier poste]"** (plus rapide que le cloud)
6. Transfer LAN, quelques minutes selon le volume
7. Le deuxième poste rejoint le mesh

### Étape 5 — Vérifier la connexion mesh

Sur les deux postes, vérifier l'indicateur mesh en bas à droite :

```
👥 2 postes connectés
```

Cliquer pour voir la liste.

### Étape 6 — Test de synchronisation temps réel

1. Sur le premier poste : créer un nouveau patient
2. Observer sur le deuxième poste : le patient apparaît en **< 1 seconde**
3. Sur le deuxième poste : modifier les informations du patient
4. Observer sur le premier poste : les modifications apparaissent en temps réel

Si ça marche : **le mesh LAN est opérationnel** ✓

### Étape 7 — Ajouter les autres postes

Répéter l'étape 4 pour chaque poste supplémentaire de la clinique (5, 10, 15 postes, etc.).

Chaque nouveau poste découvre automatiquement les précédents, import rapide via LAN, rejoint le mesh.

## Topologie recommandée par taille de clinique

### Petite clinique (1-3 postes)

- WiFi unique suffit
- Pas de configuration particulière
- Mesh plein (chaque poste connecté à tous les autres)

### Clinique moyenne (4-10 postes)

- WiFi dédié aux postes Rofecare
- Idéalement un mini-PC ou NUC en "backbone" (ex. poste accueil toujours allumé)
- Mesh plein fonctionne bien

### Grosse clinique (11-20 postes)

- Vérifier la capacité du point d'accès WiFi
- Considérer un switch Ethernet pour les postes fixes
- Mesh plein encore viable

### Hôpital (> 20 postes)

- Décomposer en plusieurs sous-réseaux par service (radiologie, chirurgie, labo)
- Chaque sous-réseau = un mesh indépendant
- Serveur Rofecare central à envisager (nous contacter : enterprise@rofecare.com)

## Quelle différence avec un serveur centralisé ?

Certaines cliniques demandent s'il faut un "serveur Rofecare" dédié. **Non, pas nécessaire** :

| Aspect | Rofecare mesh peer-to-peer | Serveur centralisé classique |
|--------|----------------------------|------------------------------|
| Matériel | Aucun supplémentaire | 1 serveur à acheter et maintenir |
| Point de panne | Aucun (mesh résilient) | Serveur = SPOF |
| Mise à jour | Auto sur chaque poste | Mise à jour du serveur = downtime |
| Coût | Gratuit | Coût matériel + admin sys |
| Backup | Chaque poste est une copie | Backup du serveur obligatoire |

## Dépannage LAN

### Les postes ne se détectent pas

1. **Vérifier qu'ils sont sur le même réseau** : chaque poste doit avoir une IP dans la même plage (ex. `192.168.1.x`)
2. **Vérifier l'isolation WiFi** : Access Point Isolation doit être **OFF** dans les paramètres du routeur
3. **Firewall OS** : autoriser Rofecare sur les 3 ports (5353 UDP, 3001 TCP, 8080 TCP)
4. **Tester mDNS** :
   - macOS : `dns-sd -B _rofecare-mercure._tcp local.`
   - Windows : installer Bonjour Print Services pour Windows
5. **Redémarrer Rofecare** sur tous les postes

### Postes détectés mais pas de sync

1. Vérifier que les postes ont bien le **même tenant** (même hôpital)
2. Vérifier les logs : Menu Aide → Diagnostics
3. Contacter le support avec les logs : support@rofecare.com

### Un poste est isolé du reste

Possible si :
- Le poste est sur un VLAN différent
- Le poste est en WiFi invité isolé
- Le firewall bloque mDNS

Solution : remettre le poste sur le même réseau que les autres.

## Synchronisation avec le cloud depuis le LAN

Dans une clinique en mesh LAN, **il suffit qu'un seul poste ait internet** pour que tous les autres reçoivent les mises à jour cloud.

Concrètement :
1. Un poste (n'importe lequel) détecte internet
2. Il pull les changements cloud → son DB locale
3. Il publie ces changements sur le mesh Mercure LAN
4. Tous les autres postes reçoivent en temps réel

**Vous n'avez rien à configurer**, c'est automatique.

## Sécurité LAN

- **Chiffrement du WiFi clinique** : WPA2 ou WPA3 obligatoire
- **Isolation de l'accès invité** : les WiFi "visiteurs" ne doivent pas être sur le même réseau que les postes Rofecare
- **JWT Mercure** : même si un intrus se connecte au WiFi clinique, il ne peut pas se faire passer pour un peer sans la clé JWT du tenant (stockée dans le keychain OS)

## Prochaines étapes

- [Guide de synchronisation](./sync.md) pour comprendre le fonctionnement de la sync
- [Dépannage](./troubleshooting.md) pour les problèmes courants
- [FAQ](./faq.md) pour les questions fréquentes
