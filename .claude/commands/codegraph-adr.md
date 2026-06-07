# codegraph:adr

Crée un Architecture Decision Record structuré.

## Quoi faire
1. `codegraph:adr "titre"` → squelette ADR pré-rempli dans
   `.project-map/adr/NNNN-titre.md` (contexte inféré du contexte courant).
2. Repérer les marqueurs [DECISION] dans les docs et proposer de les convertir.
3. Mettre à jour `.project-map/adr/INDEX.md`.
4. Lier les conventions config à l'ADR qui les justifie.

## Règle
Une décision = un ADR court. Un ADR remplacé est marqué « remplacé par »,
jamais supprimé.

Voir references/specs/spec-dev-experience.md (E.3).
