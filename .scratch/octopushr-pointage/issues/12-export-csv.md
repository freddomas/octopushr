# 12: Admin : export CSV du tableau filtré

**Status:** ready-for-agent
**Blocked by:** 10
**Type:** afk
**Lire uniquement :** spec.md story 29, lld.md §6 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Le bouton Exporter télécharge un CSV UTF-8 reflétant exactement les filtres actifs et uniquement l'organisation de l'admin (test d'intégration vert).

**What to build:** bouton « Exporter en CSV » sur `/admin/pointages` : `GET /api/admin/punches/export` avec les mêmes filtres ; colonnes matricule, nom, type, date locale, heure locale, durée ; BOM UTF-8, séparateur `;`, nom `pointages-<slug>-<date>.csv` ; neutralisation des formules (préfixer `'` les cellules commençant par `=`, `+`, `-`, `@`) ; plafond 50 000 lignes avec message si dépassé.

- [ ] Mêmes filtres et mêmes heures locales que le tableau
- [ ] Neutralisation des formules testée
- [ ] Scope org strict ; 401/403 sans session admin
- [ ] Plafond respecté (ou flux sans chargement complet en mémoire)
