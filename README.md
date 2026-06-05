# CodeGraph Studio Claude

Claude Code companion repository for installing and operating CodeGraph Studio in software projects.

Use:

```txt
CLAUDE.md
.claude/commands/
references/codegraph-reuse-directive.md
```

This repository is project-neutral. Do not add references to a client, product, or source project.

## Capabilities

- Phase 0 audit with P0/P1/P2 priorities before integration.
- Local `.project-map/` scanner with security, access surfaces, docs, agents, tests, tokens, and GraphView.
- `codegraph:preflight` before sensitive changes to list required files, blockers, warnings, commands, and estimated context tokens.
- Incremental local scan cache through `.project-map/scan-cache.json`.
- Optional stdio MCP server through `codegraph:mcp` for `query_graph`, `impact_analysis`, `security_check`, `preflight`, and `get_file_context`.
- Compact Claude Code context and sub-agent handoff packets to reduce token usage.

## Recommended Scripts

```bash
npm run codegraph:scan
npm run codegraph:security
npm run codegraph:query -- "question"
npm run codegraph:impact -- "target"
npm run codegraph:preflight -- "target"
npm run codegraph:baseline
npm run codegraph:diff
npm run codegraph:ci
npm run codegraph:github
npm run codegraph:mcp
```
