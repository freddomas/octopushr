# 0001 — Next.js sur Vercel, Supabase utilisé comme Postgres uniquement

**Statut :** accepté

**Décision :** Next.js (App Router, TypeScript) déployé sur Vercel (projet `octopushr`). Supabase fournit uniquement la base Postgres, accédée **côté serveur** via `DATABASE_URL` (Drizzle ORM + migrations). Ni Supabase Auth, ni Supabase client côté navigateur, ni PostgREST : toutes les tables ont RLS activé sans aucune policy (deny-all pour les rôles `anon`/`authenticated`), donc la clé publishable ne donne accès à rien.

**Pourquoi :** Supabase Auth ne convient pas à notre modèle (employés sans email, identifiés par matricule + visage). Une couche d'accès unique côté serveur rend l'isolation par organisation testable et évite d'exposer la base au client.

**Conséquences :** l'isolation des organisations est appliquée dans la couche d'accès aux données (toute requête prend `orgId` de la session) et vérifiée par des tests d'attaque inter-organisations (ticket 15).

**Direction artistique :** inspirée du repo `openhr/openhr` (AdminLTE 3 : barre latérale sombre, barre supérieure claire, tuiles « small-box » colorées, cartes, tableaux). Pas de reprise de code (stack .NET/Angular).
