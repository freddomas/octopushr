# 18: Tableau de bord administrateur

**Status:** ready-for-agent
**Blocked by:** 16
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** spec.md story 45, lld.md §11 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** La page /admin affiche quatre SmallBox dont les valeurs correspondent exactement aux fixtures (test) et chaque tuile mène à la liste filtrée correspondante.

**What to build:** accueil `/admin` : tuiles Présents maintenant (vert), Pointages aujourd'hui (bleu), À vérifier (jaune), Suspects (rouge/cyan), + carte « Derniers pointages » (10 lignes avec miniature). « Aujourd'hui » = jour dans le fuseau de l'organisation. Les tuiles renvoient vers les pages filtrées.

- [ ] Valeurs exactes vs fixtures ; bornes de jour dans le fuseau de l'organisation
- [ ] Pointages `refused` et `rejected` non comptés ; présents = Entrée ouverte sous le seuil de sortie manquante
- [ ] Scope organisation strict (isolation testée)
- [ ] États chargement/erreur ; responsive 375 px
