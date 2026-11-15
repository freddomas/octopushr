# Spec — OctopusHR : pointage par reconnaissance faciale (Manoir OCTOPUS)

**Status:** ready-for-agent · Design détaillé : [lld.md](./lld.md) · Tickets : [issues/](./issues/) · Recherche : `docs/research/human-face-recognition.md`
**Historique :** la première version utilisait l'empreinte digitale (WebAuthn). Abandonnée : un navigateur ne peut pas lier un doigt à une personne. Nouvelle direction : visage + matricule + téléphone enregistré.

## Problem Statement

Une organisation veut savoir de façon fiable qui arrive et part, sur un ou plusieurs sites, avec une connexion lente et instable, sans application native. Les feuilles papier se falsifient et un collègue peut « couvrir » un absent. L'administrateur doit voir tous les pointages avec la preuve (photo), et pouvoir régler la sévérité du système.

## Solution

Application web (SaaS) en français :
- Un administrateur enregistre un téléphone Android comme **pointeuse** d'un site (jeton secret). Sans ce jeton, aucun pointage.
- L'administrateur enrôle le **visage** de chaque employé en sa présence.
- L'employé choisit Entrée ou Sortie, tape son **PIN**, regarde la caméra : contrôles de vivacité sur le téléphone, comparaison 1:1 décidée par le serveur, photo conservée. validation sans visage après 3 échecs. Fonctionne **hors ligne** avec synchronisation.
- L'administrateur contrôle les pointages (photo captée à côté de la photo de référence, scores), traite les pointages à vérifier/suspects, et dispose d'un journal d'audit et d'un écran de diagnostic pour régler les seuils.
- Pas d'autres modules RH.

## User Stories

Pointeuse
1. As an administrateur, I want enregistrer le téléphone dans mes mains comme pointeuse d'un site, so that seul ce téléphone puisse servir au pointage.
2. As an administrateur, I want que le téléphone garde un jeton secret propre, so that le lieu soit prouvé.
3. As an administrateur, I want que ma session admin soit fermée sur la pointeuse après l'enregistrement, so that un employé ne puisse pas agir en admin.
4. As an administrateur, I want voir la liste des pointeuses (site, date, dernière activité, statut), so that je sache lesquelles sont actives.
5. As an administrateur, I want révoquer une pointeuse volée ou remplacée, so that elle ne puisse plus pointer immédiatement.
6. As an employé, I want un message clair si je suis sur un téléphone non enregistré, so that je sache où pointer.

Enrôlement du visage
7. As an administrateur, I want ajouter un employé avec son nom, so that il puisse être enrôlé.
8. As an administrateur, I want capturer 3 à 5 images du visage (face, légère rotation gauche/droite) en présence de l'employé, so that le profil soit robuste.
9. As an administrateur, I want un contrôle de qualité à chaque capture (un seul visage, taille suffisante, éclairage correct, netteté), so that les mauvaises captures soient refusées.
10. As an administrateur, I want que la cohérence entre les captures soit vérifiée, so that deux personnes différentes ne finissent pas dans le même profil.
11. As an administrateur, I want enregistrer une photo de référence, so that je puisse comparer visuellement plus tard.
12. As an employé, I want recevoir à l'enrôlement un PIN à 4 chiffres, montré une seule fois, so that je m'identifie sans matricule.
13. As an administrateur, I want confirmer que l'employé a été informé et consent, so that la collecte soit tracée.
14. As an administrateur, I want ré-enrôler ou supprimer un profil facial, so that je corrige une erreur ou respecte une demande de suppression.
15. As an administrateur, I want que les données biométriques soient chiffrées et jamais visibles d'un autre employé, so that la confidentialité soit respectée.

