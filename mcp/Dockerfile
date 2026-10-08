# The looot MCP stdio bridge (https://github.com/loootai/looot-mcp) as a container.
# It forwards MCP messages on stdin/stdout to https://api.looot.ai/mcp.
#
# Base: node:22.23.3-alpine3.24 (multi-arch index digest, read from Docker Hub on 2026-10-08).
FROM node:22-alpine@sha256:0a7108bf6c7bf5de370ffb1a3ed6be93d405b43ff159f681a8d18c0e2bc2e402 AS build

# Commit of loootai/looot-mcp to build. The package is not on npm, so the source comes from
# GitHub at this exact commit and installs from its lockfile (mcp-remote 0.14.3).
ARG LOOOT_MCP_REF=84a26d3efea12cf371d9c8c506fe2b65b1824d94

WORKDIR /app
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]
RUN wget -q -O - "https://codeload.github.com/loootai/looot-mcp/tar.gz/${LOOOT_MCP_REF}" \
      | tar -xz --strip-components=1 \
    && npm ci --omit=dev --ignore-scripts --no-audit --no-fund \
    && rm -rf assets test scripts .github .githooks .claude

FROM node:22-alpine@sha256:0a7108bf6c7bf5de370ffb1a3ed6be93d405b43ff159f681a8d18c0e2bc2e402

# Version in looot-mcp's package.json at LOOOT_MCP_REF. The publish workflow uses it as an image tag.
ARG UPSTREAM_VERSION=0.1.0

LABEL org.opencontainers.image.title="looot-mcp" \
      org.opencontainers.image.description="stdio bridge to the looot MCP server: discover, inspect and run data provider operations from any MCP client." \
      org.opencontainers.image.source="https://github.com/loootai/looot-docker" \
      org.opencontainers.image.url="https://looot.ai" \
      org.opencontainers.image.documentation="https://docs.looot.ai" \
      org.opencontainers.image.vendor="looot" \
      org.opencontainers.image.licenses="MIT"

ENV NODE_ENV=production

COPY --from=build --chown=1000:1000 /app /app

USER 1000:1000
WORKDIR /home/node

# stdout carries MCP messages. Run with: docker run -i --rm -e LOOOT_TOKEN <image>
ENTRYPOINT ["node", "/app/bin/looot-mcp.js"]
