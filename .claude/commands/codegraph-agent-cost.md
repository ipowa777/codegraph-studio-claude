# codegraph:agent-cost

Cartographie le coût de chaque agent IA et signale les prompts lourds.

## Quoi faire
1. Construire/charger `.project-map/agents-manifest.json` (scan agents).
2. Pour chaque agent : modèle, prompt tokens, max_tokens, coût/appel estimé
   (tarifs config.pricing), coût observé moyen si une table de coûts existe.
3. Écrire `.project-map/agent-costs.md` trié par coût décroissant.
4. Mettre en évidence l'agent le plus cher + prompts > seuil (optimisation).
5. Signaler les garde-fous manquants (max_tokens absent, sortie non validée,
   données user non échappées → injection possible).

Voir references/specs/spec-agents.md (C.1, C.2).