Pointage
16. As an employé, I want choisir Entrée ou Sortie, so that mon intention soit explicite.
17. As an employé, I want taper mon PIN sur un grand clavier numérique (saisie masquée), so that le pointage soit rapide.
18. As an employé, I want regarder la caméra frontale avec un retour visuel clair (cadre, consignes), so that je sache quoi faire.
19. As an opérateur, I want que seule la caméra en direct soit utilisable (aucun fichier ni galerie), so that une photo ne soit pas présentée.
20. As an opérateur, I want exiger un seul visage, une taille minimale, le regard vers la caméra, un clignement, et les modules liveness et antispoof, so that les usurpations simples échouent.
21. As an opérateur, I want que le visage capté soit comparé uniquement au profil de l'employé identifié par le PIN saisi, so that on ne cherche pas dans toute la base.
22. As an opérateur, I want que la décision d'acceptation soit prise par le serveur, so that le navigateur ne décide pas.
23. As an administrateur, I want régler les seuils de similarité, liveness et antispoof sans redéploiement, so that j'ajuste sur le terrain.
24. As an opérateur, I want conserver une photo (~480 px, < 50 Ko) et les scores de chaque tentative, réussie ou non, so that tout soit auditable.
25. As an employé, I want un message clair si ma séquence est incohérente (deux Entrées de suite, Sortie sans Entrée), so that je comprenne le refus.
26. As an employé, I want pouvoir de nouveau faire une Entrée si j'ai oublié ma Sortie depuis plus longtemps que le seuil, so that je ne sois pas bloqué.
27. As an employé, I want un message clair si l'accès à la caméra est refusé, so that je sache comment corriger.
28. As an employé, I want une confirmation avec mon nom, le type et l'heure locale, so that je sache que c'est enregistré.

