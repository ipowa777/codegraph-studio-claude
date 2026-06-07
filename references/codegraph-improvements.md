# CodeGraph Studio — Roadmap d'améliorations (v3)

Améliorations innovantes proposées, classées par axe : économie de tokens,
sécurité, gestion des agents IA, maintien de performance, et développement.
Chaque item indique l'intérêt, le niveau d'effort et une note d'implémentation.

Légende effort : 🟢 faible · 🟡 moyen · 🔴 élevé

---

## Axe 1 — Économie de tokens

### 1.1 Token Ledger — compteur dépensé / économisé 🟢→🔴
Le différenciateur principal. CodeGraph fait déjà l'économie (preflight =
contexte minimal au lieu de relire le repo) mais elle est invisible.
Le Token Ledger la rend mesurable.

- À chaque `codegraph:preflight`, journaliser :
  - `baseline_tokens` : coût si on lisait en entier les fichiers jugés pertinents
  - `actual_tokens` : coût réel du contexte minimal fourni (slices ciblées)
  - `saved_tokens`, `saved_pct`, `saved_usd`
- Cumul dans `.project-map/token-savings.json` (jamais versionné).
- Commande `codegraph:savings` → résumé semaine/mois + total cumulé.
- **Baseline honnête** : utiliser "fichiers pertinents en entier" par défaut
  (conservateur, crédible), PAS "repo complet" (gonflé, peu réaliste).
  Toujours afficher la méthode de calcul à côté du chiffre.
- Estimation : ~1 token ≈ 4 caractères (en) / ~3,5 (fr/code). Conversion
  directe depuis la taille des fichiers déjà mesurée par le scan.
- Chiffrage $ : réutiliser un calculateur de coût par modèle (tarifs input/output).
- Niveaux : v1 estimation statique par preflight 🟢 · v2 cumul + GraphView 🟡 ·
  v3 extension VS Code (barre de statut live) 🔴.

### 1.2 Context budget enforcer 🟡
Le preflight définit un plafond de tokens par tâche. Si le contexte requis
dépasse le plafond, CodeGraph alerte et propose de découper en sous-tâches
(handoff packets). Empêche les sessions qui explosent silencieusement le budget.

### 1.3 Slices de fichiers (plages de lignes) 🟡
Au lieu de pointer un fichier entier (« lis Courses.tsx », 1100 lignes), le
preflight pointe les plages précises (« Courses.tsx L240-290 »). Le contexte
minimal devient chirurgical. Nécessite un index symboles → lignes lors du scan.

---

## Axe 2 — Sécurité

### 2.1 Secret leak guard (pre-commit) 🟢
Hook git qui scanne le diff avant commit : clés API, tokens, fichiers .env.
Bloque le commit si fuite détectée. CodeGraph ne lit jamais les secrets en
contenu, mais peut détecter leur présence accidentelle dans un diff (patterns
sk_, whsec_, ghp_, AKIA, PRIVATE KEY, etc.).

### 2.2 Access matrix diff — alerte régression de sécurité 🟡
Compare la matrice d'accès (qui accède à quoi) entre deux scans. Alerte si :
- une route privée perd sa garde serveur
- une table perd sa RLS
- un endpoint admin devient accessible publiquement
- une colonne sensible devient writable côté client
Aurait détecté automatiquement une faille d'escalade de privilèges (ex: colonnes
d'abonnement modifiables par l'utilisateur via un update profil non restreint).

### 2.3 Surface exposure score 🟡
Note 0-100 par surface (public / user-private / admin-private / monitoring) :
présence de garde serveur, noindex, RLS, tests d'accès refusé, absence de lien
sensible dans le contenu public. Un chiffre unique pour localiser le risque.

---

## Axe 3 — Gestion des agents IA

### 3.1 Agent cost cartography 🟡
Cartographie le coût de chaque agent : modèle utilisé, taille de prompt système,
quota, coût estimé par appel. Se branche sur une table de coûts réelle si elle
existe (ex: `cost_tracking`). Identifie l'agent le plus cher et les prompts
candidats à l'optimisation. Alimente un dashboard financier (ex: Forj Ops).

### 3.2 Prompt drift detection 🟡
Détecte les changements de prompt système entre deux scans, et signale les
prompts sans garde-fou :
- pas de limite max_tokens
- pas de validation de la sortie
- données utilisateur injectées sans échappement (risque d'injection de prompt)

### 3.3 Orchestration map + détection de boucles 🔴
Cartographie qui appelle qui (agent → sous-agent → outil). Détecte les boucles
de délégation et les agents pouvant en déclencher d'autres sans limite
(risque de coût explosif / récursion non bornée).

### 3.4 Hallucination surface flag 🟡
Repère les chemins où une sortie IA non validée alimente une action sensible
(écriture DB, paiement, envoi d'email, publication) sans étape de vérification
intermédiaire. Point critique des applications IA.

---

## Axe 4 — Maintien de performance / qualité

### 4.1 Health score 0-100 + tendance 🟡
Score composite unique : sécurité + dette technique + couverture docs + dérive
d'architecture + duplication. Historisé pour suivre la tendance dans le temps.
Un KPI de santé du code visible d'un coup d'œil.

### 4.2 Architecture drift detector 🟡
CodeGraph apprend les conventions du projet (déclarées dans la config ou
inférées) et alerte dès qu'un nouveau fichier les viole. Exemples de règles :
- appels réseau via un wrapper unique (jamais `fetch()` direct)
- `.maybeSingle()` au lieu de `.single()`
- pas d'accès direct à une table réservée à une couche donnée
Transforme les conventions tacites en garde-fous automatiques.

### 4.3 Hotspot & refactor radar 🟡
Croise trois signaux : taille du fichier × nombre d'imports entrants ×
fréquence de modification (git log). Les fichiers gros + très importés +
souvent touchés sont les candidats refactor prioritaires (risque maximal).

---

## Axe 5 — Développement / expérience

### 5.1 Auto-onboarding « new dev » 🟢
Génère un guide d'entrée projet : par où commencer, fichiers clés, flux
principaux, conventions, surfaces. Utile pour un humain qui arrive ET pour
amorcer une IA sur un projet inconnu avec un minimum de tokens.

### 5.2 Handoff packet generator intelligent 🟡
À partir d'une description de tâche, génère le paquet minimal pour un sous-agent :
fichiers concernés (en slices), contexte nécessaire, contraintes, commandes de
validation. Rien de plus que le strict nécessaire.

### 5.3 Decision log + ADR automatique 🟢
Capture les décisions d'architecture dans des ADR (Architecture Decision Records)
structurés. Relie chaque règle/convention à son contexte d'origine → mémoire de
projet durable, réutilisable par l'IA entre sessions.

---

## Priorisation suggérée

Quick wins (fort impact / effort faible) :
1. Token Ledger v1 (1.1) — le différenciateur, mesurable tout de suite
2. Secret leak guard (2.1) — sécurité immédiate
3. Auto-onboarding (5.1) — valeur immédiate, peu de code
4. Decision log / ADR (5.3) — capitalise la mémoire projet

Fort impact / effort moyen :
5. Access matrix diff (2.2) — détection régression sécurité
6. Architecture drift detector (4.2) — fait respecter les conventions
7. Agent cost cartography (3.1) — pilotage coûts IA
8. Health score (4.1) — KPI global

Ambitieux / plus tard :
9. Token Ledger v3 extension VS Code (1.1)
10. Orchestration map + boucles (3.3)
