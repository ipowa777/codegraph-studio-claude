# CodeGraph Studio For Claude Code

Use these instructions when installing, auditing, or operating CodeGraph Studio in any software project.

## Core Rule

Stay project-neutral. Do not mention or copy references to any source project, client, product, or private example when creating reusable CodeGraph files.

## First Steps

1. Inspect the repository before editing: git status, scripts, package manager, framework, routes, docs, tests, CI, data layers, auth, agents, and sensitive surfaces.
2. Read `references/codegraph-reuse-directive.md` before implementing or adapting CodeGraph Studio.
3. Run a Phase 0 audit before installing code: P0/P1/P2 risks, docs inventory, dead code candidates, dependency/config issues, public/private/admin/monitoring surfaces, and AI agent boundaries.
4. Fix P0 and P1 blockers before full integration. If they cannot be fixed immediately, install CodeGraph in read-only mode and mark the project as not healthy.

## CodeGraph Commands

Prefer these npm script names in target projects:

```bash
npm run codegraph:scan
npm run codegraph:security
npm run codegraph:query -- "question"
npm run codegraph:impact -- target
npm run codegraph:preflight -- target
npm run codegraph:baseline
npm run codegraph:diff
npm run codegraph:ci
npm run codegraph:github
npm run codegraph:mcp
```

If the target project already uses different names, adapt the commands but keep the same behavior.

## Context Discipline

Before a sensitive change, read in this order:

```txt
.project-map/ai-context.md
.project-map/preflight.md
.project-map/claude-context.md
.project-map/change-impact.md
.project-map/access-matrix.md
.project-map/agents-manifest.json
docs/README.md
docs/roadmap.md
```

Do not read the full repository by default. Follow `.project-map/handoff-packets/` for delegated work.

## Non-Negotiables

- Never read, copy, log, or commit secrets.
- Never version `.project-map/` by default.
- Never delete files without explicit user validation.
- Run `codegraph:preflight` before sensitive changes and read only the files it names first.
- Keep generated AI contexts short and source-linked.
- Treat public, user-private, admin-private, monitoring-private, and system surfaces separately.
- Give sub-agents only the smallest handoff packet needed.
- Human validation is required for destructive, payment, production, mass email, external publication, or irreversible actions.

## Validation

After implementation or adaptation:

```bash
npm run codegraph:preflight -- "target"
npm run codegraph:scan
npm run codegraph:security
npm run codegraph:ci
```

Also run the target project's normal typecheck, tests, build, and e2e commands when available.

## Optional MCP

If Claude Code or the IDE can consume stdio MCP servers, expose `npm run codegraph:mcp` with local-only tools:

- `query_graph`
- `impact_analysis`
- `security_check`
- `preflight`
- `get_file_context`
