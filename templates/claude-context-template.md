# Claude Code Context

Use this file as a compact generated context for Claude Code.

## Mission

- Project purpose:
- Current objective:
- Latest validated step:

## Read First

- .project-map/ai-context.md
- .project-map/claude-context.md
- .project-map/change-impact.md
- .project-map/access-matrix.md
- .project-map/agents-manifest.json
- docs/README.md
- docs/roadmap.md

## Boundaries

- Do not read or expose secrets.
- Do not version `.project-map/`.
- Keep public, user-private, admin-private, monitoring-private, and system surfaces separate.
- Use owner/admin checks for sensitive writes.
- Use handoff packets for sub-agents.
- Use human validation for destructive, payment, production, external publication, or irreversible actions.

## Validation

- npm run codegraph:scan
- npm run codegraph:security
- npm run codegraph:ci
