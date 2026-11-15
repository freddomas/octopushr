# 16: Écran RH : tableau des pointages avec photos côte à côte, scores et filtres

**Status:** ready-for-agent
**Blocked by:** 05, 06
**Type:** afk
**Modèle / effort :** sonnet / medium
**Lire uniquement :** spec.md stories 40, 41, 47, 51, lld.md §11, §5 (endpoints d'image) (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec des pointages de fixture sur deux organisations, un admin voit uniquement les siens dans /admin/pointages avec photo captée et photo de référence côte à côte, scores, méthode, statut, filtrés par date/employé/statut, en heure locale (tests verts).

**What to build:** `/admin/pointages` (tableau paginé 50/p : employé, type, heure locale, site, méthode, statut, scores, miniatures captée | référence, durée de session, badge « Sortie manquante » selon le seuil de l'organisation), clic ⇒ vue détail avec photos en grand ; endpoints d'image authentifiés (déchiffrement serveur, `no-store`) ; filtres date, employé, statut, méthode. Données de test par fixtures.

- [ ] Requête scopée par organisation ; fuseau appliqué (changement de jour testé)
- [ ] Images : seule la session admin de la bonne organisation y accède ; URL non devinable ; jamais en cache
- [ ] Photo de référence absente (profil supprimé) ⇒ placeholder
- [ ] Durée Entrée→Sortie ; « Sortie manquante » avec le seuil de l'organisation
- [ ] Les pointages `refused` sont masqués par défaut (filtre pour les voir)
- [ ] Test d'isolation y compris sur les endpoints d'image
- [ ] Responsive (cartes empilées < 576 px) ; français
