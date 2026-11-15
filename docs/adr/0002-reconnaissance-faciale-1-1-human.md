# 0002 — Reconnaissance faciale 1:1 avec @vladmandic/human, décision côté serveur

**Statut :** accepté (remplace l'authentification par empreinte WebAuthn, abandonnée : le navigateur ne peut pas identifier le doigt de qui que ce soit, voir l'historique de la conversation)

**Décision :**
- Employé : **PIN** à 4 chiffres (clavier numérique) qui l'identifie, puis visage en direct. Comparaison **1:1** contre le seul profil de ce PIN, jamais 1:N. Pas de matricule.
- `@vladmandic/human` 3.3.6 (version épinglée, MIT), exécuté **dans le navigateur** : détection, contrôles de vivacité (`antispoof`, `liveness`, clignement, regard) et calcul du descripteur (1024 flottants). Modèles hébergés avec l'application (`public/models`), mis en cache par un service worker. Voir `docs/research/human-face-recognition.md`.
- Le serveur **décide** : il compare le descripteur reçu aux descripteurs enrôlés (formule portée à l'identique de `human.match.similarity`), applique les seuils de similarité, liveness et antispoof de l'organisation, la séquence Entrée/Sortie et l'état de l'employé.
- Le moteur de reconnaissance est derrière une interface `FaceEngine` : Human en production, un double scripté dans les tests (seam principal).
- Secours sans visage : après 3 échecs, l'employé (déjà identifié par son PIN) peut valider sans visage ; photo obligatoire, pointage « à vérifier ».
- PIN : généré au hasard par le système, unique par organisation, sans suites triviales, stocké en `HMAC-SHA256(PIN_PEPPER, org‖pin)` (une recherche par valeur interdit un hash lent ; 10 000 valeurs rendent de toute façon un hash lent peu utile).
- Hors ligne : contrôles de vivacité sur le téléphone, mise en file locale, comparaison par le serveur à la synchronisation (ADR-0004).

**Limites assumées :** les scores de vivacité et le descripteur sont produits par le navigateur et ne sont pas vérifiables par le serveur ; la vivacité passive de Human ne garantit pas le rejet d'une vidéo rejouée. Compensations : pointeuse enregistrée (ADR-0003), photo conservée avec chaque tentative, revue RH, seuils réglables, défi aléatoire (tourner la tête) activable.

**Risques du PIN-identifiant :** (1) un PIN observé ou partagé permet à un collègue de s'identifier comme l'employé ; il échoue la comparaison faciale, mais après 3 échecs volontaires il peut valider sans visage — le pointage est alors « à vérifier » avec **sa** photo ; (2) 4 chiffres = au plus 10 000 employés par organisation et un PIN deviné n'est pas rattaché à un compte à verrouiller : on bloque donc la **pointeuse** après 5 PIN inconnus consécutifs (5 min par défaut) ; (3) un PIN n'est jamais choisi par l'employé (évite de révéler qu'un PIN est déjà pris). Atténuation partielle : le clavier redistribue les chiffres à chaque saisie (contre l'observation des doigts et les traces sur l'écran ; n'empêche pas de lire les chiffres affichés). Recommandation : passer à 5–6 chiffres si l'organisation dépasse quelques centaines d'employés ou si la triche par observation devient un sujet.

**Alternatives rejetées :** WebAuthn (identité non liée à la personne sur un téléphone partagé) ; comparaison dans le navigateur (le client déciderait) ; copier les descripteurs sur le téléphone (jamais).
