# 19: Export CSV du tableau filtré

**Status:** ready-for-agent
**Blocked by:** 16
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** spec.md story 46, lld.md §11 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le bouton Exporter télécharge un CSV UTF-8 reflétant les filtres actifs et uniquement l'organisation de l'admin, sans aucune donnée biométrique (test d'intégration vert).

**What to build:** `GET /api/admin/punches/export` avec les mêmes filtres que `/admin/pointages` ; colonnes nom, type, date locale, heure locale, site, méthode, statut, similarité, durée ; BOM UTF-8, séparateur `;`, nom `pointages-<slug>-<date>.csv` ; neutralisation des formules (`=`, `+`, `-`, `@`) ; plafond 50 000 lignes avec message.

- [ ] Mêmes filtres et mêmes heures locales que le tableau
- [ ] Aucune photo, aucun descripteur dans le fichier
- [ ] Neutralisation des formules testée
- [ ] Scope organisation strict ; 401/403 sans session admin
