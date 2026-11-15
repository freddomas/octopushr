# OctopusHR

SaaS web de pointage (entrée/sortie) pour smartphones Android, sans APK. Employés : empreinte (WebAuthn). Admins : identifiant + mot de passe. Aucun autre module RH.

Documents de référence : `.scratch/octopushr-pointage/spec.md` (quoi), `.scratch/octopushr-pointage/lld.md` (comment), `GLOSSARY.md` (vocabulaire), `docs/adr/` (décisions). Les tickets sont dans `.scratch/octopushr-pointage/issues/`.

## Règles d'orchestration

- Un agent frais par ticket, jamais réutilisé pour un autre ticket. Chaque ticket contient une ligne `/goal` à donner à l'implémenteur.
- Un ticket doit tenir dans un contexte frais : lire uniquement le ticket + les sections LLD qu'il cite.
- Interface en français. Heures stockées en UTC, affichées dans le fuseau de l'organisation.
- Ne jamais committer de secrets. `DATABASE_URL`, clés Supabase et secrets de session vivent dans les variables d'environnement Vercel / `.env.local`.

## Agent skills

### Issue tracker

Markdown local sous `.scratch/<feature>/`. See `docs/agents/issue-tracker.md`.

### Triage labels

Vocabulaire par défaut (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`). See `docs/agents/triage-labels.md`.

### Domain docs

Single-context. See `docs/agents/domain.md`.
