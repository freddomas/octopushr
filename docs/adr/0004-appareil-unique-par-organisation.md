# 0004 — Un seul appareil de pointage par organisation

**Statut :** accepté (demande du produit)

**Décision :** chaque organisation a un seul smartphone Android actif (« l'appareil ») sur lequel tourne la plateforme de pointage (`/pointer`, `/enroll/*`, `/api/punch/*`, `/api/enroll/*`). Aucun pointage ni enrôlement depuis un autre appareil. L'espace administrateur (identifiant + mot de passe) reste accessible partout.

- L'administrateur active l'appareil avec un code à usage unique (15 min). L'appareil reçoit un jeton aléatoire stocké haché côté serveur et un cookie `__Host-octo_device` (HttpOnly, Secure, 1 an). Réactiver sur un autre smartphone révoque le précédent.
- Chaque route de pointage vérifie ce cookie en plus de l'assertion WebAuthn. Raison : les passkeys Android peuvent être synchronisées par le gestionnaire de mots de passe Google et donc utilisées sur un autre appareil ; seul le jeton d'appareil lie le pointage au smartphone de l'organisation.
- Plusieurs employés enregistrent leur empreinte sur le même appareil (un passkey par employé, `user.id` = id de l'employé).

**Risque accepté / à confirmer :** sur un appareil partagé, Android déverrouille un passkey avec n'importe quelle empreinte enregistrée dans le téléphone. La biométrie prouve qu'une personne autorisée est présente, pas laquelle. Un employé pourrait donc pointer à la place d'un collègue en choisissant son passkey. Atténuations prévues : l'écran de confirmation affiche le nom, la photo n'est pas prévue ; le tableau admin rend les anomalies visibles. Une vraie liaison personne↔empreinte exigerait chaque employé sur son propre smartphone (modèle initial, abandonné par ce choix).
