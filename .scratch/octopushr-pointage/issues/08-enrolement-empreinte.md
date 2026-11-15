# 08: Enrôlement : lien/QR admin + enregistrement de l'empreinte par l'employé

**Status:** ready-for-agent
**Blocked by:** 07, 18
**Type:** afk
**Lire uniquement :** spec.md stories 1–3, 11, 20, lld.md §4, ADR-0002 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec l'authentificateur virtuel Playwright, un lien généré par l'admin permet à l'employé d'enregistrer son empreinte, le lien devient inutilisable et l'employé passe Actif (test E2E vert).

**What to build:** bouton « Générer le lien d'enrôlement » sur l'employé (lien + QR code + expiration + copier), page `/enroll/[token]` plein écran mobile, utilisable uniquement sur l'appareil activé (ticket 18), endpoints `POST /api/enroll/options` et `POST /api/enroll/verify` selon LLD §4 avec `@simplewebauthn`. Chaque cas d'erreur a un écran en français.

- [ ] Jeton 32 octets aléatoires, stocké haché (sha256), expire à 48 h, usage unique ; un nouveau lien invalide les précédents non utilisés
- [ ] Options : plateforme uniquement, `userVerification: required`, credential découvrable, `attestation: none`
- [ ] Vérification : défi consommé atomiquement, origine et RP ID contrôlés, `userVerified` exigé ; credential + passage `active` + jeton consommé dans une transaction
- [ ] Écrans : jeton invalide/expiré/utilisé, employé désactivé, WebAuthn non supporté, aucune biométrie configurée, annulation
- [ ] Rate limit sur `/api/enroll/*` ; sans cookie d'appareil valide ⇒ écran « Appareil non autorisé » (403 générique)
- [ ] Plusieurs employés peuvent enregistrer leur empreinte sur le même appareil (test)
- [ ] Test E2E Playwright (authentificateur virtuel) : enrôlement réussi ; rejouer le lien échoue
