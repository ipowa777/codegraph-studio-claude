# CodeGraph Query

Ask project questions through CodeGraph instead of reading the full repository.

Examples:

```bash
npm run codegraph:query -- "which admin routes are not protected"
npm run codegraph:query -- "what files touch Supabase"
npm run codegraph:query -- "agents quotas validation"
npm run codegraph:query -- "impact if I change billing"
```

Rules:

- Prefer CodeGraph answers before broad file reads.
- If the answer is incomplete, read only the cited files.
- Cite the outputs used.
- Keep the response short and actionable.
