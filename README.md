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

Then in any Claude Code session, authenticate once:

```bash
python3 ~/.claude/skills/factiq/scripts/factiq.py login --email you@example.com
```

Credentials and tokens are cached in `~/.factiq/config.json` (chmod 600) —
they are never stored in this folder.

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

No secrets belong in this repo. Auth is interactive (`login` prompts via
getpass, or reads the `FACTIQ_PASSWORD` env var); tokens live only in
`~/.factiq/config.json`. The backend endpoints enforce JWT auth, a
1 request/second rate limit, and a monthly tool-call quota per plan.
