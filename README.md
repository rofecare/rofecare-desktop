# rofecare-desktop

> Application desktop officielle Rofecare. Tauri V2 offline-first, SQLite local + sync CRDT vers le monolithe via mTLS.

## Mission

Permettre aux établissements de santé en zones à connectivité instable (Afrique centrale) d'utiliser Rofecare **en mode offline-first** : toutes les opérations cliniques s'effectuent localement contre une base SQLite chiffrée, puis sont synchronisées en arrière-plan vers `rofecare-server` via mTLS dès que la connexion est disponible.

> **V1 (sidecars) archivée 2026-04-17.** Le repo héberge désormais uniquement V2 (Tauri + SQLite + CRDT + mTLS). Spec complète : [`docs/`](docs/) et `desktop/spec/` à la racine workspace.

## Stack

- Tauri 2 (Rust core + WebView)
- Frontend : Nuxt build de `rofecare-frontend/apps/clinic` chargé dans la WebView Tauri
- SQLite (chiffrée SQLCipher) — base locale
- CRDT (Automerge ou équivalent) — résolution de conflits hors-ligne
- mTLS — auth machine vers le monolithe pour la sync peer
- Plateformes cibles : Windows 10/11, macOS 12+, Linux Ubuntu 22.04+

## Structure

```
rofecare-desktop/
├── src-tauri/        # Cœur Rust (commandes Tauri, sync engine, mTLS, SQLite)
├── docs/             # Architecture V2, ADRs, runbooks
├── AGENTS.md         # Configuration agents Claude pour ce repo
├── CHANGELOG.md
└── LICENSE
```

(Le frontend Vue n'est pas dupliqué ici — il est consommé depuis `rofecare-frontend/apps/clinic` à la build.)

## Build

Pré-requis : Rust stable, Node 20+, pnpm. Voir `docs/` pour le bootstrap complet par OS.

```bash
# Dev
pnpm tauri dev

# Build release (génère installeurs natifs par plateforme)
pnpm tauri build
```

## Tests

```bash
cargo test                 # tests Rust (sync engine, CRDT, mTLS)
pnpm tauri test            # tests E2E webview
```

## Dépendances inter-repos

- **Consomme** : `rofecare-frontend/apps/clinic` (frontend chargé dans la webview), `rofecare-server` (sync mTLS via `rofecare-platform` peer-sync)
- **Consommé par** : aucun

## Roadmap

234 tâches `T001`-`T234` en 12 phases (~22 semaines). Avancement : `progress_desktop_foundations.md` (mémoire workspace).

## Conventions

Référentiel Vue côté webview : [`../rofecare-server/.claude/rules/vue-frontend.md`](../rofecare-server/.claude/rules/vue-frontend.md). Pas de mapping statuts/enums côté front — tout vient du backend (`statusLabel`, `statusColor`). Conventional Commits, branche `develop`, commits atomiques. Aucun agent ne commit/push : l'utilisateur passe par `/speckit.commit` + `/speckit.pr`.

## Liens utiles

- Vue d'ensemble workspace : [`../CLAUDE.md`](../CLAUDE.md)
- Spec V2 complète : `../specs/` + `docs/`
- AGENTS desktop : [`AGENTS.md`](AGENTS.md)
- CHANGELOG : [`CHANGELOG.md`](CHANGELOG.md)

---

**Statut** : Actif (développement V2 en cours, 234 tâches, ~22 semaines) · V1 archivée 2026-04-17 · dernière mise à jour 2026-05-16.