Secours PIN
29. As an employé, I want pouvoir valider sans visage après 3 échecs de reconnaissance (mon PIN m'a déjà identifié), so that je ne sois pas bloqué par un mauvais éclairage.
30. As an administrateur, I want que ces pointages soient marqués « à vérifier » avec photo obligatoire, so that je les contrôle.
31. As an opérateur, I want un blocage temporaire de la pointeuse après 5 PIN inconnus consécutifs, so that le PIN ne se devine pas par essais.
32. As an administrateur, I want régénérer le PIN d'un employé (nouveau PIN affiché une fois), so that je dépanne en cas d'oubli ou de PIN connu d'un tiers.

Hors ligne
33. As an employé, I want pointer même sans réseau, so that une coupure ne bloque pas l'équipe.
34. As an employé, I want voir « enregistré sous réserve » et le nombre de pointages en attente, so that je sache l'état.
35. As an opérateur, I want que les contrôles de vivacité tournent hors ligne, so that la file ne contienne que des captures plausibles.
36. As an opérateur, I want que la synchronisation ne crée jamais de doublon, so that un renvoi soit sans effet.
37. As an administrateur, I want que les pointages dont la comparaison échoue à la synchronisation soient marqués suspects, jamais supprimés, so that je les juge.
38. As an opérateur, I want qu'aucun descripteur d'employé ne soit copié sur le téléphone, so that un téléphone volé ne livre pas de biométrie.
39. As an employé, I want un indicateur en ligne / hors ligne / N en attente, so that l'état soit visible.

Contrôle RH
40. As an administrateur, I want la liste des pointages avec photo captée et photo de référence côte à côte, scores et méthode, so that je vérifie d'un coup d'œil.
41. As an administrateur, I want filtrer par date, employé et statut, so that je trouve vite.
42. As an administrateur, I want une file dédiée des pointages « à vérifier » et « suspects », so that je traite l'essentiel.
43. As an administrateur, I want valider ou rejeter avec un commentaire, so that la décision soit justifiée.
44. As an administrateur, I want un journal d'audit de chaque validation, rejet, annulation, ré-enrôlement, révocation et changement de paramètre, so that tout soit traçable.
45. As an administrateur, I want un tableau de bord (présents, pointages du jour, à vérifier, suspects), so that j'aie l'essentiel.
46. As an administrateur, I want exporter le tableau filtré en CSV, so that je l'utilise ailleurs.
47. As an administrateur, I want voir les heures dans le fuseau de mon organisation (Kinshasa par défaut), so that elles soient lisibles.

Diagnostic, performance et données
48. As an administrateur, I want un écran de diagnostic avec les scores en direct, so that je règle les seuils sur le téléphone réel.
49. As an opérateur, I want mesurer le temps de chargement des modèles et le temps par pointage sur le téléphone réel, so that je juge si c'est acceptable.
50. As an administrateur, I want que les photos de tentative soient purgées après une durée paramétrable, so that on ne garde pas des données inutilement.
51. As an administrateur, I want ne jamais voir les données d'une autre organisation, so that l'isolation soit garantie.

Comptes
52. As an administrateur, I want me connecter par identifiant et mot de passe, être verrouillé après des échecs et me déconnecter, so that mon espace soit protégé.
53. As a super-administrateur, I want créer, désactiver et réactiver des organisations avec leur premier administrateur, so that un client démarre.
54. As a super-administrateur, I want que mon compte vienne d'un script d'amorçage, so that personne ne s'auto-promeuve.
55. As an opérateur, I want l'application déployée sur Vercel avec migrations et secrets en place, so that la livraison soit reproductible.

## Implementation Decisions

- ADR-0001 stack ; ADR-0002 reconnaissance faciale 1:1 avec Human, décision serveur ; ADR-0003 pointeuses ; ADR-0004 hors ligne ; ADR-0005 données biométriques. Détails dans le LLD.
- Un seul rôle « administrateur » (= RH). Super-administrateur pour les organisations. Les employés n'ont pas de compte.
- Identification par PIN (4 chiffres, unique par organisation, généré par le système, stocké en HMAC), sans matricule. Le type Entrée/Sortie est choisi par l'employé ; le serveur refuse les séquences incohérentes ; une Entrée est de nouveau permise si la dernière Entrée dépasse le seuil de sortie manquante.
- Comparaison 1:1 ; similarité = max sur les descripteurs du profil ; seuils par organisation.
- Le seam de test principal est `FaceEngine` (double scripté) côté client et les endpoints API côté serveur.

## Testing Decisions

- Un bon test vérifie le comportement externe, pas l'implémentation.
- Automatisé : décisions serveur (similarité, seuils, séquence, PIN, blocage, idempotence hors ligne, isolation), API, parcours E2E avec moteur simulé. Quelques tests lents avec les vrais modèles Human sur des images fixtures sous licence libre.
- **Non automatisable de façon fiable** : photo imprimée, vidéo rejouée sur un autre téléphone, faible éclairage, contre-jour, performance sur le téléphone réel. Couverts par la procédure de test manuel, l'écran de diagnostic et le ticket de recette sur appareil.

## Out of Scope

Géolocalisation, reconnaissance 1:N, empreinte digitale/WebAuthn, application native, congés, paie, planning, correction manuelle de l'heure d'un pointage, multi-langue, SSO, avis juridique de conformité.

## Further Notes

- **Risque du PIN-identifiant** : voir ADR-0002 (observation/partage du PIN, 10 000 valeurs max par organisation, blocage de la pointeuse plutôt que du compte).
- Lettre F absente dans le brief initial (A, B, C, D, E, G) : à confirmer si une section manque.
- Hypothèses prises par défaut, à valider : plusieurs pointeuses par organisation ; administrateur = RH ; photos et descripteurs en Postgres chiffré (pas de Supabase Storage, qui exigerait une clé de service supplémentaire) ; PIN généré par le système et unique par organisation ; rétention des photos de tentative 90 jours ; secours sans visage indisponible hors ligne ; heure d'un pointage hors ligne = heure de l'appareil, signalée si douteuse ; « annulation » = rejet par l'administrateur (pas d'édition d'heure).
