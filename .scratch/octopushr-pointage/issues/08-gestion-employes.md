# 08: Admin : gestion des employés (créer, lister, désactiver, réactiver)

**Status:** ready-for-agent
**Blocked by:** 06
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** spec.md stories 7, 51, lld.md §2 (employees), §10 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un admin de fixture crée un employé (nom seul), le voit « Visage à enrôler », le désactive puis le réactive ; un admin d'une autre organisation ne le voit jamais (tests verts).

**What to build:** page `/admin/employes` : tableau (nom, statut Visage à enrôler / Actif / Désactivé, date), recherche par nom, création (nom complet uniquement ; le PIN est généré à l'enrôlement du visage, pas de matricule), désactivation/réactivation (ne supprime rien). Toutes les opérations passent par `src/server/data` avec `Scope` org.

- [ ] Création validée (zod) ; nom vide ou trop long ⇒ erreur claire (deux employés peuvent porter le même nom)
- [ ] Statut `pending_face` à la création
- [ ] Désactiver/réactiver conserve l'historique de pointages
- [ ] Test d'isolation : l'admin de l'org B ne peut ni lister, ni lire, ni modifier un employé de l'org A (404)
- [ ] Pagination/limite pour 1 000 employés
- [ ] Interface française, entrée « Employés » dans la sidebar
