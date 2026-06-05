# CodeGraph Impact

Analyze the impact of a target change.

Usage examples:

```bash
npm run codegraph:impact -- src/app/admin
npm run codegraph:impact -- agents
npm run codegraph:impact -- billing
npm run codegraph:preflight -- billing
```

Steps:

1. Run `codegraph:impact` with the target.
2. Read:

```txt
.project-map/impact-plan.md
.project-map/change-impact.md
.project-map/test-plan.md
.project-map/access-matrix.md
```

3. Report:

- directly touched files;
- impacted routes;
- impacted data;
- sensitive surfaces;
- recommended tests;
- risks P0/P1/P2.

If the impact includes P0 risk, admin, monitoring, payment, auth, user data, production, destructive actions, or autonomous agents, run `npm run codegraph:preflight -- target` and read `.project-map/preflight.md` before editing.
