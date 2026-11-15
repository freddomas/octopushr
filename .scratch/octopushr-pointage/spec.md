# Spec — OctopusHR : pointage biométrique (Manoir OCTOPUS)

**Status:** ready-for-agent · Design détaillé : [lld.md](./lld.md) · Tickets : [issues/](./issues/)

## Problem Statement

Les organisations n'ont pas de moyen simple et fiable de savoir quand leurs employés arrivent et partent. Les feuilles papier se falsifient et les applications natives imposent d'installer un APK. Les employés ont besoin de pointer en deux secondes depuis leur smartphone Android ; l'administrateur a besoin de voir, en ligne, tous les pointages de son organisation.

## Solution

Une application web (SaaS, installable en raccourci d'écran d'accueil mais sans APK) :
- L'**employé** ouvre la page de pointage dans Chrome Android, touche « Pointer », valide avec son empreinte ; le serveur enregistre automatiquement une Entrée ou une Sortie avec sa propre horloge.
- L'**administrateur** se connecte avec identifiant + mot de passe, gère ses employés (création, lien d'enrôlement, désactivation, réinitialisation d'appareil) et consulte le tableau des pointages de toute son organisation (filtres, durées, export CSV).
- Le **super-administrateur** crée les organisations et leur premier administrateur.
- Aucun autre module RH (congés, paie, planning…).

## User Stories

