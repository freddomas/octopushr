# 09: Pointeuses : enregistrer ce téléphone, jeton d'appareil, liste, révocation

**Status:** ready-for-agent
**Blocked by:** 06
**Type:** afk
**Modèle / effort :** opus / high
**Lire uniquement :** spec.md stories 1–6, lld.md §4, ADR-0003 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un admin de fixture enregistre le navigateur courant comme pointeuse, reçoit le cookie d'appareil, la session admin est fermée ; requirePointeuse accepte ce cookie et refuse (403) absent, inconnu ou révoqué (tests verts).

**What to build:** page `/admin/pointeuses` (liste : site, enregistrée le, dernière activité, statut ; bouton « Enregistrer ce téléphone comme pointeuse » avec nom du site ; bouton « Révoquer » avec confirmation), `POST /api/admin/pointeuses`, `DELETE /api/admin/pointeuses/:id`, helper serveur réutilisable `requirePointeuse(req)`, écran « Téléphone non enregistré » (403 générique, français), journalisation dans `audit_log` (register/revoke).

- [ ] Jeton 32 octets aléatoires, seul le sha256 est stocké ; cookie `__Host-octo_pointeuse` HttpOnly Secure SameSite=Lax 1 an
- [ ] Après enregistrement : session admin fermée, redirection vers `/pointer`
- [ ] `requirePointeuse` : cookie absent/inconnu/révoqué ou organisation inactive ⇒ 403 `pointeuse_invalide` ; met à jour `last_seen_at`
- [ ] Révocation immédiate côté serveur
- [ ] Une organisation peut avoir plusieurs pointeuses ; un admin ne voit/révoque que celles de son organisation (isolation testée)
- [ ] Rate limit sur l'enregistrement ; contrôle `Origin`
