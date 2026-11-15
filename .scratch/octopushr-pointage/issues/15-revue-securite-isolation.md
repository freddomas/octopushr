# 15: Revue de sécurité indépendante + tests d'attaque inter-organisations

**Status:** ready-for-agent
**Blocked by:** 14
**Type:** afk
**Lire uniquement :** lld.md §3, §5, §7, ADR-0001, ADR-0002 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le skill security-review ne laisse aucun constat haut/critique ouvert et la suite de tests d'attaque (inter-organisations, élévation de privilèges, rejeu) est verte.

**What to build:** un agent de sécurité dédié (distinct des implémenteurs) exécute le skill `security-review` sur la branche d'intégration puis écrit une suite de tests d'attaque : IDOR sur chaque route admin (employés, pointages, export, enrôlement, réinitialisation), élévation admin→super, employé sans appareil actif, rejeu de défi WebAuthn, rejeu de jeton d'enrôlement, session d'un admin désactivé, énumération d'identifiants, pointage depuis un autre navigateur avec passkey synchronisée (doit échouer), cookie d'appareil d'une autre organisation, ancien appareil après remplacement, code d'activation rejoué/expiré, injections (SQL/CSV), force brute. Les constats sont corrigés ou ouverts comme tickets bloquants.

- [ ] Rapport `docs/security/review.md` (constats, gravité, statut)
- [ ] Tests d'attaque ajoutés à `npm test` et verts
- [ ] Aucun constat haut/critique ouvert
- [ ] Preuve que la clé publishable Supabase ne permet de lire aucune table (appel REST direct refusé)
