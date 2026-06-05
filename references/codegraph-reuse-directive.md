# Directive CodeGraph Studio Reutilisable

Date: 2026-06-05

Cette directive sert a deployer dans n'importe quel projet un outil local de cartographie, audit, nettoyage, securite, CI, GraphView et optimisation de contexte IA.

Elle est concue pour etre donnee telle quelle a Codex, Claude Code ou a un developpeur senior. L'objectif n'est pas seulement de scanner un projet, mais de preparer un projet existant, corriger les priorites, reduire le bruit documentaire et fournir un contexte court, fiable et reutilisable par les assistants IA.

## Directive courte a copier

```txt
Tu vas installer dans ce projet un outil local appele CodeGraph Studio.

Objectif:
- preparer le projet avant integration: audit, structure, docs, code mort, P0/P1/P2;
- scanner le workspace sans envoyer de donnees a l'exterieur;
- generer une carte projet locale dans `.project-map/`;
- detecter routes, fichiers sensibles, dependances, donnees, docs, risques, seuils CI et impact avant/apres;
- produire un preflight avant changement sensible: fichiers requis, risques, blockers, commandes de validation et budget contexte;
- utiliser un cache incremental local pour reduire les rescans et le bruit token;
- exposer si utile un MCP local en stdio pour interroger la carte projet depuis Codex, Claude Code ou un assistant compatible;
- distinguer clairement surface publique, espace utilisateur prive, dashboard admin et monitoring interne;
- inventorier les agents IA, sous-agents, outils, prompts, modeles, budgets et delegations;
- produire une topologie d'agents reutilisable par Codex, Claude Code et les agents metier du produit;
- produire un GraphView HTML local dans `.project-map/index.html`;
- produire des contextes courts utilisables par Codex et Claude Code;
- reduire la consommation de tokens en evitant de relire tout le repo;
- documenter un workflow utilisable avant chaque changement sensible.

Regles absolues:
- ne pas supprimer de fichier sans validation explicite;
- ne jamais lire, copier, logger ou versionner les secrets locaux;
- ne pas versionner `.project-map/`;
- ne pas citer de projet source, client, produit interne ou exemple proprietaire dans la version reutilisable;
- corriger les P0 et P1 avant integration complete;
- traiter les P2 structurants: docs contradictoires, code mort, structure confuse, configs obsoletes;
- separer public, prive utilisateur, admin et monitoring dans la cartographie, les risques et les tests;
- ne donner a un agent ou sous-agent que le contexte minimal, les droits minimaux et un objectif mesurable;
- garder les rapports courts, actionnables et cites par chemins de fichiers;
- rendre l'outil compatible avec Codex, Claude Code et GitHub Actions.

Ordre obligatoire:
0. Phase 0 - Pre-audit, nettoyage et priorites P0/P1/P2.
1. Corriger P0 et P1; traiter P2 structurants ou les documenter si reportes.
2. Reorganiser ou clarifier la structure si necessaire.
3. Classer et nettoyer les fichiers `.md`.
4. Cartographier les surfaces publiques/privees/admin/monitoring et leurs donnees.
5. Cartographier les agents IA, sous-agents, prompts, outils, quotas et risques si le projet en utilise ou en prevoit.
6. Installer CodeGraph Studio.
7. Ajouter scripts, tests, GraphView, preflight, CI, MCP local optionnel et documentation.
8. Valider avec tests, build, scan, security, preflight, baseline, diff, ci, github.
9. Mettre a jour roadmap/document de suivi.

Definition of Done:
- rapport P0/P1/P2 produit et traite;
- docs classees avec un index `docs/README.md` ou equivalent;
- code mort et fichiers inutiles listes, puis supprimes/adaptes seulement apres validation;
- matrice public/prive/admin/monitoring generee avec gardes, donnees et tests associes;
- `.project-map/index.html` s'ouvre localement;
- `.project-map/preflight.md` et `.project-map/preflight.json` existent pour cadrer une modification sensible;
- `.project-map/agents-manifest.json` et `.project-map/agent-topology.md` existent si le projet contient des agents;
- `codegraph:ci` passe avec 0 blocker;
- une route privee sans garde ou une action sensible sans owner/admin check fait echouer `codegraph:ci`;
- un contexte court existe pour Codex et Claude Code;
- un paquet de delegation court peut etre donne a un sous-agent sans relire tout le repo;
- la GitHub Action publie un resume et l'artefact `.project-map/`.
```

