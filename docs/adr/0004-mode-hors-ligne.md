# 0004 — Mode hors ligne (file locale + comparaison différée)

**Statut :** accepté

**Décision :**
- Un service worker met en cache l'application et les modèles : `/pointer` fonctionne sans réseau.
- Hors ligne, le téléphone exécute quand même tous les contrôles de vivacité, puis place dans une file IndexedDB : identifiant unique du pointage (UUID généré sur l'appareil), type, PIN saisi (chiffré localement avec une clé WebCrypto non extractable, supprimé après synchro, purgé après 7 jours), descripteur **du visage capté** (pas celui de l'employé enrôlé), photo, scores, heure de l'appareil. Le pointage est accepté « sous réserve ».
- Au retour du réseau (événement `online`, ouverture de la page, Background Sync si disponible), la file est envoyée ; le serveur fait la comparaison. Échec ⇒ pointage `suspect`, jamais supprimé. L'UUID rend l'envoi idempotent : aucun doublon.
- Heure retenue pour un pointage hors ligne : heure de l'appareil, marquée `clock_skew` si elle est dans le futur (> 5 min) ou trop ancienne (> 7 jours).
- Le secours sans visage n'existe **pas** hors ligne (il suppose un échec de comparaison par le serveur).
- Un PIN inconnu du serveur à la synchronisation ⇒ pointage `suspect` sans employé rattaché.
- Séquences incohérentes hors ligne : non détectables sur place ; signalées `suspect` à la synchronisation.
- Pointeuse révoquée entre-temps : la synchronisation est refusée, la file est conservée et l'écran l'indique.
