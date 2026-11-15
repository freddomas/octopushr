# 03: Design system AdminLTE-like + logo + coquille d'application

**Status:** ready-for-agent
**Blocked by:** 02
**Type:** afk
**Lire uniquement :** lld.md §6 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** La page /design (hors production) affiche tous les composants avec le logo Manoir OCTOPUS, le layout admin est correct à 375 px et 1280 px, et lint/typecheck/test/build sont verts.

**What to build:** la couche visuelle réutilisable : tokens Tailwind (couleurs, police Source Sans 3), composants `Button`, `Input` (avec icône), `Card`, `SmallBox` (tuile colorée), `StatCard`, `Table`, `Badge`, `Alert`, `Spinner`, `EmptyState`, `PageHeader` ; layouts `AdminShell` (sidebar sombre repliable + topbar + slot) et `CenteredCardLayout` ; logo (variantes recadrée, carrée, favicon, icônes PWA 192/512 générées depuis `docs/assets/logo-octopus-manoir.png`) ; `manifest.webmanifest` (start_url `/pointer`) ; page `/design` visible hors production uniquement. Aucun accès base de données.

- [ ] Composants ci-dessus, typés, accessibles (labels, focus visible, contraste AA du texte)
- [ ] Sidebar : entrées passées en props ; repliable < 992 px
- [ ] Logo lisible sur fond sombre (pastille/bandeau blanc) et sur fond clair
- [ ] Favicon, icônes et manifest servis ; pas de service worker
- [ ] Captures de `/design` à 375 px et 1280 px ajoutées en fin de ticket (`## Comments`)
- [ ] Tests de rendu Vitest des composants clés