## Vision

CodeGraph Studio doit devenir un outil prive de pilotage developpeur. Il doit aider a comprendre un projet, reduire les risques de regression, prioriser les corrections et optimiser l'utilisation des assistants IA.

Il doit concurrencer un outil de type Graphify sur le besoin personnel suivant:

- carte locale du projet;
- graphe visuel;
- analyse de risques;
- preflight avant changement sensible;
- comparaison avant/apres;
- CI bloquante;
- cache incremental local;
- MCP local optionnel;
- resume GitHub;
- contexte IA court;
- cartographie agents/sous-agents;
- orchestration et delegations controlees;
- gouvernance des docs;
- workflow reutilisable sur plusieurs projets.

## Phase 0 - Pre-audit obligatoire

Avant d'integrer le code CodeGraph, faire un audit sans modifier le projet.

### 0.1 Etat Git et environnement

Relever:

- branche active;
- `git status`;
- scripts disponibles;
- gestionnaire de paquets;
- version Node/Python/runtime;
- presence d'une CI;
- tests existants;
- build existant;
- erreurs deja presentes.

Ne pas confondre dette existante et regressions introduites pendant l'installation.

### 0.2 Audit structure

Analyser:

- arborescence;
- separation frontend/backend;
- routes publiques/privees/admin/API;
- modules metier;
- scripts;
- tests;
- docs;
- CI;
- fichiers racine.

Produire:

```txt
docs/audit/STRUCTURE_AUDIT.md
```

Contenu attendu:

- structure actuelle;
- zones claires;
- zones confuses;
- propositions de reorganisation;
- actions sans risque;
- actions a valider avant modification.

### 0.3 Audit code mort et fichiers inutiles

Lister sans supprimer:

- fichiers non importes;
- composants non references;
- scripts jamais appeles;
- configs obsoletes;
- tests doublons;
- assets inutilises;
- anciens prototypes;
- dossiers generes versionnes par erreur.

Classer:

- `a supprimer`;
- `a fusionner`;
- `a archiver`;
- `a conserver`;
- `incertain`.

Regle: supprimer uniquement apres validation utilisateur ou decision explicite du mainteneur.

### 0.4 Audit documentation `.md`

Les projets contiennent souvent trop de `.md`. Avant CodeGraph, classer les docs.

Categories:

- `source de verite`;
- `operationnel`;
- `strategie`;
- `audit`;
- `archive`;
- `obsolete`;
- `contradictoire`;
- `a fusionner`;
- `a supprimer apres validation`.

Creer ou mettre a jour:

```txt
docs/README.md
```

Ce fichier doit devenir l'index documentaire. Il doit dire quoi lire en premier et quoi ne pas traiter comme source de verite.

Regles:

- ne pas supprimer un `.md` seulement parce qu'il est ancien;
- archiver si le doute existe;
- supprimer uniquement si le document est faux, duplique et non utile;
- signaler les contradictions entre roadmap, audit et code;
- ne pas donner le meme poids a toutes les docs dans le contexte IA.

### 0.5 Audit dependances et configs

Analyser:

- dependances inutilisees;
- dependances obsoletes;
- scripts npm incoherents;
- configs dupliquees;
- workflows CI inutilises;
- variables `.env.example` manquantes ou fausses.

Ne pas mettre a jour massivement les dependances avant d'avoir identifie les risques.

### 0.6 Audit agents IA et sous-agents

Si le projet utilise deja des agents IA, des assistants, des workflows autonomes, des prompts versionnes, des outils externes ou une orchestration multi-etapes, produire:

