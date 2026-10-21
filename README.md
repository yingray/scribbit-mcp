# Scribbit MCP plugin

A plugin for **Claude Code** and **Codex** that connects to a
[Scribbit](https://github.com/yingray/scribbit) meeting-AI API and exposes its
meetings, transcripts, and summaries as MCP tools. The server is a single
self-contained bundle — no monorepo checkout, no `pnpm install`, no build step on
your side.

## Install

Requires **Node.js >= 18** on your PATH (both clients run the bundle with `node`).

### Claude Code

```bash
claude marketplace add yingray/scribbit-mcp
# then in Claude Code:
/plugin        # install "scribbit-mcp" from the "scribbit" marketplace
/mcp           # confirm "scribbit" is connected
```

### Codex

```bash
codex plugin marketplace add yingray/scribbit-mcp
codex plugin add scribbit-mcp@scribbit
codex mcp list   # confirm "scribbit" is listed and enabled
```

Tested with codex-cli 0.153.4.

## Point it at your API

The plugin defaults to `http://localhost:3000`. To use a different API, set
`SCRIBBIT_API_BASE`:

```bash
export SCRIBBIT_API_BASE="https://your-scribbit-host.example"
```

**Important:** the value must be in the environment of the process that **launches
the client** (Claude Code or Codex), not only in an interactive shell rc. When you
start the client from a GUI launcher (Dock, Spotlight, an IDE), variables you
exported in `~/.zshrc` may not be inherited by the MCP server. If `SCRIBBIT_API_BASE`
seems ignored, set it somewhere the launcher can see (a login-shell profile, a
launchd/systemd user environment, or your IDE's env settings) and restart the client.

**Codex note:** Codex starts plugin MCP servers with a minimal environment. This
plugin passes `SCRIBBIT_API_BASE` and `SCRIBBIT_API_TOKEN` through explicitly (see
`codex.mcp.json`); other variables from your shell do not reach the server. Codex
also runs the server from the plugin directory, not from your project, so a `.env`
file in your project is not read. Use the environment variables instead.

## Authentication

The agent can sign you in. Ask it to "log in to Scribbit", or let it call `auth_status`.

1. On the first call against a login-enabled API, the server starts a **device-flow**
   authorization and returns a link to the agent.
2. The agent shows you the link. Open it, sign in, and approve the request in the Scribbit
   web app.
3. The server saves the token to `~/.config/scribbit/token.json`, so you do this only once.

If the agent reports "Cannot reach the Scribbit API at http://localhost:3000", the server
uses the default URL. Set `SCRIBBIT_API_BASE` (see above) and restart the client.

You can also skip the device flow by issuing a personal access token in the web app
(**Settings -> API tokens**) and exporting it as `SCRIBBIT_API_TOKEN`.

## Tools

- `auth_status` — check the API connection and sign in (returns the sign-in link when needed)
- `list_meetings` — recent meetings
- `get_summary` — a meeting's summary
- `get_transcript` — a meeting's transcript
- `search_transcripts` — full-text search across transcripts
- `sync_meetings` — pull newly recorded Meet meetings from the connected Google account(s)
- `process_meeting` — start processing a meeting (download, transcribe, summarize)

## Layout

One plugin directory serves both clients. Each client reads only its own manifest:

| Client      | Marketplace manifest              | Plugin manifest                    | MCP server config |
|-------------|-----------------------------------|------------------------------------|-------------------|
| Claude Code | `.claude-plugin/marketplace.json` | `plugins/scribbit-mcp/.claude-plugin/plugin.json` | `.mcp.json` (uses `${CLAUDE_PLUGIN_ROOT}`) |
| Codex       | `.agents/plugins/marketplace.json` | `plugins/scribbit-mcp/.codex-plugin/plugin.json` | `codex.mcp.json` (plugin-relative `cwd`, no variables) |

Codex does not expand `${CLAUDE_PLUGIN_ROOT}`, and Claude Code does not read a `cwd`
field, so the two MCP configs stay separate. Both launch the same `dist/main.cjs`.

## Provenance

This bundle is generated from the Scribbit monorepo — it is not hand-edited here.

- Source: https://github.com/yingray/scribbit
- Built from commit: `8cd19104ac8fd3ecbe749e5f4b17fa35c56f9b09`
- Third-party licenses for bundled dependencies: [`plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt`](plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt)

To rebuild: run `pnpm --filter @scribbit/mcp build:plugin` in the monorepo and copy
the generated `apps/mcp/plugin-dist/` contents here.

## License

[Apache-2.0](LICENSE). Licenses for the bundled third-party dependencies are listed
in [`plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt`](plugins/scribbit-mcp/dist/THIRD_PARTY_LICENSES.txt).
