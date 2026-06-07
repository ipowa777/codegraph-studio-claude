# codegraph:drift

Détecte les violations de conventions d'architecture.

## Quoi faire
1. Charger config.conventions (règles forbid / require / boundary).
2. Scanner les fichiers selon les globs des règles.
3. Par défaut, ne signaler que les NOUVELLES violations vs baseline
   (option --all pour tout lister).
4. Écrire `.project-map/drift.md` (violations par sévérité + conseil).
5. En CI : exit non-zéro sur nouvelles violations P0/P1.

Voir references/specs/spec-performance.md (D.2).
