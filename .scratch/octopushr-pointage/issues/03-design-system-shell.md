# 03: Design system AdminLTE-like + logo + coquille d'application

**Status:** ready-for-agent
**Blocked by:** 02
**Type:** afk
**Lire uniquement :** lld.md §10 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** La page /design (hors production) affiche tous les composants avec le logo Manoir OCTOPUS, le layout admin est correct à 375 px et 1280 px, et lint/typecheck/test/build sont verts.

**What to build:** couche visuelle réutilisable : tokens Tailwind (couleurs, Source Sans 3), composants `Button`, `Input` (avec icône), `Card`, `SmallBox`, `StatCard`, `Table`, `Badge`, `Alert`, `Spinner`, `EmptyState`, `PageHeader`, `NumericKeypad` (grand clavier 0–9, effacer, valider) ; layouts `AdminShell` (sidebar sombre repliable + topbar) et `CenteredCardLayout` ; logo (variantes recadrée, carrée, favicon, icônes PWA 192/512 depuis `docs/assets/logo-octopus-manoir.png`) ; `manifest.webmanifest` (start_url `/pointer`) ; page `/design` hors production. Aucun accès base de données, pas de service worker (ticket 11).

- [ ] Composants listés, typés, accessibles (labels, focus visible, contraste AA)
- [ ] Sidebar pilotée par props ; repliable < 992 px
- [ ] Logo lisible sur fond sombre (pastille blanche) et clair
- [ ] Favicon, icônes, manifest servis
- [ ] `NumericKeypad` : cibles ≥ 56 px, utilisable au pouce, clavier physique accepté
- [ ] Captures de `/design` à 375 px et 1280 px en fin de ticket (`## Comments`)
- [ ] Tests de rendu Vitest des composants clés
