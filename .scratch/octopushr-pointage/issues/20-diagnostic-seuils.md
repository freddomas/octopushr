# 20: Écran de diagnostic : scores en direct et calibration des seuils

**Status:** ready-for-agent
**Blocked by:** 10, 13
**Type:** afk
**Lire uniquement :** spec.md stories 48, 49, lld.md §12 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Sur /admin/diagnostic, avec le moteur simulé puis les vrais modèles, tous les scores s'affichent en direct avec marquage vert/rouge par seuil, la similarité contre un employé choisi est calculée sans rien enregistrer, et « Utiliser ces seuils » pré-remplit les paramètres (tests verts).

**What to build:** `/admin/diagnostic` : flux caméra + panneau temps réel (nb visages, taille, confiance, antispoof, liveness, clignement en ms, gestes, distance, similarité vs employé choisi via `POST /api/admin/diagnostic/compare` qui ne stocke rien), indicateurs vert/rouge vs seuils actuels, historique des 20 dernières mesures avec min/médiane/max, bouton « Utiliser ces seuils » (pré-remplit `/admin/parametres`, ne sauvegarde pas), mesures de performance (chargement des modèles réseau vs cache, warm-up, ms/détection, ms/pointage complet de bout en bout) et export « Copier le rapport » (JSON).

- [ ] Aucun enregistrement en base ni photo envoyée au serveur par `compare` (descripteur seul, test)
- [ ] Scores mis à jour au moins 5 fois par seconde avec le moteur simulé
- [ ] Rapport JSON contient navigateur, backend (webgl/wasm), temps de chargement, ms/détection, ms/pointage
- [ ] Réservé aux admins ; isolation sur le choix de l'employé
- [ ] Français ; utilisable au pouce sur téléphone
