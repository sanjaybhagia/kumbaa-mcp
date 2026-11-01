# Installing the Kumbaa MCP server

Kumbaa is a hosted remote MCP server. There is nothing to install or run locally and no API
key. The first time a tool is used, the person signs in to their own Kumbaa account in the
browser.

- Endpoint: `https://kumbaa.app/mcp` (Streamable HTTP)
- Auth: OAuth 2.1 with PKCE, through Clerk at `https://clerk.kumbaa.app`. Clients identify
  themselves with a client ID metadata document (CIMD). Dynamic client registration is off.
  Discovery starts from the 401 the endpoint returns
  (`/.well-known/oauth-protected-resource/mcp`).
- The account must already belong to a family archive: sign in once on the family's website
  (`<family>.kumbaa.app`) with the invited email address, or start one at
  https://kumbaa.app.

For clients that support remote servers directly:

```json
{
  "mcpServers": {
    "kumbaa": { "url": "https://kumbaa.app/mcp" }
  }
}
```

For clients that only run local (stdio) servers, bridge with `mcp-remote`. Because dynamic
client registration is off, `mcp-remote` must be given a client ID metadata document that
you host yourself, with its loopback redirect URI in `redirect_uris`:

```json
{
  "mcpServers": {
    "kumbaa": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://kumbaa.app/mcp", "--client-metadata-url", "https://<your-host>/kumbaa-client.json"]
    }
  }
}
```

To check it works, ask: "Which family archives am I in?" The client should open a Kumbaa
sign-in page, then answer from the `list_families` tool.
