# 07: Super-admin : créer et gérer les organisations + premier administrateur

**Status:** ready-for-agent
**Blocked by:** 06
**Type:** afk
**Lire uniquement :** spec.md story 53, lld.md §2 (organizations, org_settings, admins), §3 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Connecté en super-admin de fixture, je crée une organisation avec son premier administrateur, ce dernier se connecte, et je peux désactiver/réactiver l'organisation (tests verts).

**What to build:** page `/super/organisations` : liste (nom, fuseau, nb d'employés, nb de pointeuses, statut), création (nom, fuseau IANA — défaut `Africa/Kinshasa` —, identifiant + mot de passe initial du premier administrateur), activer/désactiver. Création atomique organisation + `org_settings` par défaut + premier administrateur.

- [ ] Création atomique ; slug unique généré ; fuseau validé contre la liste IANA
- [ ] Identifiant admin unique globalement ; mot de passe ≥ 12 caractères
- [ ] Organisation désactivée ⇒ admins refusés (session existante rejetée à la requête suivante)
- [ ] Réservé au super-admin (un admin reçoit 403)
- [ ] Interface française avec états vide/erreur/chargement
