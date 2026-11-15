# 10: Admin : tableau des pointages de l'organisation (filtres, durées)

**Status:** ready-for-agent
**Blocked by:** 05
**Type:** afk
**Lire uniquement :** spec.md stories 24–27, 30, 31, lld.md §2 (punches), §6 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec des pointages de fixture sur deux organisations, un admin voit uniquement ceux de la sienne dans /admin/pointages, filtrés par période/employé/type, en heure locale, avec durées et sorties manquantes (tests verts).

**What to build:** page `/admin/pointages` : tableau paginé (employé, matricule, type, date/heure dans le fuseau de l'organisation, durée de session sur les lignes Sortie, badge « Sortie manquante » sur les Entrées ouvertes ≥ 16 h), filtres période (défaut : aujourd'hui + 6 jours précédents), employé, type, tri anti-chronologique, responsive (cartes empilées < 576 px). Données de test insérées via fixtures (aucune dépendance au ticket 09).

- [ ] Requête scopée par org ; fuseau appliqué (changement de jour testé)
- [ ] Durée = Entrée→Sortie suivante du même employé ; Sortie manquante détectée
- [ ] Filtres combinables, état vide soigné, pagination serveur (50/page)
- [ ] Test d'isolation : aucune ligne d'une autre organisation, même en forçant un `employeeId` étranger
- [ ] Entrée « Pointages » dans la sidebar ; interface française
