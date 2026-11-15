# Prompt de reprise — OctopusHR (à coller dans une nouvelle fenêtre de contexte)

Tu reprends le projet **OctopusHR** : SaaS web de pointage (Entrée/Sortie) pour smartphones Android, sans APK, déployé sur Vercel. Tu es l'**orchestrateur** : tu ne codes pas les tickets toi-même, tu lances un **agent frais par ticket** (jamais réutilisé) et tu vérifies leur travail. Réponds en français.

## Où on en est
- Dépôt local : `/home/usr_claude/Projects/OpenHR` (branche de travail **`integration/pointage`**, poussée sur `https://github.com/freddomas/octopushr`, dépôt public ; `main` ne contient que les docs initiales). Le chemin `/home/usr_codex/...` du brief n'existe pas sur ce serveur.
- **Tickets 01 (accès) et 02 (squelette Next.js 16, Tailwind, Vitest, Playwright, CI) sont faits et fusionnés.** Lint, typecheck, test, build verts.
- **Tickets 03 à 27 : refondus, prêts, non démarrés.** Rien d'autre n'est codé.
- Lis dans cet ordre : `CLAUDE.md`, `.scratch/octopushr-pointage/spec.md`, `.scratch/octopushr-pointage/lld.md`, `GLOSSARY.md`, `docs/adr/0001…0005`, `docs/research/human-face-recognition.md`, `.scratch/octopushr-pointage/issues/README.md` (index + graphe de dépendances). Chaque ticket contient son `/goal`, ses critères et les sections du LLD à lire.

## Le produit (direction actuelle, validée)
- Pas d'empreinte digitale/WebAuthn (abandonné : un navigateur ne peut pas lier un doigt à une personne).
- **Pointeuse** : un administrateur connecté enregistre un téléphone ; le serveur émet un jeton secret (cookie HttpOnly), sans lequel tout pointage est refusé ; révocable ; plusieurs par organisation.
- **Employé** : choisit Entrée/Sortie, tape son **PIN à 4 chiffres** (clavier numérique dont les chiffres sont **redistribués au hasard à chaque affichage**, saisie en points), puis regarde la caméra. PIN généré par le système, unique par organisation, stocké en HMAC (`PIN_PEPPER`), **pas de matricule**. Comparaison faciale **1:1** avec `@vladmandic/human` 3.3.6 dans le navigateur (antispoof, liveness, clignement, regard activés explicitement), **décision prise par le serveur**. Modèles hébergés localement + service worker. Photo conservée avec chaque tentative. Secours sans visage après 3 échecs (« à vérifier »). Blocage de la **pointeuse** après 5 PIN inconnus. Mode hors ligne avec file IndexedDB, synchronisation idempotente, pointages douteux « suspect ».
- **Administrateur** (= RH, identifiant + mot de passe) : employés, enrôlement du visage (3–5 captures, consentement), pointeuses, paramètres (seuils sans redéploiement), tableau des pointages (photo captée | photo de référence), file « à vérifier/suspects », journal d'audit, tableau de bord, export CSV, écran de diagnostic. **Super-administrateur** : crée les organisations (fuseau par défaut `Africa/Kinshasa`).
- Données biométriques chiffrées (AES-256-GCM, `BIOMETRIC_KEYS`), jamais exposées à un autre employé.

## Accès (déjà en place, ne rien redemander sauf échec)
- **GitHub** : `gh` installé dans `~/.local/bin` (ajoute-le au `PATH` : `export PATH=$HOME/.local/bin:$PATH`), connecté comme `freddomas` (scopes repo, workflow, read:org), credential helper HTTPS configuré. Identité git locale du dépôt : `freddomas` / adresse noreply.
- **Vercel** : connecteur MCP autorisé pour l'équipe `team_JIhhMm1KcelpnrALMsIzqnBn` ; projet `octopushr` (`prj_zfRAC7Avaa9uAwPvIC4RoeFRyRAj`) existe, aucun déploiement. Variables déjà présentes : `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY`, `DATABASE_URL` (production, *sensitive*, non relisible). **La protection SSO Vercel est active** : elle bloquerait les téléphones ; à désactiver pour la production **après confirmation de l'utilisateur** (ticket 26).
- **Supabase** : connecteur MCP, projet `OctopusHR` (`cdmykxlqrkylyfpzglww`, eu-central-1), base vide. **Il n'y a pas de `DATABASE_URL` sur ce serveur** : le ticket 04 doit choisir la base de test (Postgres local, ou migrations via le connecteur) et le documenter dans l'ADR 0006.
- Secrets à générer et poser dans Vercel au ticket 26 (jamais dans le dépôt) : `SESSION_SECRET`, `BIOMETRIC_KEYS`, `BIOMETRIC_KEY_VERSION`, `PIN_PEPPER`, `CRON_SECRET`, `APP_URL`, `SUPER_ADMIN_*` (seed seulement). Le wizard `scripts/setup-access.sh` existe mais l'utilisateur a préféré les connecteurs.

## Règles d'orchestration (à respecter)
1. Suis le skill **`implement-spec`** (installé globalement, `disable-model-invocation` : applique ses étapes à la main) : branche d'intégration `integration/pointage`, **un sous-agent implémenteur frais par ticket**, chacun dans son worktree (`isolation: "worktree"`, en arrière-plan), qui appelle le skill **`tdd`**, fusionne le tip d'intégration, ne pousse rien. Un agent « merger » (ou toi pour un fast-forward trivial) fusionne ensuite dans `integration/pointage`. Une fois tous les tickets faits : skill `code-review`, puis nettoyage des worktrees.
2. **Modèle et effort** : chaque ticket porte une ligne `**Modèle / effort :**` (sonnet par défaut ; opus pour authentification, données, sécurité). Passe-la à l'outil Agent via les paramètres `model` et `effort` (l'utilisateur a explicitement demandé ce réglage). Agents sans ticket : merger et exploration `sonnet`/`low`, correctifs après `code-review` `sonnet`/`high` (`opus`/`high` si auth ou données). Ne change pas ces valeurs sans accord.
3. Prompts aux agents : **pointeurs seulement** (chemin du ticket, de la spec, du LLD, des ADR) ; pas de copier-coller. Donne le `/goal` du ticket.
4. **Frontière** : lance en parallèle tous les tickets dont les bloqueurs sont résolus (début : **03 et 04 en parallèle** ; puis 05, 06, 11 etc. selon l'index). Marque le ticket `Status: resolved` dans son fichier à la fusion.
5. **Vérifie toi-même** après chaque fusion (`npm run lint && npm run typecheck && npm test && npm run build`) : les rapports d'agents ne sont pas une preuve. Les agents de sécurité (24) et de déploiement (26) sont des agents dédiés distincts.
6. Le projet est indexé pour tokensave ? Sinon utilise Read/Grep normalement. Pas d'agent Explore pour de la recherche de code.
7. `.claude/` est ignoré par git et ESLint (les worktrees y vivent). Commits en français, terminés par `Co-Authored-By: Claude Sonnet 5.5 <noreply@anthropic.com>`. Ne jamais committer de secret. Ne jamais pousser sur `main` sans demande.
8. Toute action sortante ou irréversible (désactiver le SSO Vercel, déploiement prod, suppression) : confirme avec l'utilisateur.
9. Utilise `mcp__ccd_turn__update_plan` / `wrap_up` comme le demande l'environnement.

## Limites connues à garder en tête et à répéter honnêtement
- La vivacité passive de Human **n'est pas fiable contre une vidéo rejouée** ; scores et descripteur sont calculés par le navigateur et **non vérifiables** par le serveur. Compensations : pointeuse enregistrée, photo conservée, revue RH, seuils réglables, défi aléatoire (option).
- Le PIN est à la fois identifiant et secours : un PIN observé permet à un collègue de pointer « à vérifier » avec sa propre photo après 3 échecs volontaires. 4 chiffres = 10 000 PIN max par organisation. Passage à 5–6 chiffres recommandé si besoin ; **PIN gardé à 4 chiffres pour l'instant** (décision utilisateur).
- Je ne peux pas mesurer la performance ni tester la caméra sur le téléphone de l'utilisateur : tickets 25 (procédure) et 27 (recette humaine, `ready-for-human`). Human n'a plus de release depuis août 2025.
- Conformité juridique des données biométriques non évaluée (ADR-0005).

## Hypothèses par défaut non encore explicitement validées
Plusieurs pointeuses par organisation ; administrateur = RH ; photos/descripteurs chiffrés en Postgres (pas de Supabase Storage) ; rétention des photos 90 jours ; heure d'un pointage hors ligne = heure de l'appareil, signalée si douteuse ; « annulation » = rejet (pas d'édition d'heure) ; le brief sautait la section **F** (à confirmer si quelque chose manque).

## Première action
1. Vérifie l'état : `git -C /home/usr_claude/Projects/OpenHR status`, `git log --oneline -5`, `git branch --show-current` (attendu : `integration/pointage`), `gh auth status`.
2. Si l'utilisateur ne s'y est pas opposé, lance les **tickets 03 et 04** en parallèle (deux agents frais, deux worktrees), puis suis l'index. Rends compte à chaque jalon.
