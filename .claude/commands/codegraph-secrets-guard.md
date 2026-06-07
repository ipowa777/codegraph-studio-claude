# codegraph:secrets-guard

Scanne le diff stagé à la recherche de secrets avant commit.

## Quoi faire
1. Récupérer `git diff --cached`.
2. Chercher les motifs de secrets (voir spec-security.md B.1) :
   clés Stripe/GitHub/AWS/Google, blocs PRIVATE KEY, assignations
   API_KEY/SECRET/TOKEN/PASSWORD, fichiers .env ajoutés.
3. Appliquer l'allowlist de config.security.secretAllowlist.
4. Si match : lister fichier:ligne + TYPE de motif, SANS afficher la valeur.
   Recommander de retirer le secret et d'utiliser des variables d'env.
5. En mode CI : exit non-zéro si match.

## Règle dure
Ne JAMAIS afficher, logger ou stocker la valeur du secret détecté.

Voir references/specs/spec-security.md (B.1).
