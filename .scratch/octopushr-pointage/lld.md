# LLD — OctopusHR pointage par reconnaissance faciale

Complète [spec.md](./spec.md). Vocabulaire : `GLOSSARY.md`. Décisions : `docs/adr/`. Recherche Human : `docs/research/human-face-recognition.md`. Les tickets citent les sections par leur numéro (§).

## §1 Architecture

- Next.js (App Router, TypeScript strict, runtime Node pour toutes les routes DB), Tailwind, composants maison AdminLTE-like (§10). Déploiement Vercel (ADR-0001).
- Accès données : Drizzle ORM + `postgres` sur `DATABASE_URL` (pooler, pas de requêtes préparées). Migrations versionnées dans `drizzle/`. Couche unique `src/server/data/*` ; toute fonction reçoit un `Scope` (`{kind:"super"} | {kind:"org", orgId}`) et filtre par `org_id`.
- Validation zod de toute entrée. Erreurs JSON `{ error: { code, message } }`, messages en français.
- Client de pointage : `src/client/face/` avec l'interface **`FaceEngine`** (`load()`, `analyze(videoFrame) → FaceAnalysis`, `capture()`), implémentation `HumanEngine` (Human 3.3.6) et `ScriptedEngine` pour les tests (seam principal).
- Modèles : `public/models/` (versionnés dans le dépôt ou téléchargés par un script `npm run models:fetch` depuis `vladmandic/human-models` avec contrôle d'intégrité ; aucun CDN à l'exécution). Service worker (`public/sw.js`, cache versionné) précache coque d'appli + modèles utiles (blazeface, facemesh, iris, faceres, antispoof, liveness).
- Tests : Vitest (unit + intégration sur Postgres de test), Playwright (E2E, émulation Pixel 7, moteur simulé).
- Dossiers : `src/app/(public)`, `src/app/(admin)/admin`, `src/app/(super)/super`, `src/app/pointer`, `src/app/api/*`, `src/server/{db,data,auth,crypto,face}`, `src/client/{face,offline}`, `src/components/ui`, `src/lib`.

## §2 Modèle de données (Postgres ; RLS activé sur toutes les tables, aucune policy)

UUID `gen_random_uuid()` ; horodatages `timestamptz` UTC.

