# yahoo-finance-mcp

Yahoo Finance [MCP](https://modelcontextprotocol.io/) server — structured stock quotes, news,
financials, and options over streamable-HTTP, for the Janet platform.

## What it is

[`Alex2Yang97/yahoo-finance-mcp`](https://github.com/Alex2Yang97/yahoo-finance-mcp) (`server.py`,
commit `241e523`, MIT — vendored **unmodified**), `yfinance`-backed. The upstream server only speaks
stdio; `http_entrypoint.py` drives the same `FastMCP` instance over streamable-HTTP on
`0.0.0.0:8000` (path `/mcp`) so Janet's MCP client can reach it as a ClusterIP backend — no
stdio↔HTTP shim. Keeping `server.py` byte-for-byte upstream means re-vendoring stays a clean
drop-in; all HTTP wiring lives in `http_entrypoint.py`.

9 tools: `get_stock_info`, `get_yahoo_finance_news`, `get_historical_stock_prices`,
`get_stock_actions`, `get_financial_statement`, `get_holder_info`,
`get_option_expiration_dates`, `get_option_chain`, `get_recommendations`.

Public market data only — no auth, no secrets.

## Tag scheme

Tags are `<app_version>-<image_revision>`. The vendored server carries no upstream release version,
so `0.1.0` is this image's own app version for the vendored snapshot and the `-N` suffix is the
rebuild revision:

- `0.1.0-1` — first build of the vendored snapshot
- `0.1.0-2` — same snapshot, bumped `yfinance` floor or Dockerfile change
- `0.2.0-1` — re-vendored `server.py` from a newer upstream commit

No floating tags. Pull by tag **and** digest (digest is in the `yahoo-finance-mcp-<version>` release
notes):

```
ghcr.io/nicolerenee/yahoo-finance-mcp:0.1.0-1@sha256:<digest>
```

`yfinance` is the volatile input — it breaks when Yahoo shifts APIs, so expect periodic revision
bumps (bump `-N`, or the app version if `server.py` is re-vendored).

## Consuming (janet-mcp)

Deployed in the `janet-mcp` namespace and registered in Janet's MCP config from
[`nicolerenee/infra`](https://github.com/nicolerenee/infra)
(`kubernetes/apps/janet/yahoo-finance-mcp/`). Egress is locked to Yahoo's
`query*.finance.yahoo.com` (and the consent/redirect hosts `yfinance` follows) by a Cilium policy
there.

Config:

- `YFINANCE_MCP_HOST` — bind host (default `0.0.0.0`)
- `YFINANCE_MCP_PORT` — bind port (default `8000`)

## Platforms

`linux/amd64` only. The `fairy-k8s01` cluster's arm64 nodes are the DGX Sparks, which carry an
NVIDIA taint; this Deployment has no matching toleration, so it only ever schedules on the amd64
nodes. No need to build arm64.
