# 26: Déploiement production Vercel + recette

**Status:** ready-for-agent
**Blocked by:** 01, 23, 24, 25
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** lld.md §15, spec.md (Further Notes) (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** L'URL de production du projet octopushr répond 200 sur /api/health, le parcours de fumée en production passe et le lien est transmis à l'utilisateur avec la procédure de test manuel.

**What to build:** déploiement production sur le projet Vercel `octopushr` : variables d'environnement dans Vercel (jamais dans le dépôt) — `SESSION_SECRET`, `BIOMETRIC_KEYS`, `BIOMETRIC_KEY_VERSION`, `APP_URL`, `CRON_SECRET`, `PIN_PEPPER` (générées par `npm run keys:generate`), `DATABASE_URL` déjà présente ; **désactivation de la protection SSO Vercel pour la production après confirmation de l'utilisateur** ; migrations appliquées via le script dédié ; `seed:super` une fois puis variables de seed retirées ; organisation de démonstration « Manoir OCTOPUS » (fuseau Africa/Kinshasa) + admin de test ; cron de purge actif ; test de fumée en production ; vérification des en-têtes. Un agent de déploiement dédié.

- [ ] `/api/health` 200 ; HTTPS ; en-têtes présents ; modèles et service worker servis avec le bon cache
- [ ] Migrations appliquées en production ; RLS vérifié actif
- [ ] Super-admin amorcé ; variables de seed retirées
- [ ] Organisation de démonstration + admin de test prêts (identifiants transmis hors dépôt)
- [ ] Message final : lien Vercel, comptes de test, lien vers `docs/test-manuel-pointeuse.md`
- [ ] Rappel : biométrie, vivacité et performance doivent être testées par l'utilisateur sur le téléphone réel
