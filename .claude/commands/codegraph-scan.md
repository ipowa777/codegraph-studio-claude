# CodeGraph Scan

Run a local CodeGraph scan and summarize the generated project map.

Steps:

1. Confirm `.project-map/` is ignored by Git.
2. Run:

```bash
npm run codegraph:scan
```

3. Read only these outputs first:

```txt
.project-map/ai-context.md
.project-map/claude-context.md
.project-map/PROJECT_MAP.md
.project-map/access-matrix.md
.project-map/security-report.md
```

4. Report:

- files scanned;
- routes detected;
- access surfaces;
- security blockers;
- security warnings;
- next recommended action.
