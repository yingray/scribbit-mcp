# Scribbit MCP plugin

A Claude Code plugin that connects to a [Scribbit](https://github.com/yingray/scribbit)
meeting-AI API and exposes its meetings, transcripts, summaries, and decisions as MCP
tools. The server is a single self-contained bundle — no monorepo checkout, no
`pnpm install`, no build step on your side.

## Install

```bash
claude marketplace add yingray/scribbit-mcp
# then in Claude Code:
/plugin        # install "scribbit-mcp" from the "scribbit" marketplace
/mcp           # confirm "scribbit" is connected
```

Requires **Node.js >= 18** on your PATH (Claude Code runs the bundle with `node`).

## Point it at your API

The plugin defaults to `http://localhost:3000`. To use a different API, set
`SCRIBBIT_API_BASE`:

```bash
export SCRIBBIT_API_BASE="https://your-scribbit-host.example"
```

**Important:** the value must be in the environment of the process that **launches
Claude Code**, not only in an interactive shell rc. When you start Claude from a GUI
launcher (Dock, Spotlight, an IDE), variables you exported in `~/.zshrc` may not be
inherited by the MCP server. If `SCRIBBIT_API_BASE` seems ignored, set it somewhere
the launcher can see (a login-shell profile, a launchd/systemd user environment, or
your IDE's env settings) and restart Claude Code.

## Authentication

On the first tool call against a login-enabled API the server runs a **device-flow**
authorization: it prints a link, you sign in and approve it in the Scribbit web app,
and the token is saved to `~/.config/scribbit/token.json` so you only do it once.

You can also skip the device flow by issuing a personal access token in the web app
(**Settings -> API tokens**) and exporting it as `SCRIBBIT_API_TOKEN`.

## Tools

- `list_meetings` — recent meetings
- `get_summary` — a meeting's summary
- `get_transcript` — a meeting's transcript
- `list_decisions` — canonical decisions, filterable by meeting/project/kind/tag
- `get_decision` — a single decision by id
- `search_transcripts` — full-text search across transcripts
- `sync_meetings` — pull newly recorded Meet meetings from the connected Google account(s)
- `process_meeting` — start processing a meeting (download, transcribe, summarize)

## Provenance

This bundle is generated from the Scribbit monorepo — it is not hand-edited here.

- Source: https://github.com/yingray/scribbit
- Built from commit: `85f0ca2fc48ca7d455d26be88a8bf3f5a67b54e0`
- Third-party licenses for bundled dependencies: [`plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt`](plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt)

To rebuild: run `pnpm --filter @scribbit/mcp build:plugin` in the monorepo and copy
the generated `apps/mcp/plugin-dist/` contents here.

## License

[Apache-2.0](LICENSE). Licenses for the bundled third-party dependencies are listed
in [`plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt`](plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt).
