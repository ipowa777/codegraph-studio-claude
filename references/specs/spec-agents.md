# Spec — Gestion des agents IA (Lot C)

Pour les projets qui orchestrent des agents IA (LLM). Quatre fonctionnalités :
cost cartography, prompt drift, orchestration map, hallucination surface flag.
Le scan agents produit `.project-map/agents-manifest.json` (base commune).

---

## Base commune — agents-manifest.json
Pour chaque agent détecté (fonction/endpoint qui appelle un LLM) :
```json
{
  "agents": [
    {
      "id": "agent_x",
      "entry": "path/to/handler.ts",
      "model": "<model-id>",
      "system_prompt_tokens": 420,
      "max_tokens": 800,
      "quota": { "source": "table|config", "limits": { "free": 2, "paid": 20 } },
      "inputs_user_data": true,
      "writes": ["table_y"],
      "calls_agents": ["agent_z"],
      "validates_output": false
    }
  ]
}
```
Détection : repérer les appels au SDK LLM (wrapper interne ou SDK direct),
remonter la fonction englobante, extraire modèle, max_tokens, prompt système.

---

## C.1 Agent cost cartography

### But
Cartographier le coût de chaque agent pour identifier les plus chers.

### Calcul
- `system_prompt_tokens` estimé (cf. token economy).
- Coût/appel estimé = (prompt_tokens + max_tokens) selon tarifs `config.pricing`.
- Si une table de coûts réelle existe (`config.agents.costTable`, ex: cost_tracking),
  joindre le coût OBSERVÉ moyen par agent (plus fiable que l'estimation).

### Sortie : `.project-map/agent-costs.md` + json
Tableau trié par coût décroissant : agent, modèle, prompt tokens, max_tokens,
coût/appel estimé, coût observé moyen (si dispo), volume récent.
Met en évidence l'agent le plus coûteux et les prompts > seuil
(`config.agents.heavyPromptTokens`, ex: 1500) candidats à l'optimisation.

---

## C.2 Prompt drift detection

### But
Suivre l'évolution des prompts système et repérer les manques de garde-fou.

### Drift
Hash du prompt système par agent, stocké au baseline. `codegraph:agent-cost`
ou un scan signale les prompts modifiés depuis le baseline (diff résumé).

### Garde-fous manquants (warnings)
- `max_tokens` absent → coût non borné.
- `validates_output: false` alors que l'agent `writes` une table sensible
  ou déclenche une action (cf. C.4).
- `inputs_user_data: true` sans échappement/délimitation détectable dans le
  prompt → risque d'injection de prompt. Signaler en P1.

### Sortie
Section dans `agent-costs.md` ou `.project-map/agent-safety.md`.

---

## C.3 Orchestration map + détection de boucles

### But
Cartographier les délégations agent → sous-agent → outil et détecter les boucles.

### Implémentation
- Construire le graphe orienté à partir de `calls_agents`.
- Détecter les cycles (DFS) → boucle de délégation = P0 (risque récursion/coût).
- Détecter les agents pouvant déclencher une chaîne sans limite de profondeur
  (`config.agents.maxDelegationDepth`, ex: 3).

### Sortie : `.project-map/agent-orchestration.md`
Liste des arêtes, cycles détectés, profondeur max observée, agents « hubs »
(beaucoup de délégations sortantes). GraphView : sous-graphe agents.

---

## C.4 Hallucination surface flag

### But
Repérer où une sortie LLM non validée alimente une action sensible.

### Heuristique
Pour chaque agent :
- si `validates_output: false` ET
- la sortie est utilisée dans une action sensible : écriture DB (`writes` non
  vide), appel paiement, envoi email, publication, exécution de code/SQL
→ marquer une « surface d'hallucination » (P1, ou P0 si paiement/SQL/exec).

### Détection action sensible
Mots-clés/handlers configurables `config.agents.sensitiveSinks`
(ex: `insert`, `update`, `delete`, `charge`, `sendEmail`, `exec`, `query`).
Tracer si la valeur issue du LLM atteint un de ces sinks sans passage par une
fonction de validation (`config.agents.validators`).

### Sortie : `.project-map/agent-safety.md`
Liste des surfaces : agent, sink atteint, fichier:ligne, sévérité, suggestion
(ajouter validation/schema, borne, confirmation humaine).

### Règle
Heuristique → faux positifs possibles. Présenter comme « à vérifier », pas
comme une certitude. Fournir le chemin de données pour faciliter la revue.
