# Prowlarr

Indexer manager that integrates with Sonarr, Radarr, and other *arr apps. Manages all your torrent/usenet indexers in one place and syncs them automatically.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. Access the web UI

Open `http://<host-ip>:9696` and complete the initial setup (create a username and password).

### 3. Add indexers

1. Go to Indexers > Add Indexer
2. Search for your indexer and fill in the required fields (URL, API key, etc.)
3. Test and save

### 4. Connect to Sonarr and Radarr

1. Go to Settings > Apps > Add Application
2. Add **Sonarr**:
   - Prowlarr Server: `http://prowlarr:9696`
   - Sonarr Server: `http://sonarr:8989`
   - API Key: your Sonarr API key (from Sonarr > Settings > General)
3. Add **Radarr**:
   - Prowlarr Server: `http://prowlarr:9696`
   - Radarr Server: `http://radarr:7878`
   - API Key: your Radarr API key (from Radarr > Settings > General)
4. Test and save

Once connected, Prowlarr automatically syncs indexers to Sonarr and Radarr.

### 5. Add FlareSolverr (optional)

If any indexers are behind Cloudflare protection:

1. Go to Settings > Indexers > Add Indexer Proxy
2. Select **FlareSolverr**
3. Set URL to `http://flaresolverr:8191`
4. Tag it to apply to specific indexers that need it

## Useful commands

```bash
# View logs
docker logs prowlarr
```