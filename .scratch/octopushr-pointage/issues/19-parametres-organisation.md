# 19: Admin : paramètres de l'organisation (seuil de Sortie manquante)

**Status:** ready-for-agent
**Blocked by:** 05
**Type:** afk
**Lire uniquement :** spec.md stories 43–44, lld.md §2 (organizations), §5 (étape 4), §5bis (paramètres) (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un admin de fixture modifie le seuil de Sortie manquante de son organisation (1–48 h), la valeur est persistée et relue par les règles de pointage ; une valeur hors bornes ou une autre organisation est refusée (tests verts).

**What to build:** page `/admin/parametres` avec le champ « Seuil de Sortie manquante (heures) » (défaut 16, entier 1–48), texte d'aide expliquant l'effet (une Entrée sans Sortie depuis plus que ce seuil est signalée « Sortie manquante » et le pointage suivant redevient une Entrée), enregistrement via `PUT /api/admin/settings` ; la lecture du paramètre passe par `getOrgSettings(scope)` fourni par le ticket 04. Le changement ne réécrit pas l'historique.

- [ ] Validation zod 1–48 entier ; message d'erreur en français
- [ ] Persisté sur `organizations.missing_exit_after_hours` ; relu à chaque usage (pas de cache périmé)
- [ ] Isolation : un admin ne modifie que sa propre organisation (404 sinon) ; un super-admin n'a pas besoin d'y accéder
- [ ] Contrôle `Origin` sur la requête mutante
- [ ] Entrée « Paramètres » dans la sidebar ; interface française