Employé
1. As an employé, I want ouvrir un lien d'enrôlement sur mon téléphone, so that je lie mon empreinte à mon compte sans mot de passe.
2. As an employé, I want que l'enrôlement exige la vérification de mon empreinte, so that personne d'autre ne puisse enregistrer mon appareil.
3. As an employé, I want un lien d'enrôlement à usage unique qui expire, so that un lien volé ne soit pas exploitable.
4. As an employé, I want toucher un seul bouton « Pointer » puis valider mon empreinte, so that pointer prenne quelques secondes.
5. As an employé, I want ne pas saisir d'identifiant ni de mot de passe, so that il n'y ait rien à oublier.
6. As an employé, I want que l'application détermine seule s'il s'agit d'une Entrée ou d'une Sortie, so that je ne me trompe pas.
7. As an employé, I want voir une confirmation claire (nom, type, heure locale de l'organisation), so that je sache que mon pointage est enregistré.
8. As an employé, I want voir mes pointages du jour après avoir pointé, so that je vérifie que tout est cohérent.
9. As an employé, I want que l'heure soit celle du serveur, so that l'horloge de mon téléphone ne puisse pas fausser mon pointage.
10. As an employé, I want un message compréhensible si mon empreinte est refusée ou annulée, so that je sache quoi faire.
11. As an employé, I want un message clair si mon téléphone ne supporte pas l'empreinte ou si mon navigateur n'est pas compatible, so that je sache vers qui me tourner.
12. As an employé, I want qu'un oubli de Sortie de la veille n'inverse pas tous mes pointages suivants, so that mes données restent exploitables.
13. As an employé, I want qu'un double appui accidentel ne crée pas deux pointages, so that mes heures soient exactes.
14. As an employé désactivé, I want que mon pointage soit refusé avec un message explicite, so that je comprenne ma situation.
15. As an employé ayant changé de téléphone, I want que mon administrateur me renvoie un lien d'enrôlement, so that je retrouve l'accès.

Administrateur
16. As an administrateur, I want me connecter avec identifiant et mot de passe, so that j'accède à mon espace.
17. As an administrateur, I want être bloqué temporairement après plusieurs échecs, so that on ne puisse pas deviner mon mot de passe.
18. As an administrateur, I want me déconnecter, so that je protège mon compte sur un poste partagé.
19. As an administrateur, I want créer un employé (nom complet, matricule), so that il puisse pointer.
20. As an administrateur, I want générer un lien d'enrôlement et un QR code pour un employé, so that je le lui transmette facilement.
21. As an administrateur, I want voir qui est enrôlé, en attente d'enrôlement ou désactivé, so that je pilote le déploiement.
22. As an administrateur, I want désactiver et réactiver un employé, so that un départ n'efface pas son historique.
23. As an administrateur, I want réinitialiser l'appareil d'un employé, so that un téléphone perdu ne puisse plus pointer.
24. As an administrateur, I want un tableau de tous les pointages de mon organisation, so that je supervise les présences.
25. As an administrateur, I want filtrer par période, employé et type, so that je retrouve une information vite.
26. As an administrateur, I want voir les heures dans le fuseau de mon organisation, so that les données correspondent à la réalité locale.
27. As an administrateur, I want voir la durée de chaque session (Entrée→Sortie) et repérer les sorties manquantes, so that je détecte les anomalies.
28. As an administrateur, I want une page d'accueil avec présents maintenant, pointages du jour, employés actifs et sorties manquantes, so that j'ai l'essentiel d'un coup d'œil.
29. As an administrateur, I want exporter le tableau filtré en CSV, so that je l'utilise dans mes outils.
30. As an administrateur, I want ne jamais voir les données d'une autre organisation, so that la confidentialité soit garantie.
31. As an administrateur, I want une interface utilisable sur mobile et sur ordinateur, so that je consulte partout.

Super-administrateur
32. As a super-administrateur, I want créer une organisation (nom, fuseau horaire), so that un nouveau client démarre.
33. As a super-administrateur, I want créer le premier administrateur de cette organisation avec un mot de passe initial, so that le client puisse se connecter.
34. As a super-administrateur, I want lister, désactiver et réactiver les organisations, so that je gère les abonnements.
35. As a super-administrateur, I want que mon compte soit créé par un script d'amorçage et non par une page publique, so that personne ne puisse s'auto-promouvoir.

Plateforme
36. As an opérateur, I want que toutes les écritures sensibles soient validées côté serveur, so that aucune donnée client ne soit crue.
37. As an opérateur, I want que les endpoints d'authentification soient limités en débit, so that la force brute soit freinée.
38. As an opérateur, I want une application avec le logo du Manoir OCTOPUS et la direction artistique d'openhr, so that l'identité soit cohérente.
39. As an opérateur, I want une application déployée en production sur Vercel avec migrations appliquées, so that la livraison soit reproductible.

## Implementation Decisions

- Stack, rôle de Supabase, isolation par organisation : ADR-0001. Authentification employé : ADR-0002.
- Trois rôles : super-administrateur et administrateur (mot de passe), employé (empreinte seule).
- Multi-organisations dès le départ ; création par le super-administrateur uniquement (pas d'inscription publique).
- Fuseau horaire IANA par organisation ; stockage UTC ; interface en français.
- Pointage : alternance stricte Entrée/Sortie déterminée par le serveur ; règle de la Sortie manquante à 16 h ; anti double-appui par fenêtre de 10 s ; heure serveur uniquement.
- Anti-fraude : empreinte seule (pas de géolocalisation) — arbitrage utilisateur.
- Lecture seule du tableau des pointages : aucune correction manuelle (hors périmètre).
- Direction artistique : AdminLTE-like inspirée de openhr/openhr, logo Manoir OCTOPUS (`docs/assets/logo-octopus-manoir.png`).
- Détails (schéma, endpoints, flux, règles) : voir le LLD.

## Testing Decisions

- Un bon test vérifie le comportement externe (réponse HTTP, état en base, ce que voit l'utilisateur), pas l'implémentation.
- Seam principal : les **route handlers / server actions** appelés avec une vraie base Postgres de test ; Playwright avec authentificateur WebAuthn virtuel (CDP) pour les parcours de bout en bout.
- Tests d'attaque inter-organisations obligatoires (ticket 15).
- Pas d'antécédent dans le dépôt (projet neuf) ; les tickets posent les conventions.

## Out of Scope

Congés, paie, planning, corrections de pointage, application native/APK, géolocalisation/géofence, inscription publique d'organisations, mot de passe/PIN de secours pour employés, mode hors-ligne, notifications, rapports avancés, multi-langue, SSO, historique côté employé au-delà du jour courant.

## Further Notes

- Prérequis humains (accès GitHub, Vercel, Supabase durables) : ticket 01.
- Le répertoire de travail VPS cible est `/home/usr_codex/workspace/my_projects/OctopusHR` ; les documents sont rédigés ici et déplacés avec le dépôt au ticket 01.
- Test biométrique réel impossible sur un navigateur de bureau sans capteur : le test final se fait sur le téléphone Android de l'utilisateur via le lien de production.
