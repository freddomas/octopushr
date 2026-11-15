# 18: Appareil unique de l'organisation : activation et verrouillage du pointage

**Status:** ready-for-agent
**Blocked by:** 05
**Type:** afk
**Lire uniquement :** spec.md stories 40–42, 45, lld.md §2 (kiosk_devices, device_activation_codes), §5bis, ADR-0004 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Après activation par l'admin, seul le navigateur qui a saisi le code reçoit le cookie d'appareil et accède à /pointer et /enroll ; tout autre navigateur reçoit « Appareil non autorisé », et une nouvelle activation révoque l'ancien appareil (tests verts).

**What to build:** page admin `/admin/appareil` (statut : aucun appareil / actif depuis, bouton « Activer un appareil » qui génère un code à 8 caractères + QR valable 15 min, bouton « Remplacer l'appareil » avec confirmation), page publique `/activate` (saisie ou scan du code) et `POST /api/device/activate` selon LLD §5bis, helper serveur `requireKioskDevice()` réutilisable par les routes de pointage/enrôlement, écran « Appareil non autorisé » (403 générique, français). Les tickets 08 et 09 consomment ce helper.

- [ ] Code d'activation : aléatoire, haché, 15 min, usage unique, rate-limité ; rejeu/expiré ⇒ erreur claire
- [ ] Jeton d'appareil 32 octets, haché en base ; cookie `__Host-octo_device` HttpOnly, Secure, SameSite=Lax, 1 an
- [ ] Au plus un appareil actif par organisation (index unique) ; réactivation = révocation de l'ancien dans une transaction
- [ ] `requireKioskDevice()` : cookie absent/inconnu/révoqué, ou organisation inactive ⇒ 403 générique ; testé sur une route d'exemple
- [ ] Un admin ne peut agir que sur l'appareil de sa propre organisation (isolation testée)
- [ ] Page `/admin/appareil` en français, états vide/erreur ; entrée « Appareil » dans la sidebar
