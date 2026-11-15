# 24: Revue de sécurité indépendante + tests d'attaque

**Status:** ready-for-agent
**Blocked by:** 22
**Type:** afk
**Modèle / effort :** opus / xhigh
**Lire uniquement :** lld.md §3, §4, §7, §9, §13, ADR-0002..0005 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le skill security-review ne laisse aucun constat haut/critique ouvert et la suite de tests d'attaque est verte.

**What to build:** un agent de sécurité dédié (distinct des implémenteurs) exécute `security-review` sur la branche d'intégration puis écrit des tests d'attaque : IDOR sur chaque route admin et d'image, élévation admin→super, pointage sans jeton / jeton révoqué / jeton d'une autre organisation, rejeu de `clientId`, soumission d'un descripteur fabriqué (limites connues documentées), énumération de PIN par essais (blocage de pointeuse), force brute du PIN, mutation avec scores falsifiés, injection (SQL/CSV), fuite de biométrie dans réponses/logs, accès aux images sans session. Constats corrigés ou ouverts comme tickets bloquants.

- [ ] Rapport `docs/security/review.md` (constats, gravité, statut, limites assumées de la vivacité côté client)
- [ ] Tests d'attaque ajoutés à `npm test` et verts
- [ ] Aucun constat haut/critique ouvert
- [ ] Preuve que la clé publishable Supabase ne lit aucune table (appel REST direct refusé)
