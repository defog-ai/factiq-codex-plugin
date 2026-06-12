# FactIQ Claude Code Skill

A [Claude Code skill](https://code.claude.com/docs/en/skills) that lets Claude
answer economic and financial data questions using FactIQ's data tools over
HTTP — catalog search, read-only SQL, series lookup, market data,
earnings-call search, and shareable chart publishing. Claude orchestrates the
whole analysis itself; no codebase or database access is required, only a
FactIQ account.

## Install

Copy (or clone) this folder to your Claude Code skills directory:

```bash
git clone git@github.com:defog-ai/factiq-skill.git ~/.claude/skills/factiq
```

## Get your API key

1. Sign in at [factiq.com](https://factiq.com) and open
   **[Settings → Security](https://factiq.com/settings/security)**.
2. In the **API key** section, click **Generate API key** (or **Regenerate**
   if one already exists — this revokes the old key).
3. Copy the `fiq_...` key immediately — it is shown only once and cannot be
   retrieved later (the server stores only a hash).

Then store it for the CLI:

```bash
python3 ~/.claude/skills/factiq/scripts/factiq.py set-key   # prompts, verifies, saves
```

The key is verified against the API and cached in `~/.factiq/config.json`
(chmod 600) — never stored in this folder. Alternatively, set the
`FACTIQ_API_KEY` env var, which overrides the config.

## Contents

- `SKILL.md` — the skill definition Claude loads (setup, workflow, limits)
- `scripts/factiq.py` — self-contained stdlib-only CLI for the FactIQ
  `/tools/*` API (Python 3.10+, no dependencies)
- `references/` — SQL idioms, ChartSpec format, and dataset schema overview

## Configuration

The CLI targets `https://api.worlddb.ai` (API) and `https://factiq.com`
(share links) by default. Override with `FACTIQ_API_URL` / `FACTIQ_WEB_URL`
env vars or `--base-url` / `--web-url` flags — e.g. `http://localhost:8000`
and `http://localhost:3000` for local development against the worlddb repos.

## Security

No secrets belong in this repo. Auth uses per-user API keys (`set-key`
prompts via getpass; `FACTIQ_API_KEY` env var also works); the key lives only
in `~/.factiq/config.json`. The backend stores keys hashed, and enforces a
1 request/second rate limit and a monthly tool-call quota per plan.
