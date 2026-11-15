# 12: Enrôlement du visage par l'administrateur (3–5 captures, qualité, PIN, consentement)

**Status:** ready-for-agent
**Blocked by:** 05, 08, 11
**Type:** afk
**Lire uniquement :** spec.md stories 8–15, lld.md §5, §6, ADR-0005 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec le moteur simulé, un admin enrôle un employé : consentement, 3 à 5 captures contrôlées, PIN choisi, profil chiffré en base et employé Actif ; des captures de qualité insuffisante ou de personnes différentes sont refusées ; réenrôler et supprimer fonctionnent (tests verts).

**What to build:** écran `/admin/employes/[id]/visage` et `POST`/`DELETE /api/admin/employees/:id/face` selon LLD §6 : case de consentement, captures guidées (face, gauche, droite, +2 optionnelles) avec contrôle qualité temps réel (un seul visage, taille, confiance, éclairage, netteté), saisie du PIN deux fois par l'employé, photo de référence, validation serveur (forme, cohérence entre captures), chiffrement (ticket 05), remplacement du profil précédent, suppression physique, journal d'audit. Boutons « Ré-enrôler » et « Supprimer le profil facial » sur la fiche employé.

- [ ] Sans consentement coché ⇒ enrôlement impossible (client et serveur)
- [ ] 3 à 5 captures ; moins de 3 ou plus de 5 ⇒ refus
- [ ] Captures de qualité insuffisante refusées avec message précis (taille, éclairage, flou, plusieurs visages, aucun visage)
- [ ] Cohérence : deux captures de personnes différentes ⇒ refus serveur
- [ ] Descripteurs et photo chiffrés en base (test : aucune valeur en clair)
- [ ] Ré-enrôlement : ancien profil `deleted_at`, un seul profil actif
- [ ] Suppression : templates et photo de référence supprimés physiquement, employé `pending_face`
- [ ] PIN : 4 chiffres, haché argon2id, jamais renvoyé
- [ ] Réservé aux admins de la même organisation (isolation testée) ; journalisation sans donnée biométrique