```txt
docs/audit/AGENTS_AUDIT.md
```

Le rapport doit lister:

- agents metier existants;
- agents developpeur ou assistants attendus;
- sous-agents possibles;
- prompts systeme, prompts metier et prompts utilisateur;
- outils autorises par agent;
- modeles utilises;
- cout par execution si connu;
- quotas et limites;
- donnees accessibles;
- actions autonomes possibles;
- points de validation humaine;
- risques de boucle, hallucination, action irreversible ou surcout.

Classer chaque agent avec:

```txt
nom
role
objectif
entrees
sorties
outils
autonomie: lecture | suggestion | action avec validation | action autonome
budget: tokens, cout, temps, nombre d'appels
risques
garde-fous
tests attendus
```

Si le projet ne contient pas encore d'agents mais en prevoit, creer un inventaire cible minimal pour eviter d'ajouter plus tard une orchestration floue.

### 0.7 Audit public, prive, admin et monitoring

Si le projet contient une partie accessible publiquement et un dashboard prive de monitoring, les traiter comme des surfaces separees.

Produire:

```txt
docs/audit/ACCESS_SURFACES.md
```

Classer chaque route, page, endpoint, action serveur, webhook et table/vue de donnees:

```txt
surface: public | user-private | admin-private | monitoring-private | system
auth: none | user-session | admin-role | service-role | signed-webhook
indexation: indexable | noindex | never-public
donnees: publiques | utilisateur | admin | operationnelles | sensibles | secrets
actions: lecture | ecriture | suppression | paiement | email | IA | production
validation: aucune | session | owner-check | admin-check | signature | validation humaine
tests requis
risques P0/P1/P2
```

Definitions recommandees:

- `public`: landing, pricing, blog, documentation publique, assets publics, pages SEO;
- `user-private`: dashboard client, profil, donnees personnelles, historique, facturation utilisateur;
- `admin-private`: administration interne, moderation, utilisateurs, abonnements, support, parametres;
- `monitoring-private`: metriques, logs, runs agents, couts API, erreurs, jobs, webhooks, sante systeme;
- `system`: jobs planifies, edge functions, webhooks, workers, scripts de synchronisation.

Regles non negociables:

- le public ne doit pas exposer de lien direct vers admin ou monitoring;
- admin et monitoring doivent etre `noindex` ou equivalents;
- admin et monitoring exigent une garde explicite cote serveur, pas seulement un masquage UI;
- les donnees de monitoring ne doivent pas contenir de secrets, tokens, payloads bruts sensibles ou informations personnelles inutiles;
- les endpoints monitoring doivent etre proteges par role admin, signature, IP allowlist ou mecanisme equivalent selon le contexte;
- les dashboards prives doivent avoir des tests qui prouvent le refus d'acces anonyme et non admin;
- les logs affiches en UI doivent etre masques ou tronques si risque de secret;
- les actions admin destructives ou production exigent confirmation explicite et trace d'audit;
- les agents IA ne doivent pas pouvoir lire admin/monitoring sans mission explicite et paquet de delegation limite.

Sorties attendues:

```txt
.project-map/access-surfaces.json
.project-map/access-matrix.md
.project-map/monitoring-surfaces.md
```

La matrice doit permettre a Codex ou Claude Code de savoir immediatement si une modification touche:

- une surface publique;
- une zone utilisateur connectee;
- une zone admin;
- un dashboard monitoring;
- une donnee sensible;
- une action a risque.

## Priorites P0/P1/P2/P3

Avant l'integration complete, produire un rapport:

```txt
docs/audit/PRIORITY_FIXES.md
```

### P0 - Critique

A corriger avant tout:

- secrets exposes ou logs de secrets;
- route privee accessible publiquement;
- auth/session cassee;
- donnees utilisateur lisibles par un autre utilisateur;
- action destructive sans garde;
- webhook sans signature;
- service/admin key utilisee cote client;
- migration ou policy exposant des donnees sensibles;
- envoi email/SMS, publication externe ou action utilisateur sensible sans validation explicite;
- paiement ou abonnement manipulable.

