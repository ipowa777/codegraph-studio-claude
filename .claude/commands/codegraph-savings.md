# codegraph:savings

Affiche le Token Ledger : tokens et coût économisés par les preflights.

## Quoi faire
1. Lire `.project-map/token-savings.json` (si absent : indiquer qu'aucun
   preflight n'a encore été journalisé).
2. Afficher :
   - Total cumulé : tokens économisés + coût $ (si tarifs configurés)
   - Nombre de preflights journalisés
   - Fenêtres glissantes 7 jours / 30 jours
   - Top 5 des tâches les plus économes
3. TOUJOURS afficher la baseline utilisée et une phrase de méthode.
   Ex : « Estimation vs lecture entière des fichiers pertinents (baseline
   conservatrice). Les tokens sont des estimations. »

## Règles
- Ne jamais présenter un chiffre d'économie sans sa méthode de calcul.
- Baseline par défaut : `relevant-full` (conservatrice, crédible).
- Ne pas inventer de données si le fichier est vide.

Voir references/specs/spec-token-economy.md (A.1).
