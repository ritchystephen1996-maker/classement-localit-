# CECI — Classement central des grandes localités

## Architecture
Application Web → Utilisateurs A/B → Supabase/PostgreSQL → classement central → export Excel.

La V4 utilise Supabase comme base centrale et Supabase Realtime pour recevoir les changements en direct. Le navigateur utilise uniquement la clé publique/publishable ; aucune clé `service_role` ne doit être publiée.

## 1. Créer le projet Supabase
Créer un projet sur Supabase, puis :
- Authentication → Providers → activer Anonymous Sign-Ins.
- SQL Editor → exécuter `supabase/schema.sql`.

Supabase recommande de protéger les tables avec RLS et de n'exposer au navigateur que les permissions nécessaires.

## 2. Configurer l'application
Copier `config.example.js` vers `config.js` et remplacer :
- SUPABASE_URL
- SUPABASE_PUBLISHABLE_KEY

Ne jamais mettre une clé `service_role`/secret dans `config.js`.

## 3. GitHub Pages
Mettre tout le contenu de ce dossier dans le repository GitHub, puis :
Settings → Pages → Source : GitHub Actions.

Le workflow `.github/workflows/pages.yml` déploiera automatiquement le site à chaque push sur `main`.

## 4. Import des données
Ouvrir l'application et cliquer sur Importer Excel.
Le fichier doit contenir au minimum :
- id
- ParcelleDenomination
- Grande localité (facultatif)
- Statut (facultatif)

L'import se fait par lots de 200 lignes dans la base centrale.

## 5. Travail simultané
Deux utilisateurs connectés à la même application lisent la même base.
Lorsqu'un utilisateur déplace une carte, l'UPDATE est envoyé à Supabase et l'autre navigateur reçoit l'événement Realtime.

## 6. Excel / OneDrive
La V4 exporte actuellement un Excel depuis le classement central.
Pour écrire automatiquement dans un fichier Excel OneDrive/SharePoint, il faut ajouter une Edge Function ou une API serveur avec Microsoft Graph. Ne jamais mettre les secrets Microsoft dans `index.html`.

## 7. Sécurité
La V4 utilise l'authentification anonyme pour le MVP. Ce n'est pas encore le niveau de sécurité recommandé pour un outil CECI en production.
Pour la version de production, ajouter :
- authentification nominative ou comptes CECI ;
- rôles (admin, classificateur, lecteur) ;
- RLS plus restrictive ;
- journal protégé ;
- synchronisation Microsoft Graph côté serveur.
