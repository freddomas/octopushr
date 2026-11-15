# 02: Squelette Next.js + outillage + route /api/health

**Status:** resolved
**Blocked by:** 01
**Type:** afk
**Lire uniquement :** lld.md §1, §9 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** npm run lint, typecheck, test et build passent, et GET /api/health renvoie 200 {ok:true} en local.

**What to build:** un projet Next.js (App Router, TypeScript strict, Tailwind, ESLint, Vitest, Playwright configuré avec émulation Pixel 7) avec la structure de dossiers du LLD §1, un `.env.example` (variables LLD §8, sans valeurs), un `.gitignore` sûr (`.env*` sauf l'exemple) et une route `GET /api/health`. Scripts npm : `dev`, `build`, `lint`, `typecheck`, `test`, `test:e2e`.

- [ ] `npm run lint`, `typecheck`, `test` (1 test sur /api/health), `build` verts
- [ ] Structure de dossiers conforme au LLD §1
- [ ] `.env.example` liste toutes les variables du LLD §8
- [ ] Workflow GitHub Actions : lint + typecheck + test + build sur push/PR
- [ ] Rien d'autre (aucune page métier)
