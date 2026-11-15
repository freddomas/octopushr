# 17: File « à vérifier / suspects », validation/rejet commentés, journal d'audit

**Status:** ready-for-agent
**Blocked by:** 16
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** spec.md stories 42–44, lld.md §11 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un admin traite un pointage « à vérifier » et un « suspect » en validant ou rejetant avec commentaire ; le statut change, le pointage rejeté n'est plus compté et chaque action apparaît dans /admin/journal (tests verts).

**What to build:** `/admin/a-verifier` (file des statuts `to_review` et `suspect`, tri anciens d'abord, compteur dans la sidebar), actions Valider/Rejeter avec commentaire obligatoire (≥ 5 caractères) via `POST /api/admin/punches/:id/review`, `/admin/journal` (audit_log paginé, filtres action/admin/date), écriture d'audit pour validate, reject, enroll_face, delete_face, pin_reset, register_pointeuse, revoke_pointeuse, settings_update. Un pointage rejeté est exclu des durées, présences et totaux.

- [ ] Valider ⇒ `validated` ; Rejeter ⇒ `rejected` ; `reviewed_by/at/comment` renseignés
- [ ] Commentaire trop court ⇒ refus ; décision déjà prise ⇒ conflit clair
- [ ] Chaque action écrit une ligne d'audit avec avant/après, sans donnée biométrique
- [ ] Isolation : un admin ne revoit que les pointages de son organisation
- [ ] Rejet pris en compte dans le tableau, le tableau de bord et l'export
