# FlareSolverr

Proxy server that bypasses Cloudflare and DDoS-GUARD protection for web scrapers. Used by Prowlarr/Jackett to access indexers behind Cloudflare challenges.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. Configure in Prowlarr

1. Go to Prowlarr UI > Settings > Indexers
2. Add a new indexer proxy, select **FlareSolverr**
3. Set the URL to `http://flaresolverr:8191` (since both are on the `media` network)
4. Tag it to apply to specific indexers that need it, or leave untagged to apply to all

### 3. Verify

Open `http://<host-ip>:8191` in a browser. You should see a JSON response confirming FlareSolverr is running.

## Configuration

Environment variables in `docker-compose.yml`:

| Variable | Default | Description |
|----------|---------|-------------|
| `TZ` | `America/Toronto` | Timezone |
| `LOG_LEVEL` | `info` | Log verbosity (`debug`, `info`, `warn`, `error`) |

## Useful commands

```bash
# View logs
docker logs flaresolverr

# Test that it's running
curl http://localhost:8191
```

## Notes

- Only add it as a proxy to indexers that actually need it — using it on all indexers adds unnecessary latency.