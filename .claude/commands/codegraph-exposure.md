# codegraph:exposure

Calcule le score d'exposition 0-100 par surface.

## Quoi faire
1. Pour chaque surface (public / user-private / admin-private / monitoring),
   partir de 100 et soustraire selon les manquements pondérés
   (voir spec-security.md B.3).
2. Écrire `.project-map/exposure-score.json` avec le détail des déductions.
3. Afficher le score par surface + la liste des déductions (actionnable).

## Règle
Toujours lister les déductions. Jamais un score opaque sans justification.

Voir references/specs/spec-security.md (B.3).
