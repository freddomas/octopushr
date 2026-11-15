# LLD — OctopusHR pointage

Complète [spec.md](./spec.md). Vocabulaire : `GLOSSARY.md`. Décisions : `docs/adr/`. Les tickets citent les sections par leur numéro (§).

## §1 Architecture

- Next.js (App Router, TypeScript strict, Node runtime, pas d'Edge pour les routes DB/WebAuthn), Tailwind CSS, composants maison inspirés AdminLTE (§6).
- Accès données : Drizzle ORM + `postgres` (driver) sur `DATABASE_URL` (pooler Supabase, mode transaction ⇒ pas de requêtes préparées). Migrations SQL générées par drizzle-kit, versionnées dans `drizzle/`.
- Couche d'accès unique `src/server/data/*` : **toute** fonction reçoit un `Scope` (`{ kind: "super" } | { kind: "org", orgId }`) et filtre par `org_id`. Aucune requête SQL hors de cette couche.
- Validation : zod sur toute entrée (route handlers / server actions). Réponses d'erreur JSON `{ error: { code, message } }`, messages en français.
- Tests : Vitest (unit + intégration sur Postgres de test), Playwright (E2E, authentificateur virtuel WebAuthn, émulation Pixel 7).
- Mise en page de dossiers (convention) : `src/app/(public)`, `src/app/(admin)/admin`, `src/app/(super)/super`, `src/app/pointer`, `src/app/enroll/[token]`, `src/app/api/*`, `src/server/{db,data,auth,webauthn}`, `src/components/ui`, `src/lib`.
- Pas de Supabase Auth, pas de client Supabase navigateur (ADR-0001). `NEXT_PUBLIC_SUPABASE_*` restent définies mais inutilisées.

## §2 Modèle de données (Postgres, toutes les tables : RLS activé, aucune policy)

Identifiants `uuid` (`gen_random_uuid()`), horodatages `timestamptz` UTC.

- `organizations`: id, name, slug (unique), timezone (IANA, défaut `Indian/Mauritius` modifiable à la création — défaut confirmé par l'utilisateur avant le ticket 06), active (bool), created_at.
- `admins`: id, org_id (null ⇔ super-administrateur ; FK), role (`super`|`admin`), username (unique global, minuscule), password_hash (argon2id), active, failed_attempts, locked_until, created_at. CHECK : `role='super'` ⇔ `org_id is null`.
- `employees`: id, org_id, full_name, employee_code (unique par org), status (`pending`|`active`|`disabled`), created_at. `pending` = pas encore d'appareil.
- `enrollment_tokens`: id, employee_id, token_hash (sha256, unique), expires_at (+48 h), used_at, created_at. Le jeton clair (32 octets base64url) n'est jamais stocké.
- `webauthn_credentials`: id, employee_id, credential_id (base64url, unique), public_key (bytea), counter (bigint), transports (text[]), created_at, revoked_at. Au plus 1 non révoqué par employé (index unique partiel).
- `webauthn_challenges`: id, challenge (unique), purpose (`enroll`|`punch`), employee_id (null pour `punch`), expires_at (+5 min), consumed_at.
- `punches`: id, org_id, employee_id, type (`in`|`out`), punched_at (défaut `now()` serveur), credential_id (FK), user_agent. Index (org_id, punched_at desc), (employee_id, punched_at desc).
- `rate_limits`: key (text, pk), window_start, count. Utilisé pour login admin, challenges, enrôlement.

Invariants : un employé `disabled`/`pending` ne peut pas pointer ; une organisation `active=false` bloque connexions admin et pointages de ses employés.

## §3 Authentification administrateur / super-administrateur

- `POST /api/admin/login` {username, password} → argon2id (`@node-rs/argon2`) ; temps constant si utilisateur inconnu (hash factice).
- Échecs : 5 consécutifs ⇒ `locked_until = now + 15 min` ; en plus, rate limit IP (10/10 min) via `rate_limits`. Message d'erreur identique pour inconnu/mauvais mot de passe.
- Session : JWT HS256 (`jose`) dans cookie `__Host-octo_session` (`HttpOnly; Secure; SameSite=Lax; Path=/`), durée 8 h, claims `{ sub, role, orgId }`, secret `SESSION_SECRET` (≥ 32 octets). Le rôle/org sont **rechargés en base à chaque requête** (désactivation immédiate effective).
- Middleware : `/admin/**` exige `role=admin`, `/super/**` exige `role=super`, sinon redirection `/login`. Les route handlers revérifient (défense en profondeur).
- Amorçage super-administrateur : script `npm run seed:super` lit `SUPER_ADMIN_USERNAME` / `SUPER_ADMIN_PASSWORD` (≥ 12 caractères), idempotent, jamais exposé en HTTP.
- CSRF : `SameSite=Lax` + vérification de l'en-tête `Origin` sur toute requête mutante.

## §4 Enrôlement employé (WebAuthn registration)

1. Admin : `POST /api/admin/employees/:id/enrollment-link` → crée un `enrollment_tokens` (invalide les précédents non utilisés), retourne `{ url: <APP_URL>/enroll/<token>, expiresAt }`. UI affiche le lien + QR code (côté client).
2. Employé ouvre `/enroll/[token]` (page publique, token vérifié par hash, non expiré, non utilisé, employé non `disabled`, organisation active). Affiche nom de l'employé + bouton « Enregistrer mon empreinte ».
3. `POST /api/enroll/options` {token} → options de création (`rpID=WEBAUTHN_RP_ID`, user.id = employee_id, `authenticatorSelection: { authenticatorAttachment: "platform", residentKey: "required", userVerification: "required" }`, `attestation: "none"`), défi stocké (`enroll`, lié à l'employé).
4. `POST /api/enroll/verify` {token, response} → vérifie défi (consommation atomique), origine (`WEBAUTHN_ORIGIN`), `userVerified=true` ; insère `webauthn_credentials`, marque le token `used_at`, passe l'employé en `active`. Transaction unique.
5. Erreurs gérées : jeton invalide/expiré/utilisé, appareil sans biométrie (message dédié), annulation utilisateur, navigateur non compatible.

## §5 Pointage (WebAuthn authentication)

1. `/pointer` (page publique, optimisée mobile, logo, gros bouton « Pointer »).
2. `POST /api/punch/options` → options d'authentification **sans allowCredentials** (credential découvrable), `userVerification: "required"`, défi `punch` stocké. Rate limit par IP.
3. `POST /api/punch/verify` {response} → retrouve le credential par `credential_id` (non révoqué), employé `active`, org `active` ; vérifie défi (consommé atomiquement), origine, RP ID, signature, `userVerified`, compteur strictement croissant (si l'authentificateur renvoie 0 de façon constante, tolérer 0→0 comme le permet la spec) ; met à jour le compteur.
4. Type du pointage (calculé côté serveur dans la même transaction, verrou sur l'employé) : dernier pointage de l'employé = `in` et plus récent que 16 h ⇒ `out` ; sinon ⇒ `in`. Si dernier = `in` et ≥ 16 h ⇒ nouvelle `in` (la précédente est une Sortie manquante).
5. Anti double-appui : si un pointage du même employé existe depuis < 10 s, ne pas en créer ; renvoyer le pointage existant avec `duplicate: true`.
6. Réponse : `{ employeeName, type, punchedAt (ISO UTC), orgTimezone, today: [{type, punchedAt}] }`. L'écran affiche en heure locale de l'organisation (`Intl.DateTimeFormat`).
7. Heure : uniquement `now()` serveur.

## §6 Interface (direction artistique)

Inspiration : openhr/openhr (AdminLTE 3). Tokens : barre latérale `#343a40`, entrée active `#007bff`, fond `#f4f6f9`, cartes blanches ombrées, tuiles « small-box » (vert `#28a745`, cyan `#17a2b8`, jaune `#ffc107`, bleu `#007bff`) avec icône et pied « Plus d'infos », texte `#212529`. Marque Manoir OCTOPUS : bleu dégradé `#00a8f0 → #0b2a8a` pour accents de marque (logo, bouton principal du pointage), or `#b8892b` pour détails (filets). Police : Source Sans 3 (comme AdminLTE).

- Logo : `docs/assets/logo-octopus-manoir.png` (fond blanc ; produire variantes recadrée/carrée, favicon, icônes PWA 192/512). Dans la barre latérale sombre, placer le logo sur une pastille/bandeau blanc.
- Page de connexion admin : carte centrée sur fond `#e9ecef`, logo au-dessus, champs identifiant/mot de passe avec icônes.
- Layout admin : sidebar (Tableau de bord, Pointages, Employés ; super : Organisations), topbar (burger, nom utilisateur, Déconnexion), responsive (sidebar repliable < 992 px).
- `/pointer` et `/enroll/[token]` : plein écran mobile, sans sidebar, gros boutons (≥ 56 px), états : prêt, vérification, succès (vert), erreur (rouge).
- `manifest.webmanifest` (nom « OctopusHR », `start_url=/pointer`, `display=standalone`) ; pas de service worker.
- Tout le texte est en français ; états vides, chargement et erreurs soignés.

## §7 Sécurité (exigences transverses)

- En-têtes : CSP stricte (pas d'inline hors nonce), HSTS, `X-Content-Type-Options`, `Referrer-Policy: same-origin`, `Permissions-Policy` (publickey-credentials-get/create autorisés pour self uniquement), `frame-ancestors 'none'`.
- Rate limiting (§2 `rate_limits`) : login admin, `/api/punch/options`, `/api/enroll/*`.
- Jetons à usage unique hachés ; défis à usage unique, 5 min.
- Pas de secrets dans le dépôt ni dans les logs ; pas de PII dans les logs.
- Messages d'erreur d'authentification non énumérants.
- `npm audit --omit=dev` sans vulnérabilité haute/critique ; contrôle de secrets (gitleaks ou équivalent) avant déploiement.
- Revue de sécurité par un agent dédié (skill `security-review`) + tests d'attaque inter-organisations (ticket 15).

## §8 Environnement et déploiement

Variables : `DATABASE_URL`, `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` (inutilisées), `SESSION_SECRET`, `WEBAUTHN_RP_ID`, `WEBAUTHN_ORIGIN`, `APP_URL`, `SUPER_ADMIN_USERNAME`, `SUPER_ADMIN_PASSWORD` (seed uniquement, retirée après usage). Vercel projet `octopushr`, production sur l'alias stable (`octopushr.vercel.app` par défaut) ; migrations appliquées par un script de déploiement explicite (jamais au build). Dépôt : `https://github.com/freddomas/octopushr.git`.

## §9 Conventions de test

- Postgres de test : schéma isolé par exécution (base Supabase de dev via `DATABASE_URL_TEST` ou Postgres local/Docker si disponible — à fixer au ticket 04), migrations appliquées au démarrage, fixtures `makeOrg`, `makeAdmin`, `makeEmployee`, `makePunch`.
- WebAuthn en test : authentificateur virtuel CDP Playwright (`ctap2`, `internal`, `hasUserVerification`, `isUserVerified`, `hasResidentKey`).
- Chaque ticket livre ses propres tests ; la suite complète doit rester verte (`npm test`, `npm run lint`, `npm run typecheck`, `npm run build`).
