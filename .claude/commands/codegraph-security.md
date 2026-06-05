# CodeGraph Security

Run CodeGraph security checks and report blockers first.

Steps:

1. Run:

```bash
npm run codegraph:security
```

2. Read:

```txt
.project-map/security-report.md
.project-map/access-matrix.md
.project-map/monitoring-surfaces.md
.project-map/agents-manifest.json
```

3. Report findings in priority order:

- P0 blockers;
- P1 risks;
- P2 structural issues;
- validation commands.

Rules:

- Do not expose secrets.
- Do not quote sensitive payloads.
- Cite file paths for every finding.
