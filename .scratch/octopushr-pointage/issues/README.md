# Index des tickets — OctopusHR pointage

Un agent frais par ticket (jamais réutilisé). La frontière = tickets dont tous les bloqueurs sont résolus.

| # | Ticket | Bloqué par | Type |
|---|---|---|---|
| 01 | [Accès durables GitHub, Vercel, Supabase + dépôt de travail](./01-acces-durables.md) | None (can start immediately) | human |
| 02 | [Squelette Next.js + outillage + route /api/health](./02-scaffold-nextjs.md) | 01 | afk |
| 03 | [Design system AdminLTE-like + logo + coquille d'application](./03-design-system-shell.md) | 02 | afk |
| 04 | [Schéma Postgres, migrations, RLS deny-all, harnais de test DB](./04-schema-donnees.md) | 02 | afk |
| 05 | [Connexion administrateur (identifiant + mot de passe), session, amorçage super-admin](./05-auth-admin.md) | 03, 04 | afk |
| 06 | [Super-admin : créer et gérer les organisations + premier administrateur](./06-organisations-super-admin.md) | 05 | afk |
| 07 | [Admin : gestion des employés (créer, lister, désactiver, réactiver)](./07-gestion-employes.md) | 05 | afk |
| 08 | [Enrôlement : lien/QR admin + enregistrement de l'empreinte par l'employé](./08-enrolement-empreinte.md) | 07 | afk |
| 09 | [Employé : pointer Entrée/Sortie avec l'empreinte](./09-pointer-entree-sortie.md) | 08 | afk |
| 10 | [Admin : tableau des pointages de l'organisation (filtres, durées)](./10-tableau-pointages.md) | 05 | afk |
| 11 | [Admin : tableau de bord (présents, pointages du jour, employés actifs, sorties manquantes)](./11-tableau-de-bord.md) | 10 | afk |
| 12 | [Admin : export CSV du tableau filtré](./12-export-csv.md) | 10 | afk |
| 13 | [Admin : réinitialiser l'appareil d'un employé](./13-reinitialiser-appareil.md) | 08 | afk |
| 14 | [Durcissement sécurité transverse (en-têtes, CSP, limites, audit dépendances)](./14-durcissement-securite.md) | 09, 12, 13 | afk |
| 15 | [Revue de sécurité indépendante + tests d'attaque inter-organisations](./15-revue-securite-isolation.md) | 14 | afk |
| 16 | [Tests E2E des parcours complets (Android émulé)](./16-e2e-parcours.md) | 11, 12, 13 | afk |
| 17 | [Déploiement production Vercel + recette + transmission du lien](./17-deploiement-vercel-prod.md) | 01, 15, 16 | afk |
