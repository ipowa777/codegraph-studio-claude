# codegraph:hotspots

Identifie les fichiers à risque (gros + très importés + souvent modifiés).

## Quoi faire
1. Pour chaque fichier : taille (LOC), fan-in (imports entrants),
   churn (commits sur fenêtre git configurable).
2. Normaliser et combiner en score hotspot (voir spec-performance.md D.3).
3. Écrire `.project-map/hotspots.md` : top N + 3 signaux + suggestion refactor.

## Règle
Indicatif. Ne pas présenter comme une obligation de refactor.

Voir references/specs/spec-performance.md (D.3).
