# FactIQ Codex Plugin

A Codex plugin that lets Codex answer economic and financial data questions
using FactIQ's cloud MCP tools: catalog search, read-only SQL, series lookup,
market data, earnings-call search, and shareable chart/report publishing.
Codex orchestrates the analysis itself; no local FactIQ data script, database
access, or API key is required.

The bundled `.mcp.json` points Codex at the FactIQ MCP server. The only local
script is `build_viz.py`, which assembles already-fetched data into a
self-contained HTML visualization and can screenshot it for iteration.

## Install

Add this repository as a Codex plugin marketplace:

```bash
codex plugin marketplace add defog-ai/factiq-codex-plugin
```

Then install the plugin:

```bash
codex plugin add factiq@factiq
```

Start a new thread after installation so Codex picks up the skill and MCP
server. If the FactIQ tools need authentication, connect the MCP server:

```bash
codex mcp login factiq
```

That opens FactIQ's browser-based OAuth flow. The same connection covers data
fetching and publishing; there is no key to paste into Codex.

For local backend development, point the `factiq` MCP server at
`http://localhost:8000/mcp` in your Codex MCP config or local plugin copy. The
default bundled endpoint is `https://api.worlddb.ai/mcp`.

## Use

Ask Codex natural-language economic or financial questions:

```text
Use FactIQ to compare US CPI inflation and wage growth since 2020
Use FactIQ to make a report on what is driving US electricity demand
Use FactIQ to build a local dashboard of US labor-market indicators
```

The skill chooses one of three output modes:

- Quick chart: one focused FactIQ share chart plus a short narrative.
- Detailed report: a shareable FactIQ research page with summary, sections,
  charts, and methodology.
- Bespoke local visualization: a self-contained local HTML file for layouts
  that do not fit the standard chart/report schemas.

## Contents

- `.codex-plugin/plugin.json` - Codex plugin manifest.
- `.mcp.json` - bundled FactIQ MCP server config.
- `.agents/plugins/marketplace.json` - marketplace entry for this plugin.
- `skills/factiq/SKILL.md` - FactIQ skill instructions.
- `skills/factiq/scripts/build_viz.py` - local-only HTML viz assembler and
  screenshot renderer.
- `skills/factiq/assets/viz-shell.html` - starter shell for bespoke vizzes.
- `skills/factiq/references/` - SQL idioms, chart/report specs, bilateral
  trade, bilateral economic-policy, and fiscal-policy revenue report patterns,
  bespoke-viz guide, and dataset
  schema overview.

## Security

No secrets belong in this repo, and the plugin holds none. Access goes through
the FactIQ MCP server's OAuth flow, so Codex stores and refreshes the token.
The backend enforces rate limits and monthly tool-call quotas; publishing
counts against the same quota as data tools.
