# Installation guide for AI agents (Cline et al.)

This is a **remote, hosted** MCP server. There is nothing to install, build, clone, or run — you only
add a server entry pointing at the hosted URL. Do NOT try to npm-install or run a local process.

## Steps

1. Get a free API key (the user can do this; first calls are free):
   ```
   curl -s -X POST https://api.babyblueviper.com/register -d '{"label":"cline"}'
   ```
   The response contains `api_key`. Ask the user for their key, or have them register.

2. Add this server to the host's MCP settings as a **Streamable HTTP (remote)** server:
   - **URL:** `https://api.babyblueviper.com/mcp`
   - **Transport / type:** `streamableHttp`
   - **Header:** `Authorization: Bearer <api_key>`

   For Cline, the entry in `cline_mcp_settings.json` is:
   ```json
   {
     "mcpServers": {
       "invinoveritas": {
         "type": "streamableHttp",
         "url": "https://api.babyblueviper.com/mcp",
         "headers": { "Authorization": "Bearer <api_key>" }
       }
     }
   }
   ```

3. Confirm it connected: the server should expose tools including `review` and `verify_proof`.
   `verify_proof` requires no auth, so you can call it to confirm connectivity even before funding the key.

## Notes

- `verify_proof` is free / no-auth. `review` is paid (sats / USDC / card); the first calls are free, and
  it returns a `402` with funding instructions when a balance is needed — that is expected, not an error.
- No environment variables, no local command, no Docker. If a setup flow asks for a command or args,
  this server does not use them — it is URL + Authorization header only.
