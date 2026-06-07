# Spec — Control Tower Dashboard (capstone)

Un tableau de bord HTML local unique qui agrège toutes les sorties CodeGraph
en une vue. Étend le GraphView existant. 100% local, aucune dépendance réseau,
aucun secret. Généré par `codegraph:dashboard`, ouvert dans le navigateur.

---

## But
Aujourd'hui les sorties sont éparpillées (~15 fichiers `.project-map/*`). Le
dashboard les réunit pour un pilotage d'un coup d'œil — pour un humain comme
pour amorcer une IA sur l'état du projet.

## Sortie
`.project-map/dashboard.html` — fichier autonome (HTML+CSS+JS inline, pas de
CDN obligatoire ; si une lib de graphe est utilisée, l'inliner ou la rendre
optionnelle). Jamais versionné (déjà dans .gitignore via `.project-map/`).

## Sources agrégées (lecture seule des JSON déjà produits)
- `health-score.json` → jauge 0-100 + courbe de tendance
- `token-savings.json` → cumul tokens/$ économisés + sparkline
- `exposure-score.json` → score par surface (public/user/admin/monitoring)
- `access-diff.md` → régressions de sécurité récentes (P0/P1)
- `agent-costs.md` / `agents-manifest.json` → top agents par coût
- `agent-safety.md` → surfaces d'hallucination à vérifier
- `hotspots.md` → top fichiers à risque
- `drift.md` → violations de conventions récentes
- `PROJECT_MAP.md` → liens vers la carte complète

## Disposition (sections, ordre)
1. **En-tête** : nom projet, date du scan, score de santé global (gros chiffre
   + pastille couleur : vert ≥80, ambre 60-79, rouge <60).
2. **Rangée KPIs** (4 cartes) : Santé · Tokens économisés (cumul) ·
   Exposition min (surface la plus à risque) · Coût agents (estimé/mois).
3. **Sécurité** : scores d'exposition par surface (barres) + liste des
   régressions d'accès récentes (vide = « aucune régression ✓ »).
4. **Agents IA** (si présents) : tableau top coûts + alertes safety.
5. **Qualité** : composantes du health score (barres) + top hotspots.
6. **Conventions** : violations de drift récentes (vide = « conforme ✓ »).
7. **Pied** : méthode de calcul de chaque métrique (rappel honnêteté) +
   horodatage + lien vers GraphView et PROJECT_MAP.

## Style
- Sobre, lisible, dark/light selon `prefers-color-scheme`.
- Pas d'effet superflu. Cartes à bord léger, typographie claire.
- Chaque métrique affiche sa méthode/baseline au survol ou en légende
  (cohérent avec la règle d'or : pas de chiffre sans méthode).
- Sections vides affichées en positif (« aucune régression », « conforme »)
  plutôt que masquées — l'absence de problème est une information.

## Comportement
- Si une source manque, la section affiche « non disponible — lancer
  codegraph:<commande> » au lieu de planter.
- Régénéré à chaque `codegraph:dashboard` ou à la fin d'un `codegraph:scan`
  complet (si `features.dashboard` activé).
- Lecture seule : le dashboard ne calcule rien lui-même, il rend les JSON
  déjà produits par les autres commandes. Cela garde la logique centralisée
  et le dashboard trivial à maintenir.

## Règle
Le dashboard est une VUE, pas une source de vérité. Toute métrique vient d'un
fichier produit par une commande dédiée. Pas de calcul caché dans le HTML.
