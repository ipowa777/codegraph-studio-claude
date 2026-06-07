# Spec — Expérience développeur (Lot E)

Trois fonctionnalités : Auto-onboarding, Handoff packet generator, Decision log/ADR.

---

## E.1 Auto-onboarding « new dev »

### But
Générer un guide d'entrée projet, utile pour un humain ET pour amorcer une IA
sur un projet inconnu en un minimum de tokens.

### Contenu généré (`.project-map/onboarding.md`)
- Pitch projet (1 paragraphe, depuis README/docs).
- Stack et structure (depuis le scan).
- Par où commencer : 5 fichiers clés (les plus centraux par fan-in).
- Flux principaux : 2-3 parcours (depuis routes + orchestration).
- Conventions à respecter (depuis config.conventions).
- Surfaces et zones sensibles (depuis access-matrix).
- Commandes utiles (scan, preflight, etc.).

### Règle
Court et orienté action. C'est une rampe d'accès, pas une doc exhaustive.
Cible : lisible en < 2 minutes, < 1500 tokens.

---

## E.2 Handoff packet generator intelligent

### But
À partir d'une description de tâche, générer le paquet minimal pour un sous-agent.

### Entrée
`codegraph:handoff "ajouter un champ X au formulaire profil"`.

### Traitement
- Identifier les fichiers concernés (recherche sémantique sur le graphe + noms).
- Réduire aux SLICES pertinentes (cf. token economy A.3).
- Rassembler : contexte minimal, contraintes (conventions applicables),
  commandes de validation (lint/test/typecheck ciblés), budget tokens estimé.

### Sortie : `.project-map/handoff-packets/<slug>.md`
```
# Handoff — <tâche>
## Contexte minimal
- fichier A (L40-90) : rôle
- fichier B (L12-30) : rôle
## Contraintes
- convention X, convention Y
## Validation
- commande 1, commande 2
## Budget estimé
~N tokens
## Interdits
- ne pas toucher Z
```

### Règle
Le paquet ne contient QUE le nécessaire. Tout ajout doit être justifié.

---

## E.3 Decision log + ADR automatique

### But
Capturer les décisions d'architecture dans des ADR structurés → mémoire de
projet durable, réutilisable entre sessions (humaines ou IA).

### Format ADR (`.project-map/adr/NNNN-titre.md`)
```
# ADR NNNN — <titre>
- Date : ISO8601
- Statut : proposé | accepté | remplacé par ADR-XXXX
## Contexte
Pourquoi cette décision se pose.
## Décision
Ce qui est décidé.
## Conséquences
Ce que ça implique (positif/négatif), règles induites.
## Liens
Fichiers/conventions/issues concernés.
```

### Capture
- Manuel : `codegraph:adr "titre"` crée un squelette pré-rempli (contexte
  inféré depuis le diff/contexte courant).
- Assisté : repérer dans les docs (CLAUDE.md, notes) les marqueurs de décision
  (ex: `[DECISION]`) et proposer de les convertir en ADR numérotés.
- Lier chaque convention de `config.conventions` à l'ADR qui la justifie.

### Index : `.project-map/adr/INDEX.md`
Liste chronologique des ADR avec statut. Les ADR sont une mémoire — versionnables
si l'équipe le souhaite (contrairement au reste de `.project-map/`), au choix
via `config.adr.versioned`.

### Règle
Une décision = un ADR court. Ne pas réécrire l'histoire : un ADR remplacé est
marqué « remplacé par », jamais supprimé.
