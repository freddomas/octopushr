# Glossaire OctopusHR

Utiliser ces termes exacts dans le code, les tickets et l'UI (en français).

| Terme | Définition | À éviter |
| --- | --- | --- |
| **Organisation** | Tenant. Regroupe des administrateurs, des employés et leurs pointages. Porte un fuseau horaire IANA. | entreprise, société, compte |
| **Super-administrateur** | Opérateur de la plateforme. Crée les organisations et leur premier administrateur. Identifiant + mot de passe. N'appartient à aucune organisation. | root |
| **Administrateur** | Compte d'une organisation. Consulte les pointages et gère les employés de SON organisation uniquement. Identifiant + mot de passe. | manager, RH |
| **Employé** | Personne qui pointe. Aucun mot de passe : seule une empreinte enregistrée sur l'appareil de l'organisation l'authentifie. | utilisateur, salarié |
| **Matricule** | Identifiant libre de l'employé, unique dans l'organisation. | |
| **Appareil** | Le smartphone Android unique d'une organisation, activé par l'administrateur, sur lequel tourne la plateforme de pointage. Une organisation n'a qu'un appareil actif ; tout pointage depuis un autre appareil est refusé. L'espace administrateur reste accessible depuis n'importe quel navigateur. | borne, kiosque, téléphone de l'employé |
| **Empreinte enregistrée** | Passkey WebAuthn de plateforme créée par un employé sur l'appareil de l'organisation (déverrouillée par l'empreinte du système Android). Un employé actif en a au plus une. | credential, clé, appareil de l'employé |
| **Activation de l'appareil** | Action admin : génère un code à usage unique (15 min) à saisir/scanner sur le smartphone à activer ; l'ancien appareil est révoqué. | |
| **Seuil de sortie manquante** | Paramètre de l'organisation (16 h par défaut, 1–48 h) modifiable par l'administrateur. | |
| **Lien d'enrôlement** | URL à usage unique (valide 48 h) générée par l'administrateur ; ouvert sur l'appareil de l'organisation pour enregistrer l'empreinte de l'employé. | invitation |
| **Pointage** | Enregistrement horodaté côté serveur (UTC) d'une **Entrée** ou d'une **Sortie** d'un employé, validé par une vérification biométrique. | check-in, badge |
| **Entrée / Sortie** | Les deux types de pointage. Alternent strictement par employé. | |
| **Session ouverte** | Entrée sans Sortie correspondante. | |
| **Sortie manquante** | Session ouverte depuis plus longtemps que le seuil de l'organisation : considérée abandonnée ; le pointage suivant est une nouvelle Entrée. | |
| **Réinitialisation d'empreinte** | Action admin : révoque l'empreinte enregistrée de l'employé et génère un nouveau lien d'enrôlement. | |
