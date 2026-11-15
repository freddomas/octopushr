# 25: Procédure de test manuel sur téléphone et gabarit de rapport de mesures

**Status:** ready-for-agent
**Blocked by:** 20, 15, 14
**Type:** afk
**Modèle / effort :** sonnet / low
**Lire uniquement :** spec.md (Testing Decisions), lld.md §12, §16 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le document docs/test-manuel-pointeuse.md se suit pas à pas sur le téléphone, couvre tous les cas demandés et fournit un gabarit de rapport (scores, temps, verdict) ; un relecteur indépendant confirme qu'aucune étape n'est ambiguë.

**What to build:** `docs/test-manuel-pointeuse.md` : pré-requis (téléphone, Chrome, lien de production, compte admin de test, 2 employés de test), mise en place guidée (enregistrer la pointeuse, enrôler 2 employés avec l'écran de diagnostic ouvert), puis les cas : appareil non enrôlé (refusé) ; bon PIN + bon visage (accepté) ; bon PIN + visage d'un autre employé (refusé) ; photo imprimée du bon employé (refusé) ; vidéo du bon employé rejouée sur un autre téléphone (refusé ou signalé) ; faible éclairage ; contre-jour ; 3 échecs puis validation sans visage (accepté, « à vérifier ») ; pointage hors ligne puis synchronisation sans doublon ; refus d'accès caméra ; clavier PIN redistribué à chaque saisie. Pour chaque cas : étapes, résultat attendu, où lire les scores, comment ajuster les seuils. Section mesures : temps de chargement des modèles (1er chargement sur réseau lent, puis cache), temps par pointage (PIN → confirmation), critères d'acceptabilité proposés (ex. 1er chargement < 60 s sur réseau lent, pointage < 8 s) et gabarit de rapport JSON/tableau à renvoyer.

- [ ] Tous les cas listés dans la demande sont présents avec résultat attendu
- [ ] Règles de réglage des seuils expliquées (compromis faux rejets / fausses acceptations)
- [ ] Critères d'acceptabilité de performance explicites et modifiables
- [ ] Gabarit de rapport prêt à remplir