### P1 - Important

A corriger avant integration complete si possible:

- permissions trop larges;
- owner/admin check faible;
- fallback demo actif en production;
- erreurs avalees sur chemins sensibles;
- tests manquants sur auth, billing, donnees, IA, quotas;
- dependances critiques non verrouillees;
- CI absente sur chemins sensibles;
- logique de cout IA non bornee.

### P2 - Structurant

A traiter avant ou pendant l'installation:

- code mort probable;
- fichiers inutiles;
- docs contradictoires;
- structure confuse;
- scripts obsoletes;
- duplication forte;
- conventions incoherentes;
- tests trop fragiles;
- README ou roadmap non fiables.

### P3 - Confort

Peut rester en backlog:

- refactor cosmetique;
- meilleure ergonomie dev;
- visualisations avancees;
- classification plus fine;
- exports supplementaires.

## Decision avant integration

Apres la Phase 0:

1. Corriger tous les P0.
2. Corriger les P1 bloquants.
3. Nettoyer ou documenter les P2 structurants.
4. Mettre a jour l'index documentaire.
5. Lancer seulement ensuite l'installation CodeGraph Studio.

Si P0/P1 ne peuvent pas etre corriges tout de suite, CodeGraph peut etre installe en mode lecture seule, mais le rapport doit marquer le projet comme `non sain`.

## Installation CodeGraph Studio

### Fichiers a creer

Adapter les noms au projet:

```txt
scripts/project-codegraph.mjs
scripts/project-codegraph-mcp.mjs
scripts/lib/codegraph/
  config.mjs
  build-graph.mjs
  scan-files.mjs
  parse-imports.mjs
  parse-routes.mjs
  parse-data.mjs
  parse-docs.mjs
  risk-rules.mjs
  baseline-diff.mjs
  gates.mjs
  preflight.mjs
  github-report.mjs
  visual-graph.mjs
  graphview.mjs
  write-reports.mjs
```

Ajouter les tests:

```txt
scripts/lib/codegraph/*.test.mjs
```

### Scripts

```json
{
  "codegraph:scan": "node scripts/project-codegraph.mjs scan",
  "codegraph:report": "node scripts/project-codegraph.mjs report",
  "codegraph:security": "node scripts/project-codegraph.mjs security",
  "codegraph:query": "node scripts/project-codegraph.mjs query",
  "codegraph:impact": "node scripts/project-codegraph.mjs impact",
  "codegraph:preflight": "node scripts/project-codegraph.mjs preflight",
  "codegraph:baseline": "node scripts/project-codegraph.mjs baseline",
  "codegraph:diff": "node scripts/project-codegraph.mjs diff",
  "codegraph:ci": "node scripts/project-codegraph.mjs ci",
  "codegraph:github": "node scripts/project-codegraph.mjs github",
  "codegraph:mcp": "node scripts/project-codegraph-mcp.mjs"
}
```

### `.gitignore`

Ajouter:

```txt
.project-map/
```

## Sorties attendues

```txt
.project-map/
  index.html
  PROJECT_MAP.md
  STATUS_SUMMARY.md
  ai-context.md
  codex-context.md
  claude-context.md
  security-report.md
  preflight.md
  preflight.json
  product-flows.md
  architecture-graph.md
  architecture-graph.mmd
  change-impact.md
  ci-gates.md
  github-summary.md
  files.json
  dependencies.json
  routes.json
  access-surfaces.json
  access-matrix.md
  monitoring-surfaces.md
  data.json
  docs.json
  symbols.json
  agents-manifest.json
  agent-topology.md
  subagents.md
  agent-runs-schema.json
  baseline.json
  diff.json
  gates.json
  scan-cache.json
  handoff-packets/
```

### Contextes IA

Pour optimiser les tokens:

