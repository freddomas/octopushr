# 11: Socle reconnaissance faciale : Human local, modèles, service worker, FaceEngine, métriques

**Status:** ready-for-agent
**Blocked by:** 03
**Type:** afk
**Lire uniquement :** lld.md §1, §10 (service worker), §16, ADR-0002, docs/research/human-face-recognition.md (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Une page de test charge Human avec les modèles servis localement (aucune requête vers un CDN), détecte un visage fixture et renvoie un descripteur de 1024 valeurs ; le second chargement se fait depuis le cache du service worker hors ligne ; les temps de chargement sont mesurés (tests verts).

**What to build:** (1) dépendance `@vladmandic/human` **3.3.6 épinglée** ; (2) script `npm run models:fetch` qui récupère depuis `vladmandic/human-models` les seuls modèles utiles (blazeface, facemesh, iris, faceres, antispoof, liveness) vers `public/models/` avec somme de contrôle ; (3) interface `FaceEngine` + `HumanEngine` (configuration : `modelBasePath` local, mesh, iris, description, **antispoof et liveness activés**, gestes, filtre d'égalisation, backend `webgl` avec repli `wasm`) + `ScriptedEngine` pour les tests ; (4) service worker `public/sw.js` précachant coque + modèles (cache versionné, mise à jour propre) et son enregistrement ; (5) module `metrics` (temps de chargement des modèles réseau vs cache, warm-up, ms/détection) ; (6) page `/dev/face` (hors production) pour essayer. **Avant de coder, relire la documentation à jour de Human** (version, configuration, démo faceid) et noter tout écart avec `docs/research/human-face-recognition.md`.

- [ ] Aucune requête réseau sortante vers un CDN (test Playwright qui l'interdit)
- [ ] Modèles servis avec en-têtes de cache longs et noms versionnés ; mise à jour du cache sans perte hors ligne
- [ ] Chargement hors ligne OK après un premier chargement (Playwright, mode hors ligne)
- [ ] `HumanEngine` renvoie descripteur 1024, scores antispoof/liveness, gestes, taille, nb de visages sur image fixture (licence consignée)
- [ ] `ScriptedEngine` permet de piloter l'UI sans modèles
- [ ] Repli `wasm` si WebGL indisponible ; backend utilisé exposé dans les métriques
- [ ] Taille totale des modèles et du bundle consignée dans `docs/research/` (mesure de départ)
