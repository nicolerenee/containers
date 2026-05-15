# renovate: datasource=docker depName=ghcr.io/cloudnative-pg/postgresql versioning=loose
ARG VERSION=17

FROM ghcr.io/cloudnative-pg/postgresql:${VERSION}-bookworm

USER root

# renovate: datasource=repology depName=debian_12/postgresql-pgvector versioning=loose
ARG PGVECTOR_VERSION="0.8.2-1.pgdg12+1"

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      "postgresql-17-pgvector=${PGVECTOR_VERSION}" \
 && rm -rf /var/lib/apt/lists/*

USER 26
