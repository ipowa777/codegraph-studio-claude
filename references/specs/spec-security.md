# Spec — Sécurité (Lot B)

Trois fonctionnalités : Secret leak guard, Access matrix diff, Surface exposure
score. Toutes locales. CodeGraph ne lit jamais le CONTENU des secrets ; il
détecte des motifs et des régressions d'accès.

---

## B.1 Secret leak guard (pre-commit)

### But
Bloquer un commit qui introduit accidentellement une clé/secret.

### Implémentation
- Hook git `pre-commit` (ou étape `codegraph:ci`) qui scanne le `git diff --cached`.
- Motifs détectés (regex, extensible via config) :
  - clés génériques : `sk_live_`, `sk_test_`, `rk_live_`, `whsec_`, `ghp_`,
    `gho_`, `github_pat_`, `AKIA[0-9A-Z]{16}`, `AIza[0-9A-Za-z_\-]{35}`
  - blocs : `-----BEGIN (RSA |EC |OPENSSH )?PRIVATE KEY-----`
  - assignations suspectes : `(API_KEY|SECRET|TOKEN|PASSWORD)\s*[:=]\s*['"][^'"]{12,}`
  - fichiers `.env` non ignorés ajoutés au commit
- Allowlist : `config.security.secretAllowlist` (hashes ou chemins de faux positifs,
  ex: fixtures de test, exemples de doc comme cette spec).

### Sortie
- Si match : exit non-zéro + liste fichier:ligne + type de motif (PAS la valeur).
- Sortie volontairement non révélatrice : « clé de type Stripe détectée
  dans X:L12 » sans afficher la clé.

### Règle dure
Ne jamais logger, afficher ni stocker la valeur du secret détecté.

---

## B.2 Access matrix diff

### But
Détecter une régression de sécurité entre deux scans.

### Matrice d'accès (construite au scan)
Pour chaque ressource exposée, enregistrer dans `.project-map/access-matrix.json` :
```json
{
  "routes": [
    { "path": "/admin/x", "surface": "admin-private", "guard": true, "noindex": true }
  ],
  "tables": [
    { "name": "profils", "rls": true, "writable_client_cols": [] }
  ],
  "endpoints": [
    { "name": "stripe-webhook", "auth": "service_role", "public": false }
  ]
}
```

### Diff (`codegraph:access-diff`)
Comparer la matrice courante au baseline (`access-matrix.baseline.json`).
Alerter (P0) si :
- une route privée passe `guard: true → false`
- une table passe `rls: true → false`
- `writable_client_cols` gagne une colonne sensible (liste `config.security.sensitiveCols`)
- un endpoint passe `public: false → true`
- une route admin perd `noindex`

### Sortie : `.project-map/access-diff.md`
Liste des régressions classées P0/P1, avec ancienne vs nouvelle valeur et
fichier concerné. Si exécuté en CI, exit non-zéro sur P0.

---

## B.3 Surface exposure score

### But
Une note 0-100 par surface pour localiser le risque.

### Surfaces
`public` / `user-private` / `admin-private` / `monitoring-private`.

### Calcul (par surface)
Partir de 100, soustraire selon les manquements pondérés :
- route privée sans garde serveur : −25 chacune (cap −60)
- table accessible sans RLS : −20 chacune (cap −60)
- admin/monitoring sans noindex : −10 chacune (cap −20)
- lien admin/monitoring présent dans le contenu public : −15
- absence de test d'accès refusé sur une zone privée : −5 chacune (cap −15)
Score plancher = 0. Documenter chaque déduction.

### Sortie : `.project-map/exposure-score.json` + section dans PROJECT_MAP.md
```json
{ "public": 100, "user-private": 90, "admin-private": 75, "monitoring-private": 80,
  "deductions": { "admin-private": ["/admin/x sans garde (-25)"] } }
```

### Règle
Le score est un indicateur, pas une garantie. Toujours lister les déductions
pour qu'il soit actionnable, jamais un chiffre opaque.
