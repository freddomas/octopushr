# 07: Admin : gestion des employés (créer, lister, désactiver, réactiver)

**Status:** ready-for-agent
**Blocked by:** 05
**Type:** afk
**Lire uniquement :** spec.md stories 19, 21, 22, 30, lld.md §2 (employees), §6 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un admin de fixture crée un employé, le voit En attente d'enrôlement, le désactive puis le réactive ; un admin d'une autre organisation ne le voit jamais (tests verts).

**What to build:** page `/admin/employes` : tableau (nom, matricule, statut En attente d'enrôlement / Actif / Désactivé, date de création), recherche, création (nom complet, matricule unique dans l'organisation), désactivation/réactivation. Désactiver ne supprime rien. Toute lecture/écriture passe par `src/server/data` avec `Scope` org.

- [ ] Création validée (zod) ; matricule dupliqué ⇒ erreur claire
- [ ] Statut `pending` à la création ; `disabled` bloquera le pointage (ticket 09)
- [ ] Désactiver/réactiver conserve l'historique des pointages
- [ ] Test d'isolation : admin de l'org B ne peut ni lister, ni lire, ni modifier un employé de l'org A (404)
- [ ] Pagination/limite raisonnable pour 1 000 employés
- [ ] Interface française, entrée « Employés » dans la sidebar
