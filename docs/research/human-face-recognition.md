# Recherche : @vladmandic/human pour la reconnaissance faciale

Vérifié le 2026-11-15 sur le dépôt GitHub, le wiki, le registre npm et le code de la démo `faceid`. À refaire si la version change.

## État du paquet
- Version npm `3.3.6`, publiée le 2025-08-26 : **aucune release depuis plus de 14 mois**. Licence MIT. Pas de mention « archivé » sur le dépôt. Risque de maintenance faible mais réel : épingler la version exacte, ne pas compter sur des correctifs.
- Taille décompressée du paquet npm : ~44 Mo (on n'embarque que le bundle navigateur et les modèles utiles).

## Modèles (fichiers `.json` + `.bin`, tailles du wiki)
BlazeFace front 0,4 Mo · FaceMesh 1,6 Mo · Iris 2,6 Mo · **FaceRes (descripteur) 6,8 Mo** · Antispoof 0,85 Mo · Liveness 0,6 Mo · Emotion 0,8 Mo (inutile, désactivé). Total utile ≈ 13 Mo. Source : dépôt `vladmandic/human-models`. Ils se servent localement via `modelBasePath` (défaut navigateur `../models/`).

## Configuration par défaut (src/config.ts)
- `face.antispoof.enabled = false`, `face.liveness.enabled = false` (à activer explicitement). `mesh`, `iris`, `description` (faceres), `emotion` actifs. `attention` désactivé. `detector.maxDetected = 1`, `rotation = false`.
- `backend` : `webgl` par défaut dans le navigateur. `modelBasePath` vide. `warmup: 'full'`. `filter.enabled = true`.
- Descripteur : tableau de **1024 flottants** (`result.face[i].embedding`). La doc recommande `face.mesh` et `face.detector.rotation` activés pour la précision et avertit que des images de mauvaise qualité donnent de faux résultats.
- Comparaison : `human.match.similarity(a, b, options)` (distance de Minkowski ordre 2) ; `human.match.find(embedding, base)`. La doc suggère > 50 % comme point de départ ; la démo utilise `{ order: 2, multiplier: 25, min: 0.2, max: 0.8 }` et un seuil de 0,5.

## Démo faceid (contrôles de validation)
`minConfidence 0.6` (confiance visage, antispoof « real », liveness « live »), `minSize 224` px, clignement valide si durée entre `10` et `800` ms, distance `0.4–1.0`, délai max 30 s. Ordre : un seul visage → clignement → visage et regard centrés (gestes `facing center`, `looking center`) → scores → taille → distance → descripteur présent. Stockage IndexedDB (descripteur + image).
La démo contient un bug d'index quand un enregistrement a un descripteur vide : ne pas copier la logique de correspondance telle quelle.

## Gestes
Disponibles : `blink left/right eye`, `facing left/center/right`, `head up/down`, `mouth open`, regard `looking left/right/up/down/center` (iris). « Regarde la caméra » = `facing center` + `looking center`. Aucune option de configuration documentée ; règles dans `src/gesture.ts`.

## Limites à connaître (conséquences pour le projet)
1. Les modèles `antispoof` (834 Ko) et `liveness` (32×32 px) sont petits et **passifs** : le wiki ne donne aucun chiffre d'exactitude. Une photo imprimée sera souvent détectée ; une **vidéo rejouée** d'une vraie personne qui cligne peut passer. Il faut donc des contrôles complémentaires : défi aléatoire (tourner la tête), pointeuse enregistrée (jeton d'appareil), photo conservée pour revue RH, seuils réglables sur le terrain.
2. Le descripteur et les scores de vivacité sont **calculés dans le navigateur** : le serveur ne peut pas les vérifier. Il applique les seuils et compare les descripteurs, mais un navigateur compromis pourrait mentir. La confiance repose sur la pointeuse enregistrée et sur la photo stockée.
3. Le serveur doit refaire le calcul de similarité à l'identique de `human.match.similarity` (porter la formule, test de parité contre la librairie en devDependency).
4. Charge : ~13 Mo de modèles au premier chargement ; le cache du service worker est indispensable (connexion lente). WebGL peut manquer sur de très vieux téléphones : prévoir le repli `wasm` et le mesurer.
