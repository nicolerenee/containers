# postgres-pgvector

CloudNativePG-compatible PostgreSQL 18 image with [`pgvector`](https://github.com/pgvector/pgvector) preinstalled.

## What it is

The upstream `ghcr.io/cloudnative-pg/postgresql:<version>-bookworm` image with the Debian-packaged `postgresql-18-pgvector` extension layered on top. CNPG expects a specific UID (26) and on-disk layout; this image preserves both — it adds the extension and nothing else.

## Tag scheme

Tags are `<postgres_version>-<image_revision>`:

- `18.1-1` — first build for postgres 18.1
- `18.1-2` — same postgres base, pgvector or Dockerfile change
- `18.2-1` — postgres 18.2 base bump

No floating tags (no `latest`, no `18`). Pull by tag **and** digest:

```
ghcr.io/nicolerenee/postgres-pgvector:18.1-1@sha256:<digest>
```

The digest is recorded in the GitHub Release notes for each tag.

## Consuming from CloudNativePG

```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
spec:
  imageName: ghcr.io/nicolerenee/postgres-pgvector:18.1-1@sha256:<digest>
```

## Build inputs (Renovate-managed)

- `VERSION` — CNPG postgres base image tag (without `-bookworm` suffix)
- `PGVECTOR_VERSION` — full Debian package version of `postgresql-18-pgvector`

Both are pinned and bumped by Renovate; the image revision suffix increments on any change.

## Platforms

`linux/amd64` only for now. arm64 will be added if/when a consumer needs it.
