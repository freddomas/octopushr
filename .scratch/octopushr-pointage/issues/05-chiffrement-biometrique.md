# 05: Chiffrement AES-GCM des données biométriques et recherche de PIN (HMAC)

**Status:** ready-for-agent
**Blocked by:** 04
**Type:** afk
**Modèle / effort :** opus / high
**Lire uniquement :** lld.md §5, ADR-0005 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** encrypt/decrypt round-trip, rotation de clé, falsification détectée et mauvaise donnée associée refusée : tests verts, aucune clé dans le dépôt.

**What to build:** module `src/server/crypto/biometric.ts` (AES-256-GCM, nonce aléatoire 12 octets, données associées = id de ligne, clés versionnées depuis `BIOMETRIC_KEYS`/`BIOMETRIC_KEY_VERSION`), helpers `encryptDescriptor`/`decryptDescriptor` (Float32Array 1024 ↔ blob) et `encryptImage`/`decryptImage`, fonction `pinLookup(orgId, pin)` = HMAC-SHA256 avec `PIN_PEPPER` (recherche par valeur, impossible avec un hash lent, inutile avec 10 000 valeurs), script `npm run keys:generate` qui affiche une clé base64 et un `PIN_PEPPER` (jamais écrits dans le dépôt).

- [ ] Round-trip descripteur (1024 flottants exacts) et image
- [ ] Blob modifié ⇒ erreur ; AAD différent ⇒ erreur ; mauvaise version de clé ⇒ erreur claire
- [ ] Deux chiffrements du même clair ⇒ blobs différents
- [ ] Rotation : écriture avec la version courante, lecture d'une ancienne version
- [ ] `pinLookup` déterministe, distinct par organisation (même PIN, deux orgs ⇒ deux valeurs), sensible au pepper
- [ ] Aucune donnée biométrique, PIN ni clé dans les logs ni les messages d'erreur
