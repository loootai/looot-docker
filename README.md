# looot-docker

[![License](https://img.shields.io/github/license/loootai/looot-docker)](LICENSE) [![Docs](https://img.shields.io/badge/docs-docs.looot.ai-12A06A)](https://docs.looot.ai)

Docker images for [looot](https://looot.ai). looot gives an AI agent one key and one prepaid balance for 2,500+ data API endpoints from 90+ providers: work emails, phone numbers, company and people search, Google results, web pages, news, LinkedIn profiles, local businesses. You search the catalog, see the price before a call runs, and pay per call. A failed call costs $0.

| Image | What it is | Dockerfile |
| --- | --- | --- |
| `loootai/looot` | The looot CLI, npm package [`looot`](https://www.npmjs.com/package/looot) 1.3.0 | [cli/Dockerfile](cli/Dockerfile) |
| `loootai/looot-mcp` | The stdio MCP bridge from [loootai/looot-mcp](https://github.com/loootai/looot-mcp) 0.1.0 | [mcp/Dockerfile](mcp/Dockerfile) |

Both are built on `node:22-alpine` pinned by digest, for linux/amd64 and linux/arm64, and run as an unprivileged user (uid 1000). No token is baked in: you pass `LOOOT_TOKEN` at run time.

The image names assume the Docker Hub namespace `loootai`. Until the first release is pushed there, build locally (see [Build locally](#build-locally)).

## looot CLI

Searching the catalog is free and needs no token:

```bash
docker run --rm loootai/looot search "find a work email"
```

Everything else uses an agent token from https://looot.ai (Settings, Agent tokens). Pass it from your shell so it never lands in an image or a compose file:

```bash
export LOOOT_TOKEN=...   # never commit this

docker run --rm -e LOOOT_TOKEN loootai/looot balance
docker run --rm -e LOOOT_TOKEN loootai/looot inspect serper-search
docker run --rm -e LOOOT_TOKEN loootai/looot run serper-search --input '{"q":"looot.ai"}' --wait
```

`run` spends from your balance; `inspect` shows the price first. `docker run --rm loootai/looot help --all` lists every command.

A shell alias makes it feel like a local install:

```bash
alias looot='docker run --rm -e LOOOT_TOKEN loootai/looot'
```

`looot login` stores a token in the container's home directory, which is gone when the container exits. Use `LOOOT_TOKEN` instead.

## looot MCP bridge

Most MCP clients can connect to looot directly over HTTP at `https://api.looot.ai/mcp` and sign in in the browser. Use this image when your client only speaks stdio and you would rather not install Node.

A container has no browser, so the OAuth sign-in cannot work here. The bridge needs `LOOOT_TOKEN`.

Client config (Claude Desktop, Cursor, and other clients that take a `command`):

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

`-i` keeps stdin open, which stdio MCP needs. `-e LOOOT_TOKEN` with no value copies the variable from the client's environment into the container, so the token is not part of the command line.

The bridge passes the token as an `Authorization: Bearer` header to `https://api.looot.ai/mcp` and nowhere else.

## Tags

Each image is tagged with the version of what it contains, plus `latest` and the release tag of this repo:

- `loootai/looot`: `1.3.0`, `latest`, `v0.1.0`
- `loootai/looot-mcp`: `0.1.0`, `latest`, `v0.1.0`

## Build locally

```bash
git clone https://github.com/loootai/looot-docker && cd looot-docker
docker build -f cli/Dockerfile -t looot .
docker build -f mcp/Dockerfile -t looot-mcp .
```

To build another CLI version: `docker build -f cli/Dockerfile --build-arg UPSTREAM_VERSION=<exact version> -t looot .`

## Publishing

`.github/workflows/docker-publish.yml` builds both images for linux/amd64 and linux/arm64 and pushes them to Docker Hub when a `v*` tag is pushed. Every action is pinned by commit SHA. It needs two repository secrets:

- `DOCKERHUB_USERNAME`: the Docker Hub user or organisation that owns the images
- `DOCKERHUB_TOKEN`: a Docker Hub access token with Read & Write

Without them the job stops at its first step with a message saying which secrets to set.

To move to a new CLI release, change `ARG UPSTREAM_VERSION` in `cli/Dockerfile` (exact version, at least 7 days old), update this README, and push a new tag. For the bridge, change `LOOOT_MCP_REF` to the new commit and `UPSTREAM_VERSION` to the version in that commit's `package.json`.

Overview text for the two Docker Hub pages: [docs/DOCKERHUB-cli.md](docs/DOCKERHUB-cli.md) and [docs/DOCKERHUB-mcp.md](docs/DOCKERHUB-mcp.md).

## Checks

```bash
hadolint cli/Dockerfile mcp/Dockerfile
actionlint .github/workflows/docker-publish.yml
bash scripts/leak-scan.sh .     # also a pre-commit hook: git config core.hooksPath .githooks
```

## Related

- [looot-mcp](https://github.com/loootai/looot-mcp): the bridge source and install notes for each MCP client.
- [looot-csv-enrich](https://github.com/loootai/looot-csv-enrich): add company, email and phone columns to a CSV; it has its own image.
- [looot-js](https://github.com/loootai/looot-js) and [looot-python](https://github.com/loootai/looot-python): SDKs.
- Docs: https://docs.looot.ai

## License

MIT, copyright looot.
