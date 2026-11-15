# 04: Schéma Postgres, migrations, RLS deny-all, harnais de test DB, variables d'environnement

**Status:** ready-for-agent
**Blocked by:** 02
**Type:** afk
**Lire uniquement :** lld.md §1 (accès données), §2, §15, §16 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Les migrations s'appliquent sur une base vide, toutes les tables du LLD §2 ont RLS activé sans policy, les contraintes sont testées et npm test est vert.

**What to build:** le schéma complet du LLD §2 en Drizzle + migration SQL (organizations, org_settings, admins, employees, pointeuses, face_profiles, face_templates, punches, audit_log, rate_limits), la connexion `src/server/db`, le type `Scope`, le squelette `src/server/data/` avec `getOrgSettings(scope)`, le harnais de test (schéma isolé par exécution, fixtures `makeOrg`, `makeAdmin`, `makeEmployee`, `makeFaceProfile`, `makePointeuse`, `makePunch`), `npm run db:migrate`. Met `.env.example` en conformité avec LLD §15 (retire `WEBAUTHN_*`, ajoute `BIOMETRIC_KEYS`, `BIOMETRIC_KEY_VERSION`, `CRON_SECRET`). Décider et documenter (ADR 0006 `docs/adr/0006-base-de-test.md`) la base de test : Postgres local ou `DATABASE_URL_TEST` Supabase ; sans URL de connexion disponible sur le serveur de dev, privilégier un Postgres local ou l'outil de migration du connecteur Supabase.

- [ ] Toutes les tables, FK, index et contraintes du LLD §2 (matricule `^[0-9]{3,10}$` unique par org ; CHECK super⇔org_id null ; un profil facial actif par employé ; `punches.id` unique ; bornes de `org_settings`)
- [ ] RLS activé partout, aucune policy ; test qu'un rôle `anon` ne lit rien
- [ ] Tests d'intégration des contraintes (matricule invalide/dupliqué, 2e profil actif refusé, bornes des paramètres)
- [ ] `org_settings` créée avec les valeurs par défaut du LLD à la création d'une organisation (fonction de données)
- [ ] Fixtures et harnais documentés dans l'ADR 0006 ; migrations rejouables ; aucune donnée métier insérée
