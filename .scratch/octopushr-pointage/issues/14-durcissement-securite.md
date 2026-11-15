# 14: Durcissement sécurité transverse (en-têtes, CSP, limites, audit dépendances)

**Status:** ready-for-agent
**Blocked by:** 09, 12, 13
**Type:** afk
**Lire uniquement :** lld.md §7 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Les tests d'en-têtes passent, npm audit --omit=dev ne remonte aucune vulnérabilité haute/critique, le scan de secrets est propre et la suite complète reste verte.

**What to build:** appliquer LLD §7 à toute l'application : en-têtes (CSP avec nonce, HSTS, nosniff, Referrer-Policy, Permissions-Policy autorisant WebAuthn pour self, frame-ancestors none), contrôle `Origin` sur tous les endpoints mutants, rate limit sur tous les endpoints d'authentification, erreurs non divulgâtrices, logs sans secrets ni PII, audit des dépendances et scan de secrets (gitleaks).

- [ ] Test automatisé des en-têtes sur `/login`, `/pointer`, `/admin`
- [ ] Requête mutante avec `Origin` étranger ⇒ 403 sur chaque endpoint mutant (liste exhaustive générée)
- [ ] `npm audit --omit=dev` : 0 haute/critique ; gitleaks : 0 fuite
- [ ] `/pointer` et l'enrôlement fonctionnent toujours avec la CSP (E2E)
- [ ] Rapport court dans `docs/security/hardening.md`
