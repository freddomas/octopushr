# 0005 — Protection des données biométriques

**Statut :** accepté

**Décision :**
- Sont stockés : descripteurs faciaux (3 à 5 par profil), photo de référence, photo de chaque tentative de pointage (JPEG ~480 px, < 50 Ko). Tout est **chiffré au niveau applicatif** (AES-256-GCM, clé `BIOMETRIC_KEY` en variable d'environnement, version de clé stockée, identifiant de ligne en données associées). La base ne contient jamais ces données en clair.
- Accès : déchiffrement uniquement côté serveur, pour le rôle administrateur de l'organisation concernée. Images servies par un endpoint authentifié, `Cache-Control: no-store`, jamais d'URL publique. Un employé ne voit jamais les données d'un autre. Les descripteurs enrôlés ne sont jamais envoyés à un navigateur ni copiés sur un téléphone.
- Consentement : l'administrateur atteste à l'enrôlement que l'employé a été informé et consent (case obligatoire, horodatée, tracée).
- Suppression : l'administrateur peut supprimer le profil facial (suppression physique des descripteurs et de la photo de référence). Les photos de tentative sont purgées automatiquement après `photo_retention_days` (90 par défaut, paramétrable).
- Les données sont hébergées dans la base Supabase du projet (région eu-central-1).

**À valider hors projet :** la conformité juridique (RDC : législation sur la protection des données personnelles et le numérique ; autres pays selon les clients) n'est pas évaluée ici et ne constitue pas un avis juridique.
