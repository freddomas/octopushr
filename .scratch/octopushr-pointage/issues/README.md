# Index des tickets — OctopusHR pointage

Un agent frais par ticket (jamais réutilisé). Frontière = tickets dont tous les bloqueurs sont résolus. Les tickets 01–02 sont faits ; 03–27 refondus pour la direction « reconnaissance faciale » (les anciens 03–19 WebAuthn ont été supprimés avant tout travail).

| # | Ticket | Bloqué par | Statut |
|---|---|---|---|
| 01 | [Accès durables GitHub, Vercel, Supabase + dépôt de travail](./01-acces-durables.md) | None (can start immediately) | resolved |
| 02 | [Squelette Next.js + outillage + route /api/health](./02-scaffold-nextjs.md) | 01 | resolved |
| 03 | [Design system AdminLTE-like + logo + coquille d'application](./03-design-system-shell.md) | 02 | ready-for-agent |
| 04 | [Schéma Postgres, migrations, RLS deny-all, harnais de test DB, variables d'environnement](./04-schema-donnees.md) | 02 | ready-for-agent |
| 05 | [Chiffrement AES-GCM des données biométriques et recherche de PIN (HMAC)](./05-chiffrement-biometrique.md) | 04 | ready-for-agent |
| 06 | [Connexion administrateur (identifiant + mot de passe), session, amorçage super-admin](./06-auth-admin.md) | 03, 04 | ready-for-agent |
| 07 | [Super-admin : créer et gérer les organisations + premier administrateur](./07-organisations-super-admin.md) | 06 | ready-for-agent |
| 08 | [Admin : gestion des employés (créer, lister, désactiver, réactiver)](./08-gestion-employes.md) | 06 | ready-for-agent |
| 09 | [Pointeuses : enregistrer ce téléphone, jeton d'appareil, liste, révocation](./09-pointeuses.md) | 06 | ready-for-agent |
| 10 | [Admin : paramètres de l'organisation (seuils, sortie manquante, rétention)](./10-parametres-organisation.md) | 06 | ready-for-agent |
| 11 | [Socle reconnaissance faciale : Human local, modèles, service worker, FaceEngine, métriques](./11-socle-reconnaissance-faciale.md) | 03 | ready-for-agent |
| 12 | [Enrôlement du visage par l'administrateur (3–5 captures, qualité, PIN, consentement)](./12-enrolement-visage.md) | 05, 08, 11 | ready-for-agent |
| 13 | [Pointage en ligne par visage (PIN, vivacité, décision serveur)](./13-pointage-visage-en-ligne.md) | 09, 10, 12 | ready-for-agent |
| 14 | [Secours sans visage après 3 échecs, blocage de pointeuse, régénération du PIN](./14-secours-pin.md) | 13 | ready-for-agent |
| 15 | [Mode hors ligne : file locale, synchronisation idempotente](./15-mode-hors-ligne.md) | 13 | ready-for-agent |
| 16 | [Écran RH : tableau des pointages avec photos côte à côte, scores et filtres](./16-tableau-pointages-rh.md) | 05, 06 | ready-for-agent |
| 17 | [File « à vérifier / suspects », validation/rejet commentés, journal d'audit](./17-file-revue-audit.md) | 16 | ready-for-agent |
| 18 | [Tableau de bord administrateur](./18-tableau-de-bord.md) | 16 | ready-for-agent |
| 19 | [Export CSV du tableau filtré](./19-export-csv.md) | 16 | ready-for-agent |
| 20 | [Écran de diagnostic : scores en direct et calibration des seuils](./20-diagnostic-seuils.md) | 10, 13 | ready-for-agent |
| 21 | [Rétention des photos et suppression des données biométriques](./21-retention-suppression.md) | 13, 10 | ready-for-agent |
| 22 | [Durcissement sécurité transverse](./22-durcissement-securite.md) | 14, 15, 17, 19, 20, 21 | ready-for-agent |
| 23 | [Tests E2E des parcours complets (moteur simulé, Android émulé)](./23-tests-e2e.md) | 14, 15, 17, 18, 19, 20 | ready-for-agent |
| 24 | [Revue de sécurité indépendante + tests d'attaque](./24-revue-securite-isolation.md) | 22 | ready-for-agent |
| 25 | [Procédure de test manuel sur téléphone et gabarit de rapport de mesures](./25-procedure-test-manuel.md) | 20, 15, 14 | ready-for-agent |
| 26 | [Déploiement production Vercel + recette](./26-deploiement-vercel-prod.md) | 01, 23, 24, 25 | ready-for-agent |
| 27 | [Recette sur téléphone réel : cas manuels et mesures de performance](./27-recette-telephone-reel.md) | 26 | ready-for-human |
