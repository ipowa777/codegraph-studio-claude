# CodeGraph Preflight

Prepare a safe, minimal context before a sensitive or multi-surface change.

Usage examples:

```bash
npm run codegraph:preflight -- "src/app/admin"
npm run codegraph:preflight -- "billing webhook"
npm run codegraph:preflight -- "agents quota"
```

Steps:

1. Run `codegraph:preflight` with the target.
2. Read:

```txt
.project-map/preflight.md
.project-map/ai-context.md
.project-map/claude-context.md
```

3. Open only the files listed in `Required Files` unless the preflight justifies widening context.
4. Report:

- readiness;
- blockers;
- warnings;
- required files;
- validation commands;
- estimated context tokens;
- human validations needed.

If P0 risk, admin, monitoring, payment, production data, destructive action, or autonomous agent behavior is involved, stop for human validation before editing.