- `ai-context.md`: contexte assistant generique;
- `codex-context.md`: contexte court pour Codex;
- `claude-context.md`: contexte court pour Claude Code.
- `preflight.md`: contexte minimal avant une modification ciblee.

Chaque fichier doit:

- tenir sous 150 a 250 lignes;
- ne jamais contenir de contenu complet de fichiers;
- lister les fichiers a lire avant changement;
- prioriser les zones sensibles;
- inclure les commandes de validation;
- expliquer les regles non negociables;
- pointer vers les docs sources de verite.

### Agents IA et sous-agents

CodeGraph Studio doit pouvoir cartographier deux familles d'agents:

- agents produit: agents visibles ou utilises par l'application, par exemple collecte, scoring, generation documentaire, validation, publication, controle budget;
- agents developpeur: assistants ou sous-agents utilises par Codex, Claude Code ou un workflow CI pour analyser, corriger, tester ou documenter le projet.

Un agent est une unite specialisee avec un objectif, des entrees, des sorties, des outils et un niveau d'autonomie. Un sous-agent est une delegation limitee dans le temps, avec un paquet de contexte court, une mission precise et aucun acces inutile au reste du projet.

#### Manifest agents

Generer:

```txt
.project-map/agents-manifest.json
```

Structure minimale:

```json
{
  "agents": [
    {
      "id": "security-auditor",
      "name": "Security Auditor",
      "family": "developer",
      "role": "Detecte les risques auth, data, secrets, quotas et actions sensibles.",
      "autonomy": "suggestion",
      "inputs": ["routes.json", "data.json", "security-report.md"],
      "outputs": ["security-report.md", "change-impact.md"],
      "tools": ["read", "rg", "test"],
      "forbidden": [".env*", "secrets", "production-write"],
      "budget": {
        "max_context_lines": 250,
        "max_runtime_seconds": 120
      },
      "requires_human_validation": true
    }
  ],
  "subagent_policy": {
    "minimal_context_only": true,
    "no_secret_access": true,
    "no_destructive_action": true,
    "must_return_sources": true
  }
}
```

#### Topologie agents

Generer:

```txt
.project-map/agent-topology.md
```

Contenu attendu:

- orchestrateur principal;
- agents specialises;
- sous-agents possibles;
- ordre d'intervention;
- donnees transmises;
- validations humaines;
- points d'arret;
- couts variables;
- risques.

Topologie recommandee pour un outil concurrent de Graphify:

- `orchestrator`: decide quoi scanner, quoi lire et quoi deleguer;
- `project-cartographer`: produit la carte projet;
- `security-auditor`: detecte les surfaces sensibles;
- `docs-curator`: classe, fusionne et signale les docs contradictoires;
- `dead-code-finder`: repere fichiers inutiles, doublons et imports morts;
- `impact-analyst`: compare baseline/diff et calcule le risque;
- `test-planner`: propose les tests minimums;
- `token-optimizer`: genere les contextes courts Codex/Claude;
- `graph-query-agent`: repond aux questions sur le graphe;
- `review-agent`: verifie la coherence avant commit.

#### Sous-agents

Generer:

```txt
.project-map/subagents.md
.project-map/handoff-packets/
```

Chaque paquet de delegation doit contenir seulement:

- objectif;
- contexte produit en 5 a 15 lignes;
- fichiers a lire;
- fichiers interdits;
- commandes autorisees;
- sortie attendue;
- criteres de succes;
- limite de temps ou de tokens;
- obligation de citer les chemins lus.

Exemple:

```txt
Mission: verifier les risques de surcout IA.
Lire:
- .project-map/agents-manifest.json
- .project-map/data.json
- src/lib/ai/*
Ne pas lire:
- .env*
- node_modules
Sortie:
- 5 risques maximum
- fichiers cites
- priorite P0/P1/P2
```

#### Traces et couts

Generer:

```txt
.project-map/agent-runs-schema.json
```

Ce schema doit decrire comment tracer:

- agent;
- sous-agent parent si applicable;
- objectif;
- fichiers lus;
- outils utilises;
- model/provider si applicable;
- tokens estimes;
- cout estime;
- statut;
- erreurs;
- decision humaine;
- horodatage.

