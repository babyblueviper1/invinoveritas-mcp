# invinoveritas — MCP server

An **independent review gate for AI agents**, as a hosted MCP server. Before your agent takes an
irreversible action — shipping code, running a shell command, signing a transaction — it calls
`review` for a neutral second opinion from a party that **isn't the agent**, and gets back a
**recomputable, Bitcoin-anchored proof** anyone can re-verify without trusting the agent or us.

The one thing an agent can't self-serve is a trustworthy verdict on its own output. This is that
verdict — model-agnostic, advisory (it never blocks), and checkable from the bytes.

- **Hosted, no install.** Remote Streamable-HTTP MCP server: `https://api.babyblueviper.com/mcp`
- **Free key**, first calls free: `curl -X POST https://api.babyblueviper.com/register -d '{"label":"mcp"}'`
- **`verify_proof` is always free / no auth.**

## Tools

| Tool | What it does | Auth |
|---|---|---|
| `review` | Independent verdict (`approve` / `approve_with_concerns` / `reject`) on a code diff, shell command, plan, config, trade, or on-chain transaction, with ranked issues + a portable signed proof. | Bearer (paid) |
| `verify_proof` | Recompute and verify a signed invinoveritas proof someone handed you — confirms it without trusting the presenter or us. | free |
| `witness` | Anchor a third party's exact claim bytes, unmodified and unjudged — pure notarization (receipt + timestamp + integrity), distinct from `review` (our verdict). Source is self-declared and marked as such in the signed proof. | Bearer (paid) |

(The server also exposes market/agent-data tools; `review` + `verify_proof` are the headline.)

## Add it to your agent

**Cursor** — one-click: [**Add invinoveritas to Cursor**](cursor://anysphere.cursor-deeplink/mcp/install?name=invinoveritas&config=eyJ1cmwiOiJodHRwczovL2FwaS5iYWJ5Ymx1ZXZpcGVyLmNvbS9tY3AifQ==) (then add your `Authorization: Bearer` header), or edit `~/.cursor/mcp.json` (global) / `.cursor/mcp.json` (project):
```json
{
  "mcpServers": {
    "invinoveritas": {
      "url": "https://api.babyblueviper.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

**Cline** — Add via the MCP marketplace, or in `cline_mcp_settings.json`:
```json
{
  "mcpServers": {
    "invinoveritas": {
      "type": "streamableHttp",
      "url": "https://api.babyblueviper.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

**Claude Code**:
```bash
claude mcp add --transport http invinoveritas https://api.babyblueviper.com/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

**Devin** — Settings → Connections → MCP servers → Add a custom MCP → HTTP (Streamable), URL
`https://api.babyblueviper.com/mcp`, Auth Header `Authorization: Bearer YOUR_API_KEY`.

**OpenHands** — `config.toml`:
```toml
[mcp]
shttp_servers = [{ url = "https://api.babyblueviper.com/mcp", api_key = "YOUR_API_KEY", timeout = 60 }]
```

**Any MCP client** — point it at `https://api.babyblueviper.com/mcp` (Streamable HTTP) with header
`Authorization: Bearer YOUR_API_KEY`. Protocol `2025-06-18`; the handshake, `tools/list`, and
`tools/call` work out of the box.

## Use it

Tell your agent: *before opening a PR or running an irreversible command, call `review`* with the
diff/command as `artifact`, set `artifact_type` (`code_diff` / `shell_command` / `onchain_action` /
`general`), and `sign: true`. On `reject`, hold and fix; on `approve`, attach the proof so reviewers
can re-verify it.

## Why a verdict you can recompute

`review` with `sign: true` returns a proof that is a BIP-340-signed Nostr event, published to a
public, Bitcoin-anchored ledger. Verify it offline against our published key — recompute the event
id and check the schnorr signature — or POST it to `https://api.babyblueviper.com/verify-proof`
(free, no auth). Trust the math, not us. Wins **and** losses are published; the track record is a
forensic artifact, not a score.

- Recompute a proof yourself: zero-dep `pip install invinoveritas-verify` / `npm i invinoveritas-verify`
- Server card: `https://api.babyblueviper.com/.well-known/mcp/server-card.json`

## License

MIT
