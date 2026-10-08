# The looot CLI (https://www.npmjs.com/package/looot) as a container.
#
# Base: node:22.23.3-alpine3.24 (multi-arch index digest, read from Docker Hub on 2026-10-08).
FROM node:22-alpine@sha256:0a7108bf6c7bf5de370ffb1a3ed6be93d405b43ff159f681a8d18c0e2bc2e402

# Exact npm version of the looot CLI. The publish workflow also uses it as an image tag.
ARG UPSTREAM_VERSION=1.3.0

LABEL org.opencontainers.image.title="looot" \
      org.opencontainers.image.description="The looot CLI: search 2,500+ data API endpoints, see the price, run them with one token." \
      org.opencontainers.image.source="https://github.com/loootai/looot-docker" \
      org.opencontainers.image.url="https://looot.ai" \
      org.opencontainers.image.documentation="https://docs.looot.ai" \
      org.opencontainers.image.vendor="looot" \
      org.opencontainers.image.licenses="MIT"

ENV NODE_ENV=production \
    NPM_CONFIG_UPDATE_NOTIFIER=false

# The package has no dependencies and no install scripts.
RUN npm install --global --ignore-scripts --no-audit --no-fund "looot@${UPSTREAM_VERSION}" \
    && npm cache clean --force \
    && looot --version

USER 1000:1000
WORKDIR /home/node

ENTRYPOINT ["looot"]
CMD ["help"]
