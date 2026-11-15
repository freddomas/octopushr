# 13: Admin : réinitialiser l'appareil d'un employé

**Status:** ready-for-agent
**Blocked by:** 08
**Type:** afk
**Lire uniquement :** spec.md stories 15, 23, lld.md §2, §4 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Après réinitialisation, l'ancien appareil ne peut plus pointer, l'employé repasse En attente et un nouveau lien d'enrôlement est généré (test E2E vert).

**What to build:** action « Réinitialiser l'appareil » sur un employé (avec confirmation) : révoque le credential actif (`revoked_at`), passe l'employé en `pending`, invalide les liens non utilisés et génère un nouveau lien/QR. L'historique des pointages est conservé.

- [ ] Ancien credential refusé au pointage (message dédié)
- [ ] Nouveau lien fonctionnel ; ancien lien invalide
- [ ] Action limitée aux employés de l'organisation de l'admin (isolation testée)
- [ ] Confirmation avant action ; message de succès
