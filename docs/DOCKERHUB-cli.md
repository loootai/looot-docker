# Docker Hub overview for `looot`

Paste the text under the line into the repository overview on hub.docker.com. Short description
(100 characters max): `The looot CLI: search 2,500+ data API endpoints, see the price, run them with one token.`

Set the repository's "Source Repository" field to `https://github.com/loootai/looot-docker`.

---

# looot CLI

[looot](https://looot.ai) gives an AI agent one key and one prepaid balance for 2,500+ data API endpoints from 90+ providers: work emails, phone numbers, company and people search, Google results, web pages, news, LinkedIn profiles, local businesses. You see the price before a call runs and pay per call. A failed call costs $0. No subscription.

This image is the `looot` npm package on `node:22-alpine`, for linux/amd64 and linux/arm64. It runs as an unprivileged user and holds no token.

## Search the catalog (free, no token)

```bash
docker run --rm loootai/looot search "find a work email"
```

## With your token

Create an agent token at https://looot.ai (Settings, Agent tokens) and pass it from your shell:

```bash
export LOOOT_TOKEN=...
docker run --rm -e LOOOT_TOKEN loootai/looot balance
docker run --rm -e LOOOT_TOKEN loootai/looot inspect serper-search
docker run --rm -e LOOOT_TOKEN loootai/looot run serper-search --input '{"q":"looot.ai"}' --wait
```

`run` spends from your balance. `docker run --rm loootai/looot help --all` lists every command.

## Tags

- `1.3.0`, `latest`: looot CLI 1.3.0

## Links

- Dockerfile and source: https://github.com/loootai/looot-docker
- looot: https://looot.ai
- Docs: https://docs.looot.ai
- License: MIT
