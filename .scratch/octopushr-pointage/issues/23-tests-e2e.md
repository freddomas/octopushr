# 23: Tests E2E des parcours complets (moteur simulé, Android émulé)

**Status:** ready-for-agent
**Blocked by:** 14, 15, 17, 18, 19, 20
**Type:** afk
**Lire uniquement :** spec.md (toutes les stories), lld.md §16 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** npm run test:e2e exécute en vert les parcours de bout en bout avec moteur simulé et émulation Pixel 7, stables sur 3 exécutions.

**What to build:** suite Playwright sur base de test : (1) super-admin crée organisation + admin ; (2) admin enregistre une pointeuse ; (3) admin crée et enrôle un employé (clavier PIN piloté par une source d'aléa injectable) ; (4) employé pointe Entrée puis Sortie ; (5) téléphone non enregistré refusé ; (6) mauvais visage refusé, 3 échecs puis validation sans visage « à vérifier » ; (7) pointage hors ligne puis synchronisation sans doublon ; (8) admin voit photos/scores, traite la file, consulte le journal, exporte le CSV ; (9) révocation de pointeuse ; (10) isolation entre deux organisations ; (11) refus de caméra : message clair ; (12) mise en page `/pointer` à 412 px.

- [ ] Les 12 parcours verts et stables (3 exécutions)
- [ ] Captures d'écran des écrans clés conservées comme artefacts
- [ ] Job séparé dans le workflow CI
