# codegraph:access-diff

Compare la matrice d'accès courante au baseline et alerte sur les régressions.

## Quoi faire
1. Construire/charger `.project-map/access-matrix.json` (scan courant).
2. Charger `.project-map/access-matrix.baseline.json`.
3. Détecter les régressions P0 (voir spec-security.md B.2) :
   route privée perd sa garde, table perd sa RLS, colonne sensible devient
   writable client, endpoint privé devient public, admin perd noindex.
4. Écrire `.project-map/access-diff.md` (régressions classées P0/P1).
5. En CI : exit non-zéro sur toute régression P0.

Voir references/specs/spec-security.md (B.2).