Les traces servent a:

- comprendre les decisions;
- eviter les boucles;
- mesurer les couts;
- comparer Codex et Claude Code;
- savoir quel agent a produit quelle recommandation.

#### Regles d'orchestration

- Un agent ne doit jamais recevoir tout le repo par defaut.
- Un sous-agent ne doit jamais recevoir de secret.
- Une action destructive ou irreversible exige validation humaine.
- Une action paiement, admin, production, email massif, publication externe ou action irreversible exige validation humaine.
- Les agents autonomes doivent avoir quota, timeout et journalisation.
- Les agents qui generent du texte utilisateur doivent garder les sources et hypotheses.
- Les agents produit et developpeur doivent partager une taxonomie commune: objectif, entree, sortie, risque, cout, validation.

## Optimisation tokens

Objectif: reduire la quantite de contexte que Codex ou Claude Code doit relire.

### Regles anti-bruit

Ne jamais injecter dans les contextes IA:

- contenu complet des fichiers;
- logs;
- builds;
- artefacts;
- `node_modules`;
- `.project-map`;
- anciens rapports;
- docs archivees sauf reference utile;
- secrets;
- dumps SQL complets;
- gros fichiers lock sauf besoin precis.

### Sorties a deux niveaux

Produire:

- `PROJECT_MAP.md`: carte complete mais concise;
- `ai-context.md`: ultra court;
- `preflight.md`: fichiers requis, blockers, commandes et budget contexte avant changement sensible;
- `change-impact.md`: uniquement ce qui a change;
- `github-summary.md`: synthese PR/CI;
- `handoff-packets/`: contextes minuscules pour sous-agents;
- `index.html`: exploration humaine locale.

### Budget recommande

Pour un assistant IA:

- lire d'abord `ai-context.md`;
- lancer `codegraph:preflight` et lire `preflight.md` avant tout changement sensible;
- lire ensuite `change-impact.md` s'il existe;
- lire seulement les fichiers cites;
- ne lire les docs strategiques que si la tache est produit/business;
- ne lire les migrations que si la tache touche les donnees.

Pour un sous-agent IA:

- lire uniquement son paquet dans `.project-map/handoff-packets/`;
- lire uniquement les fichiers autorises dans ce paquet;
- retourner une synthese courte avec sources;
- ne pas ouvrir de nouveau contexte sans justification.

Impact attendu:

- moins de tokens consommes;
- moins de bruit;
- moins d'oublis de securite;
- meilleure continuite entre sessions;
- meilleure compatibilite Codex/Claude.

## Regles de risque minimales

Adapter au projet, mais inclure au minimum:

- route privee sans garde;
- route admin ou monitoring exposee publiquement;
- lien admin/monitoring visible depuis une landing publique;
- dashboard prive indexable par moteur de recherche;
- endpoint API sensible sans signature/session;
- action serveur qui modifie des donnees sans owner/admin check;
- usage de cle service/admin;
- logging de secrets;
- fichier `.env` versionne;
- policy permissive sur donnees sensibles;
- webhook sans verification de signature;
- paiement/abonnement;
- IA ou cout variable;
- quota absent;
- agent autonome sans limite;
- sous-agent sans perimetre;
- prompt sensible expose;
- outil externe appele sans validation;
- envoi email/SMS;
- action irreversible;
- suppression de donnees;
- upload/download de fichier;
- source externe;
- contradiction docs/code;
- doc source de verite obsolete.

## Seuils CI

Variables recommandees:

```txt
CODEGRAPH_MAX_BLOCKERS=0
CODEGRAPH_MAX_WARNINGS=0
```

Le mode CI doit echouer si:

