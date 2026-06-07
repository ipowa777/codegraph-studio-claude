# codegraph:dashboard

Génère le Control Tower : tableau de bord HTML local qui agrège toutes les
sorties CodeGraph en une vue.

## Quoi faire
1. Lire les JSON déjà produits (health-score, token-savings, exposure-score,
   agents-manifest, et les .md : access-diff, agent-costs, agent-safety,
   hotspots, drift).
2. Générer `.project-map/dashboard.html` autonome (voir spec-dashboard.md) :
   en-tête + score santé, rangée KPIs, sécurité, agents IA, qualité, conventions.
3. Sections sans source → « non disponible, lancer codegraph:<cmd> » (pas de crash).
4. Sections sans problème → afficher en positif (« aucune régression ✓ »).
5. Chaque métrique rappelle sa méthode/baseline (règle d'honnêteté).

## Règle
Le dashboard est une VUE en lecture seule. Aucun calcul caché : il rend les
fichiers produits par les autres commandes. Jamais versionné.

Voir references/specs/spec-dashboard.md.
