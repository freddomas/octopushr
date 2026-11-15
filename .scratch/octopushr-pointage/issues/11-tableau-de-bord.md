# 11: Admin : tableau de bord (présents, pointages du jour, employés actifs, sorties manquantes)

**Status:** ready-for-agent
**Blocked by:** 10
**Type:** afk
**Lire uniquement :** spec.md story 28, lld.md §6 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** La page /admin affiche quatre SmallBox dont les valeurs correspondent exactement aux fixtures (test) et chaque tuile mène au tableau filtré correspondant.

**What to build:** accueil `/admin` : quatre tuiles `SmallBox` (Présents maintenant = vert, Pointages aujourd'hui = bleu, Employés actifs = cyan, Sorties manquantes = jaune) comme dans la direction artistique d'openhr, + carte « Derniers pointages » (10 lignes). « Plus d'infos » renvoie vers `/admin/pointages` ou `/admin/employes` préfiltrés. « Aujourd'hui » = jour dans le fuseau de l'organisation.

- [ ] Valeurs exactes vs fixtures ; bornes de jour dans le fuseau de l'organisation
- [ ] « Sorties manquantes » utilise le seuil de l'organisation
- [ ] Scope org strict (test d'isolation)
- [ ] Liens préfiltrés fonctionnels
- [ ] États chargement/erreur gérés ; responsive 375 px
