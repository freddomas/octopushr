# 04: Schéma Postgres, migrations, RLS deny-all, harnais de test DB

**Status:** ready-for-agent
**Blocked by:** 02
**Type:** afk
**Lire uniquement :** lld.md §1 (accès données), §2, §9 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Les migrations s'appliquent sur une base vide, toutes les tables ont RLS activé sans policy, les contraintes du LLD §2 sont testées et npm test est vert.

**What to build:** le schéma complet du LLD §2 (dont `kiosk_devices`, `device_activation_codes`, `organizations.missing_exit_after_hours`, fuseau par défaut `Africa/Kinshasa`) en Drizzle + migration SQL, la connexion `src/server/db` (driver `postgres`, pooler sans requêtes préparées), le type `Scope`, le squelette de `src/server/data/` et `getOrgSettings(scope)` (fuseau, seuil de Sortie manquante), le harnais de test (schéma isolé par exécution, fixtures `makeOrg`, `makeAdmin`, `makeEmployee`, `makePunch`) et le script `npm run db:migrate`. Décider et documenter (ADR court `docs/adr/0003-test-database.md`) la base de test : `DATABASE_URL_TEST` Supabase dev ou Postgres local.

- [ ] Toutes les tables du LLD §2 avec FK, index et contraintes (CHECK super⇔org_id null, unique partiel : 1 empreinte active par employé ; 1 appareil actif par organisation)
- [ ] RLS activé sur chaque table, aucune policy ; test prouvant qu'un rôle `anon` ne lit rien
- [ ] Tests d'intégration des contraintes (matricule dupliqué par org, 2e empreinte active refusée, 2e appareil actif refusé, etc.)
- [ ] Fixtures et harnais documentés dans l'ADR 0003
- [ ] Migrations rejouables sur base vide ; aucune donnée métier insérée
