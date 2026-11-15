# 05: Connexion administrateur (identifiant + mot de passe), session, amorçage super-admin

**Status:** ready-for-agent
**Blocked by:** 03, 04
**Type:** afk
**Lire uniquement :** spec.md stories 16–18, 35, lld.md §3, §6 (connexion) (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Un administrateur de fixture se connecte sur /login, atteint /admin, est verrouillé après 5 échecs, se déconnecte ; /admin et /super redirigent sans session ; les tests passent.

**What to build:** le parcours complet de connexion : page `/login` (carte centrée, logo), `POST /api/admin/login`, `POST /api/admin/logout`, session JWT en cookie `__Host-octo_session`, middleware + garde serveur par rôle, pages `/admin` et `/super` minimales (titre + déconnexion dans `AdminShell`), limitation par compte (5 échecs ⇒ 15 min) et par IP via `rate_limits`, et le script `npm run seed:super` idempotent.

- [ ] Login OK ⇒ `/admin` (role admin) ou `/super` (role super) ; mauvais identifiants ⇒ message générique en français
- [ ] 5 échecs ⇒ verrouillage 15 min même avec le bon mot de passe (test avec horloge simulée)
- [ ] Cookie `HttpOnly; Secure; SameSite=Lax`, préfixe `__Host-` ; rôle/org rechargés en base à chaque requête (admin désactivé = accès coupé immédiatement)
- [ ] Contrôle `Origin` sur les requêtes mutantes
- [ ] `seed:super` crée/maintient le super-admin depuis l'environnement, refuse un mot de passe < 12 caractères
- [ ] Aucun mot de passe en clair dans logs ou réponses
