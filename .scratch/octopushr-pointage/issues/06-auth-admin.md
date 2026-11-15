# 06: Connexion administrateur (identifiant + mot de passe), session, amorçage super-admin

**Status:** ready-for-agent
**Blocked by:** 03, 04
**Type:** afk
**Modèle / effort :** opus / high
**Lire uniquement :** spec.md stories 52, 54, lld.md §3, §10 (connexion) (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un administrateur de fixture se connecte sur /login, atteint /admin, est verrouillé après 5 échecs, se déconnecte ; /admin et /super redirigent sans session ; les tests passent.

**What to build:** page `/login` (carte centrée, logo), `POST /api/admin/login`, `POST /api/admin/logout`, session JWT cookie `__Host-octo_session`, middleware + garde serveur par rôle, pages `/admin` et `/super` minimales dans `AdminShell`, verrouillage par compte (5 échecs ⇒ 15 min) et par IP (`rate_limits`), script `npm run seed:super` idempotent.

- [ ] Login OK ⇒ `/admin` ou `/super` selon le rôle ; mauvais identifiants ⇒ message générique en français
- [ ] 5 échecs ⇒ verrouillage 15 min même avec le bon mot de passe (horloge simulée)
- [ ] Cookie `HttpOnly; Secure; SameSite=Lax`, préfixe `__Host-` ; rôle/org rechargés en base à chaque requête (admin ou org désactivés ⇒ accès coupé)
- [ ] Contrôle `Origin` sur requêtes mutantes
- [ ] `seed:super` crée/maintient le super-admin depuis l'environnement, refuse < 12 caractères
- [ ] Aucun mot de passe en clair dans logs ou réponses
