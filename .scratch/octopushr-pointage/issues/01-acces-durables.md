# 01: Accès durables GitHub, Vercel, Supabase + dépôt de travail

**Status:** ready-for-human
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
