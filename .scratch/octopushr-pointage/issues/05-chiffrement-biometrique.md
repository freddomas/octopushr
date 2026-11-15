# 05: Chiffrement AES-GCM des données biométriques

**Status:** ready-for-agent
**Blocked by:** 04
**Type:** afk
**Lire uniquement :** lld.md §5, ADR-0005 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** encrypt/decrypt round-trip, rotation de clé, falsification détectée et mauvaise donnée associée refusée : tests verts, aucune clé dans le dépôt.

**What to build:** module `src/server/crypto/biometric.ts` (AES-256-GCM, nonce aléatoire 12 octets, données associées = id de ligne, clés versionnées depuis `BIOMETRIC_KEYS`/`BIOMETRIC_KEY_VERSION`), helpers `encryptDescriptor`/`decryptDescriptor` (Float32Array 1024 ↔ blob) et `encryptImage`/`decryptImage`, script `npm run keys:generate` qui affiche une clé base64 (jamais écrite dans le dépôt).

- [ ] Round-trip descripteur (1024 flottants exacts) et image
- [ ] Blob modifié ⇒ erreur ; AAD différent ⇒ erreur ; mauvaise version de clé ⇒ erreur claire
- [ ] Deux chiffrements du même clair ⇒ blobs différents
- [ ] Rotation : écriture avec la version courante, lecture d'une ancienne version
- [ ] Aucune donnée biométrique ni clé dans les logs ni les messages d'erreur