- blockers > seuil;
- warnings > seuil;
- diff introduit un nouveau blocker;
- une route privee devient non protegee;
- une integration sensible apparait sans garde identifiable;
- une action sensible apparait sans test ou validation;
- un fichier secret local est versionne.
- une route admin ou monitoring n'a pas de garde serveur;
- une page admin ou monitoring est indexable;
- une surface publique reference une route admin/monitoring;
- un dashboard monitoring expose des payloads bruts sensibles.

## GitHub Action type

```yaml
name: CodeGraph

on:
  workflow_dispatch:
  pull_request:
  push:
    branches:
      - main

jobs:
  codegraph:
    runs-on: ubuntu-latest
    env:
      CODEGRAPH_MAX_BLOCKERS: "0"
      CODEGRAPH_MAX_WARNINGS: "0"

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm run codegraph:ci
      - if: always()
        uses: actions/upload-artifact@v4
        with:
          name: project-codegraph
          path: .project-map/
          if-no-files-found: ignore
```

## GraphView local

Le fichier:

```txt
.project-map/index.html
```

Doit etre:

- autonome;
- consultable en local;
- sans serveur obligatoire;
- sans CDN obligatoire;
- sans contenu complet de fichiers;
- filtre par risque;
- recherche par fichier/route/tag;
- graphe visuel;
- panneaux routes, fichiers sensibles, impact CI, findings;
- utilisable par un humain avant une refonte.

## Workflow quotidien

Avant changement sensible:

```bash
npm run codegraph:baseline
npm run codegraph:scan
npm run codegraph:preflight -- "zone ou changement vise"
```

Lire:

```txt
.project-map/preflight.md
.project-map/ai-context.md
.project-map/codex-context.md ou .project-map/claude-context.md
.project-map/index.html
```

Apres changement:

```bash
npm run codegraph:diff
npm run codegraph:ci
npm run test
```

Puis:

- corriger les blockers;
- mettre a jour roadmap;
- mettre a jour docs si le comportement a change;
- commit/push uniquement apres validation.

## Compatibilite Codex et Claude Code

### Pour Codex

Codex doit lire en priorite:

```txt
.project-map/preflight.md
.project-map/codex-context.md
.project-map/change-impact.md
.project-map/agents-manifest.json
docs/README.md
docs/roadmap.md
```

Pour deleguer a un sous-agent, Codex doit creer ou lire un paquet court dans:

```txt
.project-map/handoff-packets/
```

Le sous-agent ne doit pas recevoir plus de contexte que ce paquet et les fichiers cites.

### Pour Claude Code

Claude Code doit lire en priorite:

```txt
.project-map/preflight.md
.project-map/claude-context.md
.project-map/change-impact.md
.project-map/agents-manifest.json
docs/README.md
docs/roadmap.md
```

Pour Claude Code, le meme principe s'applique: une delegation doit partir d'un paquet court, avec objectif, fichiers autorises, fichiers interdits et sortie attendue.

Si le projet utilise deja un fichier d'instructions Claude, par exemple:

```txt
CLAUDE.md
.claude/
```

Alors ajouter une section:

```txt
Avant chaque changement sensible, executer CodeGraph:
1. npm run codegraph:scan
2. npm run codegraph:preflight -- "zone ou changement vise"
3. lire .project-map/preflight.md puis .project-map/claude-context.md
4. si auth/data/payment/IA: npm run codegraph:security
5. apres changement: npm run codegraph:diff && npm run codegraph:ci
```

### Assistant neutre

Si plusieurs assistants sont utilises, maintenir:

```txt
.project-map/ai-context.md
```

comme contexte commun, puis garder les variantes Codex/Claude pour les consignes specifiques.

Si le projet utilise des sous-agents, maintenir aussi:

```txt
.project-map/agents-manifest.json
.project-map/agent-topology.md
.project-map/handoff-packets/
```

comme couche commune de delegation.

### MCP local optionnel

Si l'assistant ou l'IDE peut consommer un MCP stdio, ajouter `codegraph:mcp`.

Outils recommandes:

