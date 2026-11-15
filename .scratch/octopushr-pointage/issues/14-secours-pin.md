# 14: Secours sans visage après 3 échecs, blocage de pointeuse, régénération du PIN

**Status:** ready-for-agent
**Blocked by:** 13
**Type:** afk
**Modèle / effort :** opus / high
**Lire uniquement :** spec.md stories 29–32, lld.md §8 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Après 3 échecs de reconnaissance l'écran propose de valider sans visage ; le pointage est enregistré « à vérifier » avec photo ; 5 PIN inconnus consécutifs bloquent la pointeuse ; l'admin régénère un PIN (tests verts).

**What to build:** compteur d'échecs consécutifs (`employees.face_failures`) ; après `face_failures_before_pin` échecs l'écran propose « Pointer sans reconnaissance (à vérifier) » (le PIN saisi au départ a déjà identifié l'employé) ; `POST /api/punch/fallback` selon LLD §8 (photo obligatoire, accepté seulement si le compteur le permet, séquence identique, statut `to_review`, raison `pin_fallback`, méthode `pin`) ; blocage de la pointeuse (`pointeuses.locked_until`) après `pin_max_attempts` PIN inconnus consécutifs, message « Pointeuse bloquée, réessayez dans N min » ; action admin « Régénérer le PIN » sur la fiche employé (nouveau PIN unique aléatoire affiché une seule fois, ancien invalidé, compteur remis à zéro).

- [ ] Secours proposé seulement après N échecs ; succès du visage remet le compteur à zéro ; appel direct du secours sans échecs préalables ⇒ refus serveur
- [ ] Secours accepté ⇒ pointage `to_review` + `pin_fallback`, photo stockée ; sans photo ⇒ refus
- [ ] 5 PIN inconnus consécutifs depuis une pointeuse ⇒ blocage (5 min par défaut, paramétrable), message clair ; un PIN reconnu remet le compteur à zéro
- [ ] Le PIN n'apparaît jamais dans les logs ni dans les réponses (sauf régénération, une seule fois)
- [ ] Séquences incohérentes refusées comme en §7
- [ ] Régénération du PIN : unique dans l'organisation, ancien PIN invalide, isolation testée, journalisée
- [ ] Secours indisponible hors ligne (le ticket 15 l'affichera)
