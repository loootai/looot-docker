# Docker Hub overview for `looot-mcp`

Paste the text under the line into the repository overview on hub.docker.com. Short description
(100 characters max): `stdio MCP bridge to looot: 4,200+ data API endpoints for any MCP client, one token.`

Set the repository's "Source Repository" field to `https://github.com/loootai/looot-docker`.

---

# looot MCP bridge

[looot](https://looot.ai) gives an AI agent one key and one prepaid balance for 4,200+ data API endpoints from 300+ providers. The agent searches the catalog, sees the price before it runs, and pays per call. A failed call costs $0.

This image is a stdio MCP server that forwards every message to the hosted looot MCP server at `https://api.looot.ai/mcp`. Use it when your MCP client only speaks stdio and you would rather not install Node. Clients that support remote MCP servers can connect to that URL directly.

## Use it

A container has no browser, so it needs an agent token from https://looot.ai (Settings, Agent tokens):

```json
{
  "mcpServers": {
    "looot": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "-e", "LOOOT_TOKEN", "loootai/looot-mcp"],
      "env": { "LOOOT_TOKEN": "your-agent-token" }
    }
  }
}
```

The token is sent as an `Authorization: Bearer` header to `https://api.looot.ai/mcp` and nowhere else. Nothing is stored in the image.

## What is inside

`node:22-alpine`, the bridge from https://github.com/loootai/looot-mcp at a pinned commit, and `mcp-remote` 0.14.3 from its lockfile. linux/amd64 and linux/arm64, unprivileged user.

## Tags

- `0.1.0`, `latest`: looot-mcp 0.1.0

## Links

- Dockerfiles: https://github.com/loootai/looot-docker
- Bridge source: https://github.com/loootai/looot-mcp
- looot: https://looot.ai
- Docs: https://docs.looot.ai
- License: MIT
