# 0002 — Reconnaissance faciale 1:1 avec @vladmandic/human, décision côté serveur

**Statut :** accepté (remplace l'authentification par empreinte WebAuthn, abandonnée : le navigateur ne peut pas identifier le doigt de qui que ce soit, voir l'historique de la conversation)

**Décision :**
- Employé : matricule (clavier numérique) + visage en direct. Comparaison **1:1** contre le seul profil de ce matricule, jamais 1:N.
- `@vladmandic/human` 3.3.6 (version épinglée, MIT), exécuté **dans le navigateur** : détection, contrôles de vivacité (`antispoof`, `liveness`, clignement, regard) et calcul du descripteur (1024 flottants). Modèles hébergés avec l'application (`public/models`), mis en cache par un service worker. Voir `docs/research/human-face-recognition.md`.
- Le serveur **décide** : il compare le descripteur reçu aux descripteurs enrôlés (formule portée à l'identique de `human.match.similarity`), applique les seuils de similarité, liveness et antispoof de l'organisation, la séquence Entrée/Sortie et l'état de l'employé.
- Le moteur de reconnaissance est derrière une interface `FaceEngine` : Human en production, un double scripté dans les tests (seam principal).
- Secours : PIN à 4 chiffres haché (argon2id) après 3 échecs ; pointage marqué « à vérifier ».
- Hors ligne : contrôles de vivacité sur le téléphone, mise en file locale, comparaison par le serveur à la synchronisation (ADR-0004).

**Limites assumées :** les scores de vivacité et le descripteur sont produits par le navigateur et ne sont pas vérifiables par le serveur ; la vivacité passive de Human ne garantit pas le rejet d'une vidéo rejouée. Compensations : pointeuse enregistrée (ADR-0003), photo conservée avec chaque tentative, revue RH, seuils réglables, défi aléatoire (tourner la tête) activable.

**Alternatives rejetées :** WebAuthn (identité non liée à la personne sur un téléphone partagé) ; comparaison dans le navigateur (le client déciderait) ; copier les descripteurs sur le téléphone (jamais).
