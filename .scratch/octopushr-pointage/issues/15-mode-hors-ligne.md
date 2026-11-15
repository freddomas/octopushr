# 15: Mode hors ligne : file locale, synchronisation idempotente

**Status:** ready-for-agent
**Blocked by:** 13
**Type:** afk
**Lire uniquement :** spec.md stories 33–39, lld.md §9, ADR-0004 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec le réseau coupé (Playwright), un pointage est mis en file et affiché « sous réserve » ; au retour du réseau il est synchronisé sans doublon, même si l'envoi est rejoué ; un échec de comparaison devient « suspect » (tests verts).

**What to build:** `OfflineQueue` IndexedDB (descripteur du visage capté, photo, scores, `clientId` UUID, heure appareil), indicateur permanent `en ligne / hors ligne / N en attente`, écran « Enregistré hors ligne — sous réserve », `POST /api/punch/sync` (lots ≤ 20, idempotent sur `clientId`, statuts selon LLD §9, `clock_skew`), déclencheurs de synchro (`online`, ouverture, Background Sync, minuterie), gestion de la pointeuse révoquée (file conservée + bandeau), PIN masqué hors ligne avec message explicatif. Les descripteurs enrôlés ne sont jamais envoyés ni stockés sur le téléphone. Le PIN saisi est conservé dans la file chiffré avec une clé WebCrypto non extractable de l'appareil, supprimé après synchronisation et purgé après 7 jours.

- [ ] Hors ligne : contrôles de vivacité identiques, pointage en file, message « sous réserve »
- [ ] Synchro : même `clientId` envoyé deux fois ⇒ un seul pointage
- [ ] Échec de comparaison/scores/séquence/heure douteuse ⇒ `suspect` (pas supprimé), avec raison
- [ ] Succès ⇒ `ok` avec `offline = true` et `punched_at` = heure de l'appareil
- [ ] 403 `pointeuse_invalide` à la synchro ⇒ file conservée, bandeau visible
- [ ] Test : aucune donnée d'employé enrôlé (descripteurs) dans IndexedDB ni dans les réponses
- [ ] Indicateur correct dans les trois états ; reprise après redémarrage du navigateur
- [ ] PIN jamais en clair dans IndexedDB ; supprimé après synchro ; purge à 7 jours ; PIN inconnu à la synchro ⇒ pointage `suspect` sans employé
