# codegraph:health

Calcule le score de santé du code 0-100 et sa tendance.

## Quoi faire
1. Calculer les composantes : sécurité, dette, docs, dérive archi, duplication
   (pondérations config, voir spec-performance.md D.1).
2. Score = moyenne pondérée. Append à `.project-map/health-score.json` (tendance).
3. Afficher le score global ET le détail par composante (jamais le chiffre seul).

Voir references/specs/spec-performance.md (D.1).
