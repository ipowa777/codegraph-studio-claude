# codegraph:agent-map

Cartographie l'orchestration des agents et détecte boucles + surfaces d'hallucination.

## Quoi faire
1. Construire le graphe de délégation depuis agents-manifest.json (calls_agents).
2. Détecter les cycles (P0) et la profondeur de délégation max.
3. Repérer les surfaces d'hallucination : sortie LLM non validée atteignant
   un sink sensible (DB write, paiement, email, exec/SQL) — voir spec C.4.
4. Écrire `.project-map/agent-orchestration.md` et `.project-map/agent-safety.md`.

## Règle
Les surfaces d'hallucination sont des heuristiques « à vérifier », pas des
certitudes. Toujours fournir le chemin de données pour la revue humaine.

Voir references/specs/spec-agents.md (C.3, C.4).