- `query_graph`: repondre a une question projet depuis la carte locale;
- `impact_analysis`: produire le plan d'impact d'une zone;
- `security_check`: retourner blockers, warnings et findings;
- `preflight`: retourner fichiers requis, commandes, blockers, warnings et budget contexte;
- `get_file_context`: retourner les metadonnees d'un fichier sans exposer tout son contenu.

Regles MCP:

- stdio local uniquement par defaut;
- aucun reseau requis;
- pas de secrets dans les reponses;
- pas de contenu complet de fichier sauf demande explicite et sure;
- les outils doivent reconstruire ou lire une carte locale a jour avant de repondre.

## Adaptation par type de projet

### SaaS web

Priorites:

- routes;
- auth;
- separation public/user/admin/monitoring;
- admin;
- monitoring prive;
- billing;
- base de donnees;
- webhooks;
- emails.

Pour un SaaS avec landing publique et dashboard prive:

- la landing doit etre traitee comme surface publique indexable;
- l'espace client doit etre traite comme `user-private`;
- l'admin et le monitoring doivent etre traites comme `admin-private` ou `monitoring-private`;
- aucun lien admin/monitoring ne doit apparaitre dans le footer, header ou contenu public;
- les routes admin/monitoring doivent avoir garde serveur, noindex et tests d'acces refuse;
- les metriques, logs et traces agents doivent etre masquees si elles contiennent donnees utilisateur, couts, payloads ou erreurs sensibles.

### Application IA

Priorites:

- couts API;
- prompts;
- modeles;
- quotas;
- donnees utilisateur;
- traces;
- orchestration agents;
- sous-agents et delegations;
- hallucination ou generation non validee.

### Marketplace / e-commerce

Priorites:

- paiement;
- commandes;
- remboursements;
- stocks;
- webhooks;
- droits vendeurs/acheteurs.

### Projet data

Priorites:

- pipelines;
- schemas;
- sources externes;
- exports;
- donnees sensibles;
- retention.

### Projet mobile

Priorites:

- endpoints backend;
- permissions;
- stockage local;
- auth;
- analytics;
- deep links.

## Maintenance

- Relancer CodeGraph avant toute refonte large.
- Lancer `codegraph:preflight` avant toute modification sensible ou multi-surface.
- Ne jamais versionner `.project-map/` par defaut.
- Versionner uniquement le code du scanner, les tests, la workflow et la documentation.
- Ajouter une regle de risque des qu'une nouvelle surface sensible apparait.
- Traiter un nouveau blocker comme une regression prioritaire.
- Refaire un audit docs/code mort tous les mois ou avant une grosse release.
- Garder les contextes IA courts pour proteger le budget token.

## Checklist finale

- [ ] Phase 0 realisee.
- [ ] Rapport P0/P1/P2 produit.
- [ ] P0 corriges.
- [ ] P1 corriges ou explicitement acceptes.
- [ ] P2 structurants traites.
- [ ] Docs `.md` classees.
- [ ] `docs/README.md` cree ou mis a jour.
- [ ] Surfaces public/user/admin/monitoring cartographiees.
- [ ] Admin et monitoring proteges cote serveur.
- [ ] Admin et monitoring marques noindex ou equivalent.
- [ ] Landing publique sans lien admin/monitoring.
- [ ] Donnees monitoring masquees si sensibles.
- [ ] `.project-map/` ignore par Git.
- [ ] Scripts CodeGraph ajoutes.
- [ ] Tests CodeGraph ajoutes.
- [ ] GraphView genere.
- [ ] Preflight genere pour au moins une zone sensible.
- [ ] Cache incremental local actif ou explicitement desactive.
- [ ] MCP local ajoute si l'IDE ou l'assistant cible le supporte.
- [ ] Contextes Codex/Claude generes.
- [ ] Agents IA inventories si presents ou prevus.
- [ ] Topologie agents/sous-agents generee.
- [ ] Paquets de delegation sous-agents definis.
- [ ] Budget, risques et validation humaine definis pour chaque agent.
- [ ] CI CodeGraph ajoutee.
- [ ] Roadmap mise a jour.
- [ ] Validation complete passee.
