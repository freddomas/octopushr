# 01: Accès durables GitHub, Vercel, Supabase + dépôt de travail

**Status:** resolved
**Blocked by:** None (can start immediately)
**Type:** human
**Lire uniquement :** spec.md (Further Notes), lld.md §8 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le dépôt freddomas/octopushr est cloné dans /home/usr_codex/workspace/my_projects/OctopusHR avec ce spec, et gh, vercel et la connexion Supabase fonctionnent sans ressaisie.

**What to build:** l'orchestrateur demande à l'utilisateur les accès durables/persistants dont dépendent les tickets suivants, puis prépare le dossier de travail.

- [ ] Accès Git : jeton/clé permettant clone et push sur `https://github.com/freddomas/octopushr.git` (credential helper persistant, pas de saisie à chaque push)
- [ ] Accès Vercel : jeton (`VERCEL_TOKEN`) avec droits sur le projet `octopushr`, `vercel link` fait, scope/team notés
- [ ] Accès Supabase : `DATABASE_URL` (pooler) valide depuis le VPS, test `select 1` OK ; base/schéma de test éventuel (`DATABASE_URL_TEST`)
- [ ] Secrets générés et stockés hors dépôt : `SESSION_SECRET`, identifiants du super-administrateur (≥ 12 caractères)
- [ ] Domaine de production stable confirmé (`WEBAUTHN_RP_ID`, par défaut `octopushr.vercel.app`) et fuseau horaire par défaut des organisations confirmé
- [ ] Dossier `/home/usr_codex/workspace/my_projects/OctopusHR` créé ; `CLAUDE.md`, `GLOSSARY.md`, `docs/`, `.scratch/` copiés dedans ; premier commit poussé
- [ ] Aucun secret committé

## Answer

- GitHub : `gh` 2.104 connecté (compte freddomas, scopes repo/workflow/read:org), credential helper git HTTPS. Dépôt `freddomas/octopushr` (public), `main` poussé.
- Vercel : connecteur autorisé ; projet `octopushr` (prj_zfRAC7Avaa9uAwPvIC4RoeFRyRAj, team_JIhhMm1KcelpnrALMsIzqnBn). `DATABASE_URL` présente en production (sensitive, non relisible). La protection SSO Vercel est active : à désactiver au ticket 17.
- Supabase : connecteur OK, projet OctopusHR (cdmykxlqrkylyfpzglww), base vide. Pas de DATABASE_URL locale : base de test à trancher au ticket 04 (Postgres local ou connecteur).
- Dépôt de travail : /home/usr_claude/Projects/OpenHR (le chemin /home/usr_codex n'existe pas sur ce serveur).
- Reste pour le ticket 17 : SESSION_SECRET, WEBAUTHN_*, APP_URL et identifiants super-admin dans Vercel ; domaine par défaut octopushr.vercel.app ; fuseau par défaut Indian/Mauritius (à reconfirmer avant le ticket 06).
