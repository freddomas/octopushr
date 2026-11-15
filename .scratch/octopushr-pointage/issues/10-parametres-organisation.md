# 10: Admin : paramètres de l'organisation (seuils, sortie manquante, rétention)

**Status:** ready-for-agent
**Blocked by:** 06
**Type:** afk
**Lire uniquement :** spec.md story 23, lld.md §2 (org_settings), §12 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un admin de fixture modifie les paramètres de son organisation, les valeurs sont persistées, relues par getOrgSettings à chaque usage et tracées dans le journal ; les valeurs hors bornes et une autre organisation sont refusées (tests verts).

**What to build:** page `/admin/parametres` (tous les champs de `org_settings` avec libellé, aide, bornes, valeur par défaut rappelée), `PUT /api/admin/settings`, bouton « Rétablir les valeurs par défaut », journal `settings_update` (anciennes/nouvelles valeurs). Effet immédiat, sans redéploiement ; pas de cache périmé.

- [ ] Bornes : similarité/liveness/antispoof 0–1 ; taille min 80–600 px ; sortie manquante 1–48 h ; rétention 7–730 jours ; échecs avant PIN 1–10 ; PIN max 3–10 ; blocage 1–1440 min
- [ ] Validation zod ; messages d'erreur français
- [ ] Isolation : modification limitée à l'organisation de l'admin
- [ ] Contrôle `Origin` ; entrée « Paramètres » dans la sidebar
- [ ] `challenge_enabled` (défi aléatoire) exposé avec explication
