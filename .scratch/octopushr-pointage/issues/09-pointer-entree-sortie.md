# 09: Employé : pointer Entrée/Sortie avec l'empreinte

**Status:** ready-for-agent
**Blocked by:** 08, 18
**Type:** afk
**Lire uniquement :** spec.md stories 4–10, 12–14, lld.md §5, ADR-0002 (+ `GLOSSARY.md`, `CLAUDE.md`)

**/goal :** Avec l'authentificateur virtuel, deux appuis successifs sur /pointer créent une Entrée puis une Sortie à l'heure serveur ; les cas limites (double appui, Sortie manquante, employé désactivé, organisation désactivée) sont couverts par des tests verts.

**What to build:** page `/pointer` réservée à l'appareil activé (ticket 18) (mobile, logo, gros bouton « Pointer », états prêt/vérification/succès/erreur), `POST /api/punch/options` et `POST /api/punch/verify` selon LLD §5 : assertion fraîche avec credential découvrable à chaque pointage, type Entrée/Sortie déterminé côté serveur, règle Sortie manquante (seuil de l'organisation, défaut 16 h, lu à chaque pointage), anti double-appui (10 s), heure serveur, réponse avec les pointages du jour affichés en heure locale de l'organisation.

- [ ] Premier pointage ⇒ Entrée, suivant ⇒ Sortie, suivant ⇒ Entrée (transaction + verrou employé, pas de course)
- [ ] Dernière Entrée ≥ seuil de l'organisation ⇒ nouvelle Entrée (horloge simulée) ; un seuil modifié s'applique au pointage suivant
- [ ] Sans cookie d'appareil valide, ou avec le cookie d'une autre organisation ⇒ 403 « Appareil non autorisé », même avec une assertion WebAuthn valide
- [ ] Deux pointages < 10 s ⇒ un seul enregistré, `duplicate: true`
- [ ] Employé `disabled`/`pending`, credential révoqué, organisation inactive ⇒ refus avec messages distincts
- [ ] Compteur de signatures vérifié ; défi rejoué ou expiré ⇒ refus
- [ ] L'heure envoyée par le client est ignorée (test)
- [ ] Écran succès : nom, type en grand (vert Entrée / bleu Sortie), heure locale, liste du jour ; erreurs claires en français (annulation, non supporté)
- [ ] Rate limit sur `/api/punch/options`
