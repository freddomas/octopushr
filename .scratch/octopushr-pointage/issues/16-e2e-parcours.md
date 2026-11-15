# 16: Tests E2E des parcours complets (Android émulé)

**Status:** ready-for-agent
**Blocked by:** 11, 12, 13
**Type:** afk
**Lire uniquement :** spec.md (toutes les stories), lld.md §9 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** npm run test:e2e exécute en vert les 8 parcours bout en bout avec authentificateur virtuel et émulation Pixel 7, stables sur 3 exécutions.

**What to build:** suite Playwright sur base de test : (1) super-admin crée organisation + admin ; (2) admin crée un employé et génère le lien ; (3) employé enrôle son appareil ; (4) employé pointe Entrée puis Sortie ; (5) admin voit pointages, durées, tableau de bord et exporte le CSV ; (6) réinitialisation d'appareil ; (7) isolation entre deux organisations ; (8) mise en page `/pointer` à 412 px.

- [ ] Les 8 parcours verts et stables (3 exécutions consécutives)
- [ ] Captures des écrans clés conservées comme artefacts
- [ ] Job séparé dans le workflow CI
