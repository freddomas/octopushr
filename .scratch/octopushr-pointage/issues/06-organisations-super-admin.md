# 06: Super-admin : créer et gérer les organisations + premier administrateur

**Status:** ready-for-agent
**Blocked by:** 05
**Type:** afk
**Lire uniquement :** spec.md stories 32–34, lld.md §2 (organizations, admins), §3 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Connecté en super-admin de fixture, je crée une organisation avec son premier administrateur, ce dernier se connecte, et je peux désactiver/réactiver l'organisation (tests verts).

**What to build:** page `/super/organisations` : liste (nom, fuseau, nb d'employés, statut), formulaire de création (nom, fuseau IANA, identifiant + mot de passe initial du premier administrateur), activer/désactiver. Une organisation désactivée empêche la connexion de ses administrateurs.

- [ ] Création atomique organisation + premier administrateur ; slug unique généré
- [ ] Fuseau validé contre la liste IANA ; défaut = valeur confirmée au ticket 01
- [ ] Identifiant admin unique globalement (erreur claire) ; mot de passe ≥ 12 caractères
- [ ] Organisation désactivée ⇒ admins refusés (session existante rejetée à la requête suivante)
- [ ] Accessible uniquement au super-admin (un admin reçoit 403)
- [ ] Interface française avec états vide/erreur/chargement
