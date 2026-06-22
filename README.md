# Weaviate UI

A single-file, read-only web UI for browsing a Weaviate instance over its
REST/GraphQL HTTP API. **No gRPC required** — which is exactly why it works
against a domain that only proxies the HTTP port (8080), where Verba and the
v4 clients fail their gRPC health check.

It only ever issues `GET` requests and GraphQL `Get` queries. It never writes,
updates, or deletes. Pairs naturally with a read-only API key.

## Features
- Collections sidebar with prop counts + filter
- Object browser as a table, click any row for a full detail drawer
- Per-collection schema view (highlighted JSON)
- GraphQL console (Cmd/Ctrl+Enter to run)
- Cluster/meta view
- Row-level filtering, adjustable limit, copy-to-clipboard
- The endpoint URL is remembered locally; the API key is never persisted

## Run locally (quick test)
```bash
docker compose up --build
# open http://localhost:8088
```
Then enter:
- URL: `https://weaviate-url`
- Key: your read-only key

## Deploy on the server
```bash
# copy this folder to the server, then:
docker compose up -d --build
```
It listens on `:8088` (host) → `:8080` (container). Point Traefik at it by
uncommenting the labels in `docker-compose.yml` and setting your host +
certresolver. Strongly consider the basic-auth middleware so the explorer
isn't publicly reachable.

## CORS
Because the browser calls Weaviate directly from this page's origin, Weaviate
must allow it. Weaviate enables permissive CORS by default, so this usually
just works. If you hit a CORS error in the browser console, set on the
Weaviate container:
```
ENABLE_CORS=true
```
(or configure your reverse proxy to add `Access-Control-Allow-*` headers for
the explorer's origin).

## Files
- `index.html` — the entire app (no build step, no dependencies)
- `nginx.conf` — static serve + security headers + `/healthz`
- `Dockerfile` — nginx:alpine, multi-arch (arm64/amd64)
- `docker-compose.yml` — build + run, Traefik labels ready to uncomment