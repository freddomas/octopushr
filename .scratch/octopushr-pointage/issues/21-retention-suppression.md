# 21: Rétention des photos et suppression des données biométriques

**Status:** ready-for-agent
**Blocked by:** 13, 10
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** spec.md stories 14, 50, lld.md §13 (purge), ADR-0005 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Une tâche planifiée supprime les photos de tentative plus vieilles que la rétention de l'organisation sans supprimer les lignes de pointage ; la suppression d'un profil facial efface physiquement descripteurs et photo de référence (tests verts).

**What to build:** endpoint `GET /api/cron/purge-photos` protégé par `CRON_SECRET` (en-tête `Authorization: Bearer`), configuré dans `vercel.json` (quotidien), supprimant `photo_enc` des `punches` plus vieux que `photo_retention_days` par organisation (la ligne et les scores restent) ; vérification que la suppression de profil (ticket 12) est physique ; page admin affichant la dernière purge et la rétention courante.

- [ ] Photo supprimée à J+rétention ; ligne de pointage conservée ; photo de référence non touchée
- [ ] Endpoint refuse sans `CRON_SECRET` valide
- [ ] Respect du paramètre par organisation (deux organisations, deux durées)
- [ ] Idempotent ; traite par lots sans dépasser le temps de la fonction
- [ ] Journal de purge (compte, pas de donnée biométrique)
