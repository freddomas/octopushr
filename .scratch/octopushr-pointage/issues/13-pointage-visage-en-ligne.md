# 13: Pointage en ligne par visage (PIN, vivacité, décision serveur)

**Status:** ready-for-agent
**Blocked by:** 09, 10, 12
**Type:** afk
**Lire uniquement :** spec.md stories 16–28, lld.md §7, §14, ADR-0002 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec le moteur simulé, un employé enrôlé pointe Entrée puis Sortie depuis une pointeuse valide ; les cas refusés (pointeuse invalide, mauvais visage, scores sous seuil, séquence incohérente, PIN inconnu) sont refusés côté serveur avec photo et scores conservés ; quelques tests avec les vrais modèles passent (tests verts).

**What to build:** `/pointer` (écrans Entrée/Sortie → clavier PIN masqué à disposition aléatoire (nouveau tirage à chaque affichage et après chaque erreur) → caméra), boucle de validation (un visage, taille, regard, clignement, antispoof, liveness, distance ; défi aléatoire si `challenge_enabled`), capture (descripteur + photo ~480 px < 50 Ko), `GET /api/pointeuse/config`, `POST /api/punch` avec décision serveur selon LLD §7 (similarité portée de Human + test de parité §14, seuils d'organisation, séquence, anti double-appui, tentatives `refused` conservées), écrans de résultat (succès avec nom/type/heure locale, refus avec raison claire), écran d'erreur caméra refusée. Aucune sélection de fichier possible.

- [ ] Pointeuse invalide/révoquée ⇒ 403 « Téléphone non enregistré », aucun traitement
- [ ] Bon PIN + bon visage ⇒ `ok` ; bon PIN + visage d'un autre employé ⇒ `refused` (photo + scores conservés)
- [ ] Un scores antispoof/liveness/clignement sous seuil ⇒ `refused`
- [ ] Séquence : Entrée après Entrée récente ⇒ refus clair ; Sortie sans Entrée ⇒ refus clair ; Entrée après Entrée plus vieille que le seuil ⇒ acceptée
- [ ] Le clavier PIN redistribue les chiffres à chaque affichage (nouveau pointage, nouvel essai, retour après erreur) ; le PIN saisi n'est jamais affiché en clair
- [ ] PIN inconnu ⇒ message générique, tentative conservée (`employee_id` null) ; `pin_max_attempts` PIN inconnus consécutifs ⇒ pointeuse bloquée `pin_lock_minutes`
- [ ] Le serveur ne fait jamais confiance à un « succès » envoyé par le client : il recalcule similarité et vérifie les seuils
- [ ] Test de parité `similarity.ts` vs `human.match.similarity` (tolérance 1e-6)
- [ ] Aucun `<input type=file>` ni accès galerie ; refus de caméra ⇒ message et pointage impossible
- [ ] Photo conservée < 50 Ko, chiffrée ; réponse sans donnée biométrique
- [ ] Tests « vrais modèles » (tag `@models`) : même personne ⇒ accepté, personnes différentes ⇒ refusé, sur images fixtures libres de droits
