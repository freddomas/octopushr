# Glossaire OctopusHR

Utiliser ces termes exacts dans le code, les tickets et l'UI (en français).

| Terme | Définition | À éviter |
| --- | --- | --- |
| **Organisation** | Tenant. Regroupe administrateurs, employés, pointeuses et pointages. Porte un fuseau horaire IANA (défaut `Africa/Kinshasa`) et ses paramètres. | entreprise, société, compte |
| **Super-administrateur** | Opérateur de la plateforme. Crée les organisations et leur premier administrateur. Identifiant + mot de passe. N'appartient à aucune organisation. | root |
| **Administrateur** | Compte d'une organisation (identifiant + mot de passe). Il joue aussi le rôle de « RH » : gère les employés, enrôle les visages, enregistre les pointeuses, revoit les pointages. Ne voit que SON organisation. | manager, RH (rôle distinct : il n'y en a pas) |
| **Employé** | Personne qui pointe. Identifié par son **PIN**, puis vérifié par son visage. Pas de mot de passe, pas de matricule. | utilisateur, salarié |
| **PIN** | Code à 4 chiffres généré par le système à l'enrôlement, **unique dans l'organisation**, montré une seule fois. Il **identifie** l'employé ; le visage est ensuite comparé 1:1 au profil de ce PIN. Stocké uniquement sous forme d'empreinte à clé secrète (HMAC). | matricule, identifiant, mot de passe |
| **Pointeuse** | Téléphone Android enregistré par un administrateur pour un **site** ; détient un jeton secret d'appareil. Seul lieu où l'on peut pointer. Plusieurs par organisation possibles. | borne, kiosque, appareil |
| **Site** | Nom libre du lieu d'une pointeuse (ex. « Siège Gombe »). Pas d'entité propre. | |
| **Profil facial** | 3 à 5 descripteurs + photo de référence d'un employé, chiffrés, créés par l'administrateur en présence de l'employé. | template, gabarit |
| **Descripteur** | Vecteur de 1024 nombres calculé par Human sur un visage. | empreinte (confusion avec digitale) |
| **Pointage** | Enregistrement d'une **Entrée** ou d'une **Sortie** avec photo, scores, méthode, statut. | check-in, badge |
| **Tentative refusée** | Pointage de statut `refusé` : conservé (photo + scores) mais non compté. | échec |
| **Méthode** | `visage` ou `PIN`. | |
| **Statut** | `ok`, `à vérifier` (secours sans visage), `suspect` (comparaison échouée à la synchronisation, séquence incohérente, heure douteuse), `validé` / `rejeté` (décision admin), `refusé`. | |
| **Sous réserve** | Pointage hors ligne accepté provisoirement, en attente de comparaison par le serveur. | |
| **Secours sans visage** | Après 3 échecs de reconnaissance, l'employé peut valider son pointage sans visage ; photo obligatoire, pointage « à vérifier ». | PIN de secours |
| **Blocage de pointeuse** | Après 5 PIN inconnus consécutifs depuis une pointeuse, elle est bloquée quelques minutes (paramétrable). | |
| **Sortie manquante** | Entrée sans Sortie depuis plus longtemps que le seuil de l'organisation (16 h par défaut) ; une nouvelle Entrée est alors autorisée. | |
| **Seuils** | Paramètres de l'organisation : similarité, liveness, antispoof, sortie manquante, rétention des photos, etc. Modifiables sans redéploiement. | |
| **Diagnostic** | Écran administrateur qui affiche en direct les scores pour régler les seuils. | |
