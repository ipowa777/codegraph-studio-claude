# Spec — Token economy (Lot A)

Specs d'implémentation pour Claude Code. Trois fonctionnalités : Token Ledger,
Context budget enforcer, File slices. Tout est local, rien de versionné dans
`.project-map/`.

---

## A.1 Token Ledger

### But
Mesurer et rendre visible l'économie de tokens réalisée par le preflight
(contexte minimal vs lecture entière des fichiers pertinents).

### Estimation des tokens
Fonction `estimateTokens(text, lang)` :
- `en` : longueur / 4
- `fr` : longueur / 3.6
- `code` : longueur / 3.4
Arrondi à l'entier supérieur. La langue est inférée par extension de fichier
(`.ts`/`.tsx`/`.js`/`.mjs`/`.sql` → code, `.md` → fr/en selon contenu).

### Chiffrage en dollars (optionnel, si tarifs fournis)
`config.pricing` (template) : `{ "<model>": { "input": <usd_per_mtok>, "output": <usd_per_mtok> } }`.
Coût ≈ (tokens / 1_000_000) × input. Sortie ignorée pour la baseline lecture.

### Baseline (honnête par défaut)
Trois modes dans `config.tokenLedger.baseline` :
- `"relevant-full"` (DÉFAUT) : somme des tokens des fichiers que le preflight
  a jugés pertinents, lus EN ENTIER.
- `"repo-full"` : somme de tout le repo scanné (flatteur, à éviter sauf demande).
- `"historical"` : moyenne réelle des sessions passées (si `token-savings.json`
  contient un historique). Le plus juste.

### Mesure réelle
Somme des tokens des SLICES effectivement fournies par le preflight
(voir A.3). Si slices non disponibles, fallback = tokens des extraits listés
dans `preflight.md`.

### Sortie : `.project-map/token-savings.json`
```json
{
  "version": 1,
  "baseline_mode": "relevant-full",
  "entries": [
    {
      "ts": "ISO8601",
      "target": "billing checkout",
      "baseline_tokens": 18240,
      "actual_tokens": 2110,
      "saved_tokens": 16130,
      "saved_pct": 88.4,
      "saved_usd": 0.0129,
      "files_considered": 6,
      "model": "<optional>"
    }
  ],
  "totals": {
    "preflights": 1,
    "saved_tokens": 16130,
    "saved_usd": 0.0129,
    "window_7d_saved_tokens": 16130,
    "window_30d_saved_tokens": 16130
  }
}
```
Append à chaque preflight. Recalculer `totals` + fenêtres glissantes.

### Commande `codegraph:savings`
Lit `token-savings.json`, affiche :
- total cumulé (tokens + $), nb de preflights
- fenêtres 7j / 30j
- top 5 des tâches les plus économes
- TOUJOURS afficher la baseline utilisée + 1 phrase de méthode
  (« économie estimée vs lecture entière des fichiers pertinents »).
Ne jamais présenter un chiffre sans sa méthode de calcul.

### Honnêteté (règle dure)
- Défaut = baseline conservatrice `relevant-full`.
- Le mode `repo-full` n'est utilisé que si explicitement configuré ET
  son libellé l'indique clairement (« vs relecture intégrale du repo »).
- Les tokens sont des ESTIMATIONS — toujours le préciser.

---

## A.2 Context budget enforcer

### But
Empêcher une tâche de dépasser silencieusement un plafond de contexte.

### Config
`config.tokenLedger.maxContextTokens` (ex: 8000).

### Comportement (dans preflight)
- Calculer `actual_tokens` du contexte minimal proposé.
- Si `> maxContextTokens` :
  - Marquer le preflight `over_budget: true`.
  - Émettre une recommandation : découper en N sous-tâches via handoff packets,
    avec une proposition de découpage (par fichier / par surface / par couche).
  - Ne pas bloquer, mais avertir clairement en tête de `preflight.md`.

---

## A.3 File slices (plages de lignes)

### But
Pointer des plages précises plutôt que des fichiers entiers.

### Implémentation
- Le scan construit un index symboles → lignes (fonctions, composants, exports,
  routes, handlers) par fichier — `.project-map/symbol-index.json`.
- Le preflight, pour chaque fichier requis, fournit les SLICES pertinentes :
  `{ "file": "...", "ranges": [[240,290],[612,640]], "symbols": ["handleX"] }`.
- `preflight.md` liste les slices, pas « lis tout le fichier ».
- Fallback gracieux : si l'index ne couvre pas un fichier, fournir le fichier
  entier mais le marquer comme `full-read` (compte dans le budget).

### Bénéfice croisé
A.3 améliore A.1 (mesure réelle plus basse) et A.2 (budget plus précis).
