# 22: Durcissement sécurité transverse

**Status:** ready-for-agent
**Blocked by:** 14, 15, 17, 19, 20, 21
**Type:** afk
**Modèle / effort :** opus / high
**Lire uniquement :** lld.md §13 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Les tests d'en-têtes passent (y compris caméra, workers, wasm), npm audit --omit=dev ne remonte aucune vulnérabilité haute/critique, le scan de secrets est propre et la suite complète reste verte.

**What to build:** appliquer LLD §13 : CSP compatible avec caméra, service worker, wasm et blob, HSTS, nosniff, Referrer-Policy, `Permissions-Policy: camera=(self)`, frame-ancestors none ; contrôle `Origin` sur tous les endpoints mutants ; rate limit sur login, enregistrement de pointeuse, `/api/punch*` ; limites de taille de payload (photo, descripteur, lots de synchro) ; erreurs non divulgâtrices ; logs sans secret ni biométrie ; audit des dépendances et scan de secrets.

- [ ] Test automatisé des en-têtes sur `/login`, `/pointer`, `/admin`
- [ ] Mutation avec `Origin` étranger ⇒ 403 sur chaque endpoint mutant (liste générée)
- [ ] Payload > limite ⇒ 413 ; descripteur mal formé ⇒ 400
- [ ] `npm audit --omit=dev` : 0 haute/critique ; scan de secrets : 0 fuite
- [ ] `/pointer`, l'enrôlement et la synchro fonctionnent avec la CSP (E2E)
- [ ] Rapport court `docs/security/hardening.md`
