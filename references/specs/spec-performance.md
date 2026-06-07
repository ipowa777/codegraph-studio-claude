# Spec — Performance / qualité (Lot D)

Trois fonctionnalités : Health score, Architecture drift detector, Hotspot radar.

---

## D.1 Health score 0-100 + tendance

### But
Un KPI unique de santé du code, historisé.

### Composantes (pondérations par défaut, configurables)
- Sécurité (30%) : dérivé de exposure-score + access-diff (régressions = pénalité).
- Dette technique (25%) : ratio fichiers > seuil de taille, TODO/FIXME, duplication.
- Couverture docs (15%) : présence et fraîcheur des docs clés vs zones de code.
- Dérive d'architecture (20%) : nb de violations de conventions (cf. D.2).
- Duplication (10%) : blocs dupliqués détectés.

Chaque composante normalisée 0-100, score = moyenne pondérée.

### Sortie : `.project-map/health-score.json`
```json
{
  "ts": "ISO8601",
  "score": 78,
  "components": { "security": 85, "debt": 70, "docs": 75, "drift": 80, "duplication": 90 },
  "trend": [ { "ts": "...", "score": 74 }, { "ts": "...", "score": 78 } ]
}
```
Append au scan. GraphView : courbe d'évolution du score.

### Règle
Afficher le détail des composantes, pas seulement le chiffre global. Le score
sert à suivre une tendance, pas à juger dans l'absolu.

---

## D.2 Architecture drift detector

### But
Faire respecter les conventions du projet automatiquement.

### Conventions (config.conventions)
Liste de règles déclaratives, chacune :
```json
{
  "id": "no-direct-fetch",
  "description": "Utiliser le wrapper réseau, jamais fetch() direct",
  "match": { "glob": "apps/**/*.{ts,tsx}", "pattern": "\\bfetch\\(", "not_pattern": "callEdgeFunction" },
  "severity": "P1",
  "advice": "Remplacer fetch() par le wrapper interne"
}
```
Types de règles :
- `forbid` : un motif ne doit pas apparaître (ex: `.single(` interdit, préférer `.maybeSingle(`).
- `require` : si un motif A présent, un motif B doit l'être aussi.
- `boundary` : un dossier ne doit pas importer d'un autre (couches).

### Exécution (`codegraph:drift`)
- Scanner les fichiers selon les globs.
- Pour chaque violation : fichier:ligne, règle, sévérité, conseil.
- Mode baseline : ne signaler que les NOUVELLES violations (diff) pour ne pas
  noyer sous l'existant. `--all` pour tout lister.

### Sortie : `.project-map/drift.md`
Violations classées par sévérité. En CI : exit non-zéro sur P0/P1 nouvelles.

### Bénéfice
Transforme les conventions tacites (souvent dans un CLAUDE.md) en garde-fous
exécutables. Détecte les régressions de style/archi dès leur introduction.

---

## D.3 Hotspot & refactor radar

### But
Identifier les fichiers à risque (gros + très importés + souvent modifiés).

### Signaux (par fichier)
- `size` : lignes de code.
- `fan_in` : nombre d'imports entrants (qui dépend de ce fichier).
- `churn` : nombre de commits touchant le fichier sur N jours (`git log`,
  fenêtre `config.hotspots.churnWindowDays`, défaut 90).

### Score hotspot
Normaliser chaque signal 0-1, score = moyenne (pondération configurable).
Les fichiers à la fois gros, très importés et très modifiés = candidats
refactor prioritaires (risque de régression maximal).

### Sortie : `.project-map/hotspots.md`
Top N fichiers triés par score, avec les 3 signaux et une suggestion
(split, extraction, tests à ajouter). Purement indicatif.
