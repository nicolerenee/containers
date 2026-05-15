# renovate: datasource=docker depName=ghcr.io/cloudnative-pg/postgresql versioning=loose
ARG VERSION=18.1

FROM ghcr.io/cloudnative-pg/postgresql:${VERSION}-bookworm

USER root

# renovate: datasource=repology depName=debian_12/postgresql-pgvector versioning=loose
ARG PGVECTOR_VERSION="0.8.0-1.pgdg120+1"

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      "postgresql-18-pgvector=${PGVECTOR_VERSION}" \
 && rm -rf /var/lib/apt/lists/*

USER 26
