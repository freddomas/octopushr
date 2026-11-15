# 17: Déploiement production Vercel + recette + transmission du lien

**Status:** ready-for-agent
**Blocked by:** 01, 15, 16
**Type:** afk
**Lire uniquement :** lld.md §8, spec.md (Further Notes) (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** L'URL de production du projet octopushr répond 200 sur /api/health, le parcours de fumée en production passe et le lien est transmis à l'utilisateur avec la procédure de test.

**What to build:** déployer en production sur le projet Vercel `octopushr` : variables d'environnement dans Vercel (jamais dans le dépôt), `WEBAUTHN_RP_ID`/`WEBAUTHN_ORIGIN`/`APP_URL` alignés sur l'alias de production stable, migrations appliquées via le script dédié, `seed:super` exécuté une fois puis variables de seed retirées, organisation de démonstration « Manoir OCTOPUS » + admin créés, test de fumée en production (login admin, création d'employé, génération du lien), vérification des en-têtes en production. Un agent de déploiement dédié (jamais un implémenteur de fonctionnalité).

- [ ] `/api/health` 200 en production ; HTTPS ; en-têtes présents
- [ ] Migrations appliquées sur la base de production ; RLS vérifié actif
- [ ] Super-admin amorcé ; variables de seed retirées de Vercel
- [ ] Organisation de démonstration prête ; lien d'enrôlement de test généré pour l'utilisateur
- [ ] Message final : lien Vercel, identifiants admin de test (transmis hors dépôt), procédure sur Android (ouvrir le lien d'enrôlement, enregistrer l'empreinte, ouvrir /pointer)
- [ ] Rappel : la biométrie réelle doit être testée par l'utilisateur sur son téléphone
