# Yahoo Finance MCP — vendored Alex2Yang97/yahoo-finance-mcp (server.py,
# unmodified upstream, commit 241e523, MIT) served over streamable-HTTP by
# http_entrypoint.py. python:3.11-slim + uv, mirroring the upstream project's
# own Dockerfile shape (uv-image install stage -> slim runtime,
# `uv pip install --system`), non-root since we own this build.
#
# Built by this repo's Release pipeline (see .github/workflows). Build context
# is this app dir (apps/yahoo-finance-mcp/), so COPY paths are bare filenames.

FROM ghcr.io/astral-sh/uv:python3.11-bookworm-slim AS uv

WORKDIR /app

ENV UV_COMPILE_BYTECODE=1 \
    UV_LINK_MODE=copy

COPY pyproject.toml .
RUN --mount=type=cache,target=/root/.cache/uv \
    uv pip install --system -r pyproject.toml

# --- runtime -----------------------------------------------------------------
FROM python:3.11-slim

RUN groupadd --system --gid 65532 mcp && \
    useradd --system --uid 65532 --gid mcp --no-create-home --shell /usr/sbin/nologin mcp

WORKDIR /app

# Bring over the interpreter's site-packages + installed console scripts from
# the uv stage (same layout upstream's own Dockerfile copies).
COPY --from=uv /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY --from=uv /usr/local/bin /usr/local/bin
COPY server.py http_entrypoint.py ./

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app \
    YFINANCE_MCP_HOST=0.0.0.0 \
    YFINANCE_MCP_PORT=8000

USER mcp
EXPOSE 8000

ENTRYPOINT ["python", "http_entrypoint.py"]
