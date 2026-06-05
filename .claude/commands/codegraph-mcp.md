# CodeGraph MCP

Use or verify the optional local stdio MCP server for CodeGraph Studio.

Run:

```bash
npm run codegraph:mcp
```

Expected local tools:

- `query_graph`
- `impact_analysis`
- `security_check`
- `preflight`
- `get_file_context`

Rules:

- Keep the MCP local by default.
- Do not expose secrets, logs, `.env` files, or full file contents.
- Prefer metadata, cited paths, summaries, blockers, commands, and handoff packets.
- Rebuild or refresh the local CodeGraph map before returning answers when accuracy matters.

Validation:

```bash
npm run codegraph:scan
npm run codegraph:preflight -- "mcp"
npm run codegraph:ci
```
