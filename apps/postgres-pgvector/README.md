# postgres-pgvector

CloudNativePG-compatible PostgreSQL image with [`pgvector`](https://github.com/pgvector/pgvector) preinstalled.

## What it is

The upstream `ghcr.io/cloudnative-pg/postgresql:<version>-bookworm` image with the Debian-packaged `postgresql-<major>-pgvector` extension layered on top. CNPG expects a specific UID (26) and on-disk layout; this image preserves both — it adds the extension and nothing else.

Tracks the latest GA CNPG postgres major. Currently postgres 17 (CNPG has not yet published 18 images — the upstream tag set runs `15.x`, `16.x`, `17`).

## Tag scheme

Tags are `<postgres_major>-<image_revision>`. CNPG only publishes floating major-version tags on bookworm (no per-patch), so we pin the rebuild revision ourselves:

- `17-1` — first build on the `17-bookworm` base
- `17-2` — same base, bumped pgvector or Dockerfile change
- `18-1` — postgres 18 base bump (when CNPG publishes one)

No floating tags (no `latest`, no `17`). Pull by tag **and** digest:

```
ghcr.io/nicolerenee/postgres-pgvector:17-1@sha256:<digest>
```

The digest is recorded in the GitHub Release notes for each tag.

## Consuming from CloudNativePG

```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
spec:
  imageName: ghcr.io/nicolerenee/postgres-pgvector:17-1@sha256:<digest>
```

## Build inputs (Renovate-managed)

- `VERSION` — CNPG postgres base image tag (without `-bookworm` suffix)
- `PGVECTOR_VERSION` — full Debian (PGDG) package version of `postgresql-<major>-pgvector`

Both are pinned and bumped by Renovate; the image revision suffix increments on any change.

## Platforms

`linux/amd64` only for now. arm64 will be added if/when a consumer needs it.
