# 0003 — Pointeuses : téléphones enregistrés par un administrateur (preuve de lieu)

**Statut :** accepté

**Décision :**
- Un administrateur connecté, depuis le téléphone lui-même, « enregistre ce téléphone comme pointeuse » en nommant le site. Le serveur génère un jeton secret aléatoire (32 octets), n'en stocke que le hash (sha256) et le pose sur le téléphone dans le cookie `__Host-octo_pointeuse` (HttpOnly, Secure, SameSite=Lax, 1 an). La session administrateur de ce téléphone est fermée juste après.
- Plusieurs pointeuses par organisation sont possibles (un site = une ou plusieurs pointeuses).
- Toute requête de pointage, de synchronisation ou de configuration de pointage exige un jeton valide, non révoqué, d'une organisation active. Sinon 403 générique « Téléphone non enregistré ».
- L'administrateur peut révoquer une pointeuse (téléphone volé, remplacé) ; effet immédiat côté serveur.
- Le jeton prouve « ce téléphone-là », pas « cette personne » : l'identité vient du visage (ADR-0002).

**Pourquoi un cookie HttpOnly plutôt que le stockage local :** il est joint automatiquement aux requêtes, y compris celles de la synchronisation hors ligne, et un script de la page ne peut pas le lire.
