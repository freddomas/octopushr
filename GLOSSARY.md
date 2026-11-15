# Glossaire OctopusHR

Utiliser ces termes exacts dans le code, les tickets et l'UI (en français).

| Terme | Définition | À éviter |
| --- | --- | --- |
| **Organisation** | Tenant. Regroupe des administrateurs, des employés et leurs pointages. Porte un fuseau horaire IANA. | entreprise, société, compte |
| **Super-administrateur** | Opérateur de la plateforme. Crée les organisations et leur premier administrateur. Identifiant + mot de passe. N'appartient à aucune organisation. | root |
| **Administrateur** | Compte d'une organisation. Consulte les pointages et gère les employés de SON organisation uniquement. Identifiant + mot de passe. | manager, RH |
| **Employé** | Personne qui pointe. Aucun mot de passe : seule l'empreinte de son appareil l'authentifie. | utilisateur, salarié |
| **Matricule** | Identifiant libre de l'employé, unique dans l'organisation. | |
| **Appareil** | Passkey WebAuthn de plateforme enregistrée par un employé (empreinte Android). Un employé actif a au plus un appareil actif. | credential, clé |
| **Lien d'enrôlement** | URL à usage unique (valide 48 h) générée par l'administrateur ; l'employé l'ouvre sur son téléphone pour enregistrer son appareil. | invitation |
| **Pointage** | Enregistrement horodaté côté serveur (UTC) d'une **Entrée** ou d'une **Sortie** d'un employé, validé par une vérification biométrique. | check-in, badge |
| **Entrée / Sortie** | Les deux types de pointage. Alternent strictement par employé. | |
| **Session ouverte** | Entrée sans Sortie correspondante. | |
| **Sortie manquante** | Session ouverte depuis plus de 16 h : considérée abandonnée ; le pointage suivant est une nouvelle Entrée. | |
| **Réinitialisation d'appareil** | Action admin : révoque l'appareil de l'employé et génère un nouveau lien d'enrôlement. | |
