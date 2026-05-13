# Authenticating Atlassian MCP inside a container

Typical MCP config for Atlassian (HTTP endpoint):

```json
{
  "mcpServers": {
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    }
  }
}
```

Cursor still has to finish **OAuth** afterward. On a normal setup you usually run **`/mcp login atlassian`** in the **Cursor CLI** (or by navigating inside the **`/mcp list`** menu).

## If no browser is available inside the container the login fails

- The tool may open a browser **inside** the container and waits indefinitelly.
- Or the **authorize URL** appears too briefly to copy and open on your **host** browser.

If the callback never lands where the login tool expects, the flow hangs.

## What to do instead

Use the shell entrypoint, not the Cursor slash command:

```bash
cursor-agent mcp login atlassian
```

Run that **inside the container**. It prints a stable URL and listens on **`http://localhost:8787/callback`**.

You still log in once in a **browser on the host**. The browser will redirect to **localhost:8787** on the **host**, so that port must reach the listener inside the container: **`docker compose --service-ports`** or **`docker run -p 8787:8787`**.

1. Map **8787** from the container to the host.
2. Run **`cursor-agent mcp login atlassian`** in a shell in the container.
3. Open the printed URL on the host, complete Atlassian’s page, and let the callback finish.

### Example transcript

```text
MCP 'atlassian' requires authentication...
If it doesn't open, navigate to:
https://mcp.atlassian.com/v1/authorize?...<copy from your terminal>...

Listening on http://localhost:8787/callback for the OAuth callback...

✓ MCP login successful
Received authorization code '<redacted>'...
```