# Setup notes

Built 2026-10-18. No keys or passwords in this file — those live in Supabase
secrets and in the local, git-ignored `.env.local` / `webhook.sql`.

- **Live app:** https://open-brain-express-mu.vercel.app
- **Supabase project ref:** fdqdtdwugfbniqrembsq
- **AI model (LLM_MODEL secret):** anthropic/claude-haiku-5.5
- **Embedding model:** default (openai/text-embedding-3-small)
- **Supadata:** set up (YouTube transcripts)

## Edge functions deployed

- enrich-thought
- capture-youtube
- capture-url
- search-brain
- weekly-digest
- open-brain-mcp (with `--no-verify-jwt`)

## Database schedules

- Trigger `on_thought_created` → enrich-thought on every insert
- `weekly-brain-digest` — Sundays 08:00 UTC
- `keep-brain-awake` — Sundays and Wednesdays 09:00 UTC

## Repairs made during setup

- **Enrichment trigger:** new Supabase projects don't have
  `supabase_functions.http_request` (it only appears once a dashboard Database
  Webhook is created). `webhook.sql` instead defines
  `public.enrich_thought_on_insert()`, which calls `net.http_post` directly with
  the same `{ type, table, record }` payload a webhook sends.
- **YouTube:** YouTube refused the built-in (Innertube) route from Supabase's
  servers, so every video failed with "Could not read anything". Fixed by adding
  a Supadata key.
- **Windows:** `npx` is blocked by the PowerShell script policy on this machine;
  `npx.cmd` works.