- `organizations`: id, name, slug (unique), timezone (défaut `Africa/Kinshasa`), active, created_at.
- `org_settings` (1:1, créée avec l'organisation) : `similarity_threshold` (0–1, défaut 0.5), `liveness_threshold` (défaut 0.6), `antispoof_threshold` (défaut 0.6), `min_face_size_px` (défaut 224), `face_failures_before_pin` (défaut 3), `pin_max_attempts` (défaut 5 PIN inconnus consécutifs avant blocage de la pointeuse), `pin_lock_minutes` (défaut 5), `missing_exit_after_hours` (défaut 16, 1–48), `photo_retention_days` (défaut 90, 7–730), `challenge_enabled` (défaut false : défi aléatoire « tournez la tête »), `updated_at`.
- `admins`: id, org_id (null ⇔ super), role (`super`|`admin`), username (unique), password_hash (argon2id), active, failed_attempts, locked_until, created_at.
- `employees`: id, org_id, full_name, status (`pending_face`|`active`|`disabled`), pin_lookup (HMAC-SHA256 du PIN, unique par organisation, null avant enrôlement), face_failures (échecs de reconnaissance consécutifs), consent_at, consent_admin_id, created_at. Pas de matricule : deux employés peuvent porter le même nom.
- `pointeuses`: id, org_id, site_name, token_hash (sha256, unique), registered_by_admin_id, registered_at, last_seen_at, revoked_at, locked_until (blocage après PIN inconnus), user_agent.
- `face_profiles`: id, employee_id, reference_photo_enc (bytea), key_version, enrolled_by_admin_id, enrolled_at, deleted_at. Unique partiel : 1 profil non supprimé par employé.
- `face_templates`: id, profile_id, idx (0–4), descriptor_enc (bytea : 1024 float32 chiffrés), key_version.
- `punches`: id (uuid, généré sur l'appareil = idempotence ; unique), org_id, employee_id (null si PIN inconnu), pointeuse_id, type (`in`|`out`), method (`face`|`pin`), status (`ok`|`to_review`|`suspect`|`validated`|`rejected`|`refused`), reasons (text[]), similarity, liveness, antispoof (real), offline (bool), device_time, received_at (`now()` serveur), punched_at (heure effective), photo_enc (bytea), key_version, reviewed_by, reviewed_at, review_comment. Index (org_id, punched_at desc), (employee_id, punched_at desc), (org_id, status).
- `audit_log`: id, org_id, admin_id, action, entity, entity_id, details (jsonb sans donnée biométrique), created_at.
- `rate_limits`: key (pk), window_start, count.

Invariants : un pointage n'est accepté que depuis une pointeuse valide de l'organisation ; employé `disabled`/`pending_face` ne peut pas pointer ; organisation inactive bloque tout.

## §3 Authentification administrateur / super-administrateur

- `POST /api/admin/login` {username, password} : argon2id, temps constant si inconnu, 5 échecs ⇒ `locked_until` +15 min, rate limit IP (10 / 10 min), message générique.
- Session JWT HS256 (`jose`) en cookie `__Host-octo_session` (HttpOnly; Secure; SameSite=Lax), 8 h, claims `{sub, role, orgId}` ; rôle/organisation **rechargés en base à chaque requête**. Secret `SESSION_SECRET` ≥ 32 octets.
- Middleware : `/admin/**` ⇒ role admin, `/super/**` ⇒ role super. Les handlers revérifient. Contrôle `Origin` sur toute requête mutante.
- `npm run seed:super` (variables `SUPER_ADMIN_USERNAME`/`SUPER_ADMIN_PASSWORD` ≥ 12 car.), idempotent, jamais via HTTP.

## §4 Pointeuses (ADR-0003)

- `POST /api/admin/pointeuses` {siteName} (session admin) : génère jeton 32 octets, stocke sha256, pose `__Host-octo_pointeuse` (HttpOnly; Secure; SameSite=Lax; Max-Age 1 an), **ferme la session admin** du téléphone, redirige `/pointer`. UI : « Enregistrer ce téléphone comme pointeuse » dans `/admin/pointeuses`.
- `requirePointeuse(req)` : cookie → hash → ligne non révoquée → organisation active ; met à jour `last_seen_at` ; sinon 403 `{code:"pointeuse_invalide"}` (message « Téléphone non enregistré »). Utilisé par `/api/pointeuse/config`, `/api/punch`, `/api/punch/sync`, `/api/punch/fallback`.
- `DELETE /api/admin/pointeuses/:id` ⇒ `revoked_at` (effet immédiat), trace dans `audit_log`.

## §5 Chiffrement des données biométriques (ADR-0005)

- `src/server/crypto/biometric.ts` : AES-256-GCM, clé `BIOMETRIC_KEYS` (JSON `{version: base64}`, version courante `BIOMETRIC_KEY_VERSION`), nonce 12 octets aléatoire, données associées = id de ligne. Format : `nonce || ciphertext || tag`. API : `encrypt(plain, aad)`, `decrypt(blob, aad, keyVersion)`. Rotation : nouvelles écritures avec la version courante, lecture de toutes versions.
- Descripteur = `Float32Array(1024)` ⇒ 4096 octets avant chiffrement. Les endpoints d'image : session admin de l'organisation, déchiffrement en mémoire, `Cache-Control: no-store`, `Content-Type: image/jpeg`.

- Recherche du PIN : `pinLookup(orgId, pin) = HMAC-SHA256(PIN_PEPPER, orgId‖':'‖pin)` (hex), colonne `employees.pin_lookup` unique par organisation. Un hash lent est impossible ici (recherche par valeur) et peu utile (10 000 valeurs).

## §6 Enrôlement du visage (admin, en présence de l'employé)

- Écran `/admin/employes/[id]/visage` (session admin ; tout appareil avec caméra, y compris la pointeuse après reconnexion admin). Étapes : (1) case de consentement obligatoire ; (2) 3 à 5 captures guidées (face, légère rotation gauche, droite, [+2]) ; (3) PIN : **généré par le système** (4 chiffres aléatoires, unique dans l'organisation, sans suites triviales) et affiché **une seule fois** à l'employé et à l'administrateur, avec « Régénérer » ; (4) récapitulatif.
- Contrôle qualité côté client à chaque capture : un seul visage ; taille ≥ `min_face_size_px` ; confiance détection ≥ 0.6 ; éclairage (luminance moyenne du visage dans [70, 190] et contraste suffisant) ; netteté (variance du Laplacien ≥ seuil) ; pas de clignement/bouche ouverte pendant la capture. Message précis si échec.
- `POST /api/admin/employees/:id/face` {descriptors: number[1024][3..5], referencePhoto (JPEG base64 ≤ 150 Ko), consent: true} : validation de forme (1024 finis, valeurs dans une plage plausible), **cohérence interne** (similarité entre chaque paire ≥ `similarity_threshold`, sinon refus « captures de personnes différentes ou trop dissemblables »), chiffrement, transaction : ancien profil `deleted_at`, nouveau profil + templates, `pin_lookup` (HMAC) généré côté serveur — la réponse renvoie le PIN en clair **une seule fois** —, employé `active`, `consent_at`. Journal.
- `DELETE /api/admin/employees/:id/face` : suppression physique des templates et de la photo de référence ; employé `pending_face`. Journal.

## §7 Pointage en ligne

1. `/pointer` (requiert pointeuse valide via `GET /api/pointeuse/config`, qui renvoie les seuils et paramètres utiles au client). Écran 1 : **Entrée** / **Sortie** (gros boutons). Écran 2 : clavier numérique large pour le **PIN** (4 chiffres, saisie masquée). Écran 3 : caméra frontale.
2. Caméra : `getUserMedia({video:{facingMode:"user"}})` uniquement ; aucune `<input type=file>` ; refus ⇒ écran explicatif « Autorisez la caméra… » et pointage impossible.
3. Boucle de validation (≤ 30 s, ~30 ms/itération), tous obligatoires : un seul visage ; taille ≥ `min_face_size_px` ; regard caméra (`facing center` + `looking center`) ; clignement valide (10–800 ms) ; confiance visage ≥ 0.6 ; `antispoof.real` ≥ `antispoof_threshold` ; `liveness.live` ≥ `liveness_threshold` ; distance 0.4–1.0. Si `challenge_enabled` : défi aléatoire (tourner à gauche ou à droite, détecté par `facing left/right`) avant la capture finale. Modules `antispoof` et `liveness` **activés explicitement** (désactivés par défaut dans Human).
4. Capture : descripteur (1024) + photo JPEG ~480 px (qualité adaptative < 50 Ko) + scores. `POST /api/punch` {clientId (uuid), type, pin, descriptor, photo, scores{liveness, antispoof, confidence}, blinkOk, challengeOk, deviceTime}.
5. Décision serveur (transaction, verrou sur l'employé) :
   a. `requirePointeuse`. b. Employé par `pin_lookup = HMAC(PIN_PEPPER, org‖pin)` ; PIN inconnu ou employé `disabled`/`pending_face` ⇒ tentative `refused` (photo + scores conservés, `employee_id` null), message générique « PIN ou visage non reconnu » (pas d'énumération) et compteur `pin-miss:{pointeuse}` : à `pin_max_attempts` PIN inconnus consécutifs la pointeuse est bloquée `pin_lock_minutes` (`pointeuses.locked_until`, message « Pointeuse bloquée, réessayez dans N min »). Un PIN reconnu remet le compteur à zéro.
   c. Vérifie `blinkOk`, scores ≥ seuils. d. similarité = max sur les templates déchiffrés (formule portée de `human.match.similarity`, voir §14) ≥ `similarity_threshold`. Échec ⇒ `refused`, `face_failures += 1`, la réponse indique `offerPin: face_failures >= face_failures_before_pin`.
   e. **Séquence** : dernier pointage accepté (`ok|validated|to_review`) de l'employé. `type = in` refusé si le dernier est `in` plus récent que `missing_exit_after_hours` ; `type = out` refusé s'il n'y a pas d'`in` ouvert. Refus ⇒ `refused` + raison `sequence`, message clair (« Vous avez déjà pointé votre entrée à HH:MM »).
   f. Succès ⇒ `status ok`, `face_failures = 0`, `punched_at = received_at`.
   g. Réponse : `{status, type, employeeName, punchedAt, orgTimezone, offerPin}` ; l'heure affichée est celle du serveur.
6. Anti double-appui : même employé, même type, < 10 s ⇒ renvoie le pointage existant.
7. Rate limit par pointeuse + IP.

## §8 Secours sans visage

- Proposé après `face_failures_before_pin` échecs consécutifs de **ce** employé (déjà identifié par son PIN). `POST /api/punch/fallback` {clientId, type, pin, photo, scores?, deviceTime} : photo **obligatoire** ; employé retrouvé par `pin_lookup` ; refusé si `face_failures < face_failures_before_pin` ; séquence identique §7.e ; succès ⇒ `status to_review`, raison `pin_fallback`, méthode `pin`.
- Blocage : `pin_max_attempts` PIN inconnus consécutifs depuis une pointeuse (reconnaissance ou secours) ⇒ pointeuse bloquée `pin_lock_minutes` ; remise à zéro par un PIN reconnu.
- Admin : `POST /api/admin/employees/:id/pin-reset` génère un nouveau PIN unique aléatoire (affiché une seule fois, jamais relisible), invalide l'ancien, remet `face_failures` à zéro. Journalisé.
- Limite assumée (ADR-0002) : le PIN est identifiant **et** secours ; un collègue qui connaît le PIN d'un autre peut, après 3 échecs volontaires, pointer à sa place sous statut « à vérifier » avec sa propre photo. 4 chiffres = 10 000 PIN maximum par organisation.
- Indisponible hors ligne (ADR-0004).

## §9 Mode hors ligne (ADR-0004)

- Service worker : précache coque + modèles ; stratégie « cache d'abord » pour les modèles (versionnés), « réseau d'abord avec repli » pour l'API de configuration (dernière config en cache).
- `OfflineQueue` (IndexedDB) : `{clientId, type, pin (chiffré par une clé WebCrypto non extractable, supprimé après synchro, purgé après 7 jours), descriptor:Float32Array, photo:Blob, scores, blinkOk, challengeOk, deviceTime, tries}`.
- Hors ligne : mêmes contrôles de vivacité ; l'écran dit « Enregistré hors ligne — sous réserve » ; indicateur permanent `en ligne | hors ligne | N en attente`.
- Synchro : `POST /api/punch/sync` {items[≤20]} idempotent sur `clientId` (déjà reçu ⇒ renvoie l'état existant). Le serveur applique §7 c–e **sans refuser** : échec de comparaison ⇒ `suspect` (raison `face_mismatch`), séquence incohérente ⇒ `suspect` (`sequence`), scores sous seuil ⇒ `suspect`, `deviceTime` dans le futur (> 5 min) ou > 7 jours ⇒ `suspect` (`clock_skew`) ; sinon `ok` avec `offline = true`. `punched_at = deviceTime`.
- Déclencheurs : événement `online`, ouverture de `/pointer`, Background Sync si disponible, toutes les 60 s si file non vide. 403 `pointeuse_invalide` ⇒ file conservée, bandeau « Pointeuse révoquée ».

## §10 Interface (direction artistique)

Inspiration openhr/openhr (AdminLTE 3). Tokens : sidebar `#343a40`, actif `#007bff`, fond `#f4f6f9`, cartes blanches, tuiles « small-box » (vert `#28a745`, cyan `#17a2b8`, jaune `#ffc107`, bleu `#007bff`), police Source Sans 3. Marque Manoir OCTOPUS : dégradé `#00a8f0 → #0b2a8a`, or `#b8892b`. Logo `docs/assets/logo-octopus-manoir.png` (fond blanc ; variantes recadrée/carrée/favicon/icônes PWA ; pastille blanche sur fond sombre).
- Admin : sidebar (Tableau de bord, Pointages, À vérifier, Employés, Pointeuses, Paramètres, Diagnostic, Journal ; super : Organisations), topbar, responsive.
- `/pointer` : plein écran mobile, sans sidebar, boutons ≥ 56 px, états clairs (succès vert, refus rouge, sous réserve ambre), retour visuel sur le visage (cadre qui change de couleur, consignes textuelles).
- `manifest.webmanifest` (start_url `/pointer`, standalone) ; service worker (§1).
- Texte entièrement en français.

## §11 Écran RH et revue

- `/admin/pointages` : tableau paginé (50/p) : employé, type, heure locale, site (pointeuse), méthode, statut, scores, miniatures **photo captée | photo de référence** côte à côte (clic ⇒ grand format), durée de session, badge « Sortie manquante ». Filtres date, employé, statut, méthode.
- `/admin/a-verifier` : file des statuts `to_review` et `suspect` ; actions **Valider** / **Rejeter** avec commentaire obligatoire (≥ 5 car.) ⇒ `validated`/`rejected` (un pointage rejeté n'est pas compté), `reviewed_by/at`, ligne `audit_log` (avant/après, commentaire).
- `/admin/journal` : `audit_log` paginé et filtrable (action, admin, date). Actions tracées : validate, reject, enroll_face, delete_face, pin_reset, register_pointeuse, revoke_pointeuse, settings_update, consent.
- Tableau de bord : tuiles Présents maintenant, Pointages aujourd'hui, À vérifier, Suspects ; derniers pointages.
- Export CSV (`;`, BOM UTF-8, neutralisation des formules) sans photos.

## §12 Paramètres et diagnostic

- `/admin/parametres` : tous les champs de `org_settings` (validation par bornes, effet immédiat, journalisé). Aucun redéploiement.
- `/admin/diagnostic` (caméra) : flux direct + panneau de scores en temps réel (nb visages, taille, confiance, antispoof, liveness, clignement ms, gestes, distance, similarité contre un employé choisi via `POST /api/admin/diagnostic/compare` **sans rien enregistrer**), marquage vert/rouge par seuil, boutons « Utiliser ces seuils » (pré-remplit Paramètres) et « Copier le rapport ». Mesures : temps de chargement des modèles (réseau vs cache), warm-up, ms/détection, ms/pointage complet ; export JSON.

## §13 Sécurité (transverse)

- En-têtes : CSP stricte (`script-src 'self' 'wasm-unsafe-eval'` si repli wasm, `worker-src 'self'`, `connect-src 'self'`, `img-src 'self' data: blob:`, `media-src 'self' blob:`), HSTS, nosniff, `Referrer-Policy: same-origin`, `Permissions-Policy: camera=(self)`, `frame-ancestors 'none'`.
- Rate limiting : login admin, `/api/punch*`, enregistrement de pointeuse.
- Jetons hachés ; secrets uniquement en variables d'environnement ; pas de PII ni de biométrie dans les logs ni dans `audit_log.details`.
- Purge : tâche Vercel Cron quotidienne supprimant les `punches.photo_enc` plus vieux que `photo_retention_days` (la ligne reste, photo supprimée).
- `npm audit --omit=dev` sans haute/critique ; scan de secrets ; revue `security-review` + tests d'attaque (isolation, pointage sans jeton, jeton révoqué, rejeu `clientId`, énumération de PIN par essais, surcharge de payload, PIN force brute).

## §14 Similarité côté serveur

`src/server/face/similarity.ts` porte l'algorithme de `human.match.similarity` (distance de Minkowski ordre 2 entre deux `Float32Array(1024)`, puis normalisation `multiplier 25`, bornes `min 0.2`, `max 0.8` de la démo faceres) ; **test de parité** contre `@vladmandic/human` (devDependency) sur des vecteurs aléatoires et réels (tolérance 1e-6). La similarité d'un pointage = max sur les templates du profil.

## §15 Environnement et déploiement

Variables : `DATABASE_URL`, `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` (inutilisées), `SESSION_SECRET`, `BIOMETRIC_KEYS`, `BIOMETRIC_KEY_VERSION`, `APP_URL`, `CRON_SECRET`, `PIN_PEPPER`, `SUPER_ADMIN_USERNAME`, `SUPER_ADMIN_PASSWORD` (seed uniquement). Les variables `WEBAUTHN_*` sont supprimées. Vercel projet `octopushr` ; migrations par script explicite, jamais au build ; protection SSO Vercel à désactiver en production ; domaine de production stable. Dépôt `https://github.com/freddomas/octopushr.git`.

## §16 Conventions de test

- Postgres de test : schéma isolé par exécution, fixtures `makeOrg`, `makeAdmin`, `makeEmployee`, `makeFaceProfile`, `makePointeuse`, `makePunch`.
- `ScriptedEngine` : produit des `FaceAnalysis` prédéfinis (descripteur, scores, gestes) pour piloter l'UI et l'API de bout en bout sans modèle.
- Quelques tests « vrais modèles » (lents, tag `@models`) sur images fixtures **libres de droits ou synthétiques** (licence consignée) : même personne ⇒ accepté, personnes différentes ⇒ refusé.
- Cas non automatisables (photo imprimée, vidéo rejouée, éclairage, performance téléphone) ⇒ procédure manuelle (ticket 25) + diagnostic (ticket 20).
- `npm test`, `npm run lint`, `npm run typecheck`, `npm run build` doivent rester verts.
