# Rofecare Desktop - Distribution docs

> Instructions locales pour la session Codex desktop sur `rofecare-desktop`. Ce repo est un perimetre desktop separe du repo `desktop/`.

## Ownership

- Session responsable : **desktop**.
- Modifications autorisees uniquement dans `rofecare-desktop/` quand ce repo est explicitement annonce comme perimetre de travail.
- Lecture autorisee de `desktop/`, `rofecare-frontend/` et backend pour comprendre les contrats, l'UX, la synchronisation et les integrations.
- Aucune modification de backend, Flyway, Nuxt, specs globales, scripts de deploiement ou autres sous-repos sans validation de la session principale **backend/core**.
- Aucun commit ni push automatique.
- Avant toute modification, annoncer les chemins exacts concernes.

## Sources locales

- `README.md` - presentation et usage du repo.
- `docs/getting-started.md` - demarrage.
- `docs/installation.md` - installation.
- `docs/deployment.md` - deploiement desktop.
- `docs/sync.md` et `docs/lan-setup.md` - synchronisation et LAN.
- `docs/troubleshooting.md` et `docs/faq.md` - support.
- `CHANGELOG.md` - historique.

## Regles techniques

- Ce repo ne doit pas devenir une source de verite backend ou schema sans coordination.
- Toute modification de documentation doit rester coherente avec `desktop/spec/` si elle concerne l'architecture, la sync, l'installation ou la release.
- Les sujets sensibles comme enrollment, audit, stockage local, update/signing, sync LAN et donnees patient doivent etre verifies avec la session **backend/core** quand ils touchent des contrats ou garanties systeme.

## Validation

- Verifier les liens et chemins documentes apres modification.
- Si une commande est documentee, verifier qu'elle existe ou marquer clairement son statut.
- Pour les changements qui decrivent une release, comparer avec `desktop/spec/devops/RELEASE-CHECKLIST.md`.

## Coordination

- Backend + desktop, schema + sync, auth/passkeys, API contracts, securite sensible, packaging release ou deploiement passent par la session **backend/core** ou `rofecare-conductor`.
- Si la tache necessite une implementation Tauri/Rust, basculer le travail vers `desktop/` ou annoncer explicitement le changement de perimetre.
