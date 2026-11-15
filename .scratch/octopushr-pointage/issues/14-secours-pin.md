# 14: Secours PIN après 3 échecs, blocage et réinitialisation

**Status:** ready-for-agent
**Blocked by:** 13
**Type:** afk
**Lire uniquement :** spec.md stories 29–32, lld.md §8 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Après 3 échecs de reconnaissance l'écran propose le PIN ; un PIN correct enregistre un pointage « à vérifier » avec photo ; 5 PIN faux bloquent l'employé ; l'admin débloque (tests verts).

**What to build:** compteur d'échecs consécutifs (`employees.face_failures`) ; écran PIN sur `/pointer` après `face_failures_before_pin` échecs ; `POST /api/punch/pin` selon LLD §8 (photo obligatoire, séquence identique, statut `to_review`, raison `pin_fallback`) ; blocage `pin_locked_until` après `pin_max_attempts` ; action admin « Réinitialiser le PIN / débloquer » sur la fiche employé ; messages clairs.

- [ ] PIN proposé seulement après N échecs ; succès du visage remet le compteur à zéro
- [ ] PIN correct ⇒ pointage `to_review` + `pin_fallback`, photo stockée ; sans photo ⇒ refus
- [ ] PIN faux ×5 ⇒ blocage 15 min (paramétrable), message clair, même avec le bon PIN pendant le blocage
- [ ] PIN comparé en temps constant (argon2id), jamais loggé
- [ ] Séquences incohérentes refusées comme en §7
- [ ] Réinitialisation admin (isolation testée) ; journalisée
- [ ] PIN indisponible hors ligne (le ticket 15 l'affichera)
