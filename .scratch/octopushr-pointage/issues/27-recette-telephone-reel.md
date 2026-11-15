# 27: Recette sur téléphone réel : cas manuels et mesures de performance

**Status:** ready-for-human
**Blocked by:** 26
**Type:** human
**Lire uniquement :** docs/test-manuel-pointeuse.md (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le rapport de recette (tous les cas de docs/test-manuel-pointeuse.md + temps de chargement et temps par pointage) est rempli sur le téléphone réel et les seuils sont réglés ; décision d'acceptabilité prise.

**What to build:** l'utilisateur exécute la procédure du ticket 25 sur le téléphone de la pointeuse, règle les seuils avec l'écran de diagnostic, renvoie le rapport ; l'orchestrateur analyse les temps (chargement des modèles, pointage) et conclut sur leur acceptabilité, puis ouvre des tickets de correction si besoin (seuils, repli wasm, taille de modèles, défi aléatoire à activer).

- [ ] Les 11 cas manuels exécutés avec résultat noté
- [ ] Temps de chargement (1er chargement et depuis le cache) et temps par pointage mesurés sur le téléphone réel
- [ ] Seuils finaux enregistrés dans `/admin/parametres`
- [ ] Décision écrite : acceptable / à améliorer (avec actions)
