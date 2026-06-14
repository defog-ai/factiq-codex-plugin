# FactIQ Codex Plugin

A Codex plugin that lets Codex answer economic and financial data questions
using FactIQ's data tools over HTTP: catalog search, read-only SQL, series
lookup, market data, earnings-call search, and shareable chart or report
publishing.

Codex orchestrates the analysis itself. It discovers the right data, queries
FactIQ, computes the requested metrics locally, and publishes either a focused
chart or a multi-section research report.

## Install

Add this repository as a Codex plugin marketplace:

```bash
codex plugin marketplace add defog-ai/factiq-codex-plugin
```

Then restart Codex, open the plugin directory with `/plugins`, choose the
FactIQ marketplace, and install the FactIQ plugin.

In Codex CLI environments that support direct plugin install commands, this
also works after adding the marketplace:

```bash
codex plugin add factiq@factiq
```

Start a new thread after installation and ask Codex to use FactIQ, for
example:

```text
Use FactIQ to chart US unemployment since 2019
```

## Get your API key

1. Sign in at [factiq.com](https://factiq.com) and open
   [Settings -> Security](https://factiq.com/settings/security).
2. In the API key section, click **Generate API key** or **Regenerate**.
3. Copy the `fiq_...` key immediately. Keys are shown only once.

You can store the key non-interactively:

```bash
python3 skills/factiq/scripts/factiq.py set-key --key "fiq_..."
```

For a secure prompt that keeps the key out of the conversation transcript, run:

```bash
python3 skills/factiq/scripts/factiq.py set-key
```

The key is verified against the API and cached in `~/.factiq/config.json`
with user-only file permissions. You can also set `FACTIQ_API_KEY`, which
overrides the config file.

## Use

Once installed, ask Codex natural-language economic or financial questions:

```text
Use FactIQ to compare US CPI inflation and wage growth since 2020
Use FactIQ to make a report on what is driving US electricity demand
Use FactIQ to check my FactIQ account status
```

The skill chooses one of two output modes:

- Quick chart: one focused chart plus a short narrative.
- Detailed report: a shareable research page with summary, sections, charts,
  and methodology.

## Contents

- `.codex-plugin/plugin.json` - Codex plugin manifest.
- `.agents/plugins/marketplace.json` - Marketplace entry for this plugin.
- `skills/factiq/SKILL.md` - FactIQ skill instructions.
- `skills/factiq/scripts/factiq.py` - self-contained stdlib-only CLI for
  the FactIQ `/tools/*` API. Requires Python 3.10+.
- `skills/factiq/references/` - SQL idioms, chart spec, report spec, and
  dataset schema overview.

## Configuration

The CLI targets `https://api.worlddb.ai` for API calls and
`https://www.factiq.com` for share links by default. Override with
`FACTIQ_API_URL` / `FACTIQ_WEB_URL` or `--base-url` / `--web-url`, for example
`http://localhost:8000` and `http://localhost:3000` for local development.

`set-key` remembers the URL it verified the key against in the config file.
If that saved URL later stops working, the next `set-key` run falls back to
the default API and saves whichever one works. Explicit `--base-url` and
`FACTIQ_API_URL` overrides are always honored as given.

## Security

No secrets belong in this repo. Auth uses per-user API keys. The key lives
only in `~/.factiq/config.json` or `FACTIQ_API_KEY`. The backend stores keys
hashed, enforces a 1 request/second rate limit, and applies a monthly
tool-call quota per plan.
