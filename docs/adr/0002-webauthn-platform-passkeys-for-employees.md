# 0002 — Authentification employé : passkey WebAuthn de plateforme, vérification à chaque pointage

**Statut :** accepté

**Décision :**
- Bibliothèque `@simplewebauthn/server` + `@simplewebauthn/browser`.
- Enrôlement : `authenticatorAttachment: "platform"`, `userVerification: "required"`, `residentKey: "required"` (credential découvrable).
- Pointage : une assertion WebAuthn fraîche (`userVerification: "required"`, défi à usage unique, expire en 5 min) est exigée **à chaque pointage**. L'employé est identifié par le credential découvrable ; il n'y a donc **ni champ identifiant, ni session employé, ni mot de passe**.
- Contrôle du compteur de signatures et de l'origine ; défi consommé atomiquement.
- RP ID = domaine de production stable (variable `WEBAUTHN_RP_ID`). Changer de domaine invalide tous les appareils : ne jamais le faire sans réenrôlement général. Les déploiements preview ne servent pas au test biométrique.

**Pourquoi :** exigence « empreinte uniquement » ; Android Chrome expose l'empreinte via WebAuthn sans APK.

**Alternatives rejetées :** session employé longue durée (un téléphone prêté pourrait pointer sans empreinte) ; mot de passe/PIN de secours (hors exigence).
