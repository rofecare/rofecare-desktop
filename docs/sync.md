# Synchronisation — Comment ça marche

Rofecare Desktop utilise un système de synchronisation **à deux niveaux** qui combine temps réel local et synchronisation cloud opportuniste.

## Vue d'ensemble

```
┌─────────────────────────────┐
│   Cloud rofecare.com        │   ← Source de vérité
│   (source de vérité)        │
└──────────┬──────────────────┘
           │
           │ Sync cloud toutes les 5 min
           │ (si internet disponible)
           │
           ▼
┌─────────────────────────────┐
│  Un poste de votre clinique │
│  connecté à internet        │
└──────────┬──────────────────┘
           │
           │ Diffusion temps réel LAN
           │ (mesh Mercure)
           │
           ▼
┌─────────────────────────────┐
│  Tous les autres postes     │
│  de votre clinique          │
└─────────────────────────────┘
```

## Principe essentiel

**Un seul poste de la clinique doit parler au cloud** pour que **tous les autres postes** reçoivent les nouvelles données. Cela économise énormément de bande passante internet et permet à une clinique avec une connexion internet faible de rester à jour avec peu de coût.

## Les 2 axes de synchronisation

### Axe 1 — Synchronisation cloud

**Quand** : toutes les 5 minutes si une connexion internet est détectée.

**Ce qui se passe** :
1. Un poste (celui qui a internet) contacte `api.rofecare.com`
2. Il télécharge les nouvelles données créées depuis la dernière sync
3. Il envoie ses propres modifications locales vers le cloud
4. Les données cloud arrivent sur son poste

**Bande passante** : typiquement < 1 MB par sync en utilisation normale.

### Axe 2 — Mesh LAN temps réel (Mercure)

**Quand** : en continu, dès qu'un changement se produit.

**Ce qui se passe** :
1. Quand vous créez/modifiez quelque chose sur votre poste, c'est appliqué localement
2. Votre poste publie l'événement sur son hub Mercure local
3. Tous les autres postes de la clinique, connectés à votre hub via mDNS, reçoivent l'événement en < 1 seconde
4. Chacun applique le changement dans sa propre base locale
5. Leurs UI se mettent à jour automatiquement

**Bande passante** : uniquement les changements, tout en LAN (gratuit).

### Conjonction des deux axes

Quand un poste sync avec le cloud, les nouvelles données qu'il reçoit sont **automatiquement propagées aux autres postes via Mercure LAN** — sans que ces autres postes aient besoin de sync avec le cloud eux-mêmes.

**C'est la magie du "un sync pour tous".**

## Délais typiques

| Action | Visible sur les autres postes de la clinique | Visible sur le cloud |
|--------|----------------------------------------------|----------------------|
| Création d'un patient | < 1 seconde (via LAN) | < 5 min (via sync cloud) |
| Prescription ajoutée | < 1 seconde | < 5 min |
| Nouvelle facture | < 1 seconde | < 5 min |
| Consultation d'un résultat de labo | Immédiat | Immédiat si online, sinon < 5 min |

## Que se passe-t-il si...

### ... il n'y a pas d'internet du tout ?

- Les postes de la clinique continuent à se synchroniser entre eux via le LAN
- Chacun peut créer, modifier, consulter comme normal
- La sync cloud reprendra automatiquement dès qu'internet sera de nouveau disponible

**Limite** : après **7 jours** sans aucune sync cloud réussie, l'application passe en **mode lecture seule** jusqu'à une reconnexion. C'est une sécurité pour empêcher la dérive des données.

### ... il n'y a qu'un seul poste dans la clinique ?

Aucun problème. Le mesh LAN n'est utile qu'avec plusieurs postes. Avec un seul poste, seule la sync cloud fonctionne.

### ... il y a plusieurs postes mais aucun avec internet ?

Les postes se synchronisent entre eux en LAN. Les données restent à jour entre eux. La sync cloud attendra qu'un poste ait à nouveau internet.

### ... plusieurs postes ont internet en même temps ?

Pas de problème. Chaque poste fait sa propre sync cloud. Les doublons sont automatiquement dédupliqués.

### ... je modifie la même information que mon collègue en même temps ?

- **Pour la plupart des cas** (création de patient, nouvelle prescription, etc.) : aucun conflit, les deux changements sont conservés
- **Pour les updates rares** : la modification la plus récente gagne (last-write-wins)
- **Pour les changements critiques** (ex. deux médecins modifient la même prescription) : conflit détecté et stocké pour résolution manuelle

## Indicateurs de synchronisation

### Icône de statut (en bas à droite de la fenêtre)

| Icône | Signification |
|-------|---------------|
| 🟢 **Sync OK** | Tout est à jour |
| 🟡 **Sync en attente** | Des modifications locales attendent d'être envoyées au cloud |
| 🟠 **Internet absent** | Pas de sync cloud en cours, LAN seul |
| 🔴 **Dérive critique** | > 5 jours sans sync cloud, bientôt lecture seule |
| 🔒 **Lecture seule** | > 7 jours sans sync cloud, écriture bloquée |

### Mesh LAN

```
👥 4 postes connectés
```

Cliquez pour voir la liste :

- Dr. Martin (Active — 2s)
- Mami (Active — 0s)
- Accueil (Active — 5s)
- Tina (Active — 1s)

## Contrôle manuel

### Forcer une sync

Menu **Aide** → **Synchroniser maintenant** ou raccourci `Cmd/Ctrl + Maj + S`.

### Voir les changements en attente

Menu **Paramètres** → **Synchronisation** → **Modifications locales en attente**.

### Importer depuis le cloud

Menu **Données** → **Importer depuis le cloud**. Voir [Premiers pas](./getting-started.md).

## Sécurité de la sync

- **HTTPS TLS 1.3** entre le poste et le cloud
- **Isolation par group** : votre hôpital ne voit jamais les données d'un autre hôpital
- **JWT signé** : chaque requête de sync est authentifiée
- **Signature Mercure** : chaque événement publié est signé et vérifié par les récepteurs
- **Rejet des événements inter-groups** : même en cas de LAN partagé, les événements d'un autre group sont automatiquement rejetés

## Pour aller plus loin

- [Configuration LAN](./lan-setup.md) — déploiement multi-postes dans une clinique
- [FAQ](./faq.md#synchronisation) — questions fréquentes sur la sync
- [Dépannage](./troubleshooting.md#synchronisation) — résoudre les problèmes de sync
