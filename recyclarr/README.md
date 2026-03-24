# Recyclarr

Syncs TRaSH Guides recommended custom formats and quality profiles to Sonarr and Radarr.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer). This creates a named volume `recyclarr_recyclarr-config`.

### 2. Generate the config

```bash
docker exec recyclarr recyclarr config create
```

This creates a starter `recyclarr.yml` inside the volume.

### 3. Copy the config into the volume

The config file in this repo (`recyclarr-config/recyclarr.yml`) needs to be copied into the Docker volume on the remote host. From the remote host:

```bash
docker run --rm -i -v recyclarr_recyclarr-config:/config alpine sh -c "cat > /config/recyclarr.yml" < recyclarr.yml
```

Or edit the file directly inside the volume:

```bash
docker run --rm -it -v recyclarr_recyclarr-config:/config alpine vi /config/recyclarr.yml
```

### 4. Add API keys

Edit the config and replace the placeholder API keys:

- **Sonarr**: Found in Sonarr UI > Settings > General > API Key
- **Radarr**: Found in Radarr UI > Settings > General > API Key

### 5. Create quality profiles

The config defines these quality profiles (created automatically on first sync):

| Profile | Description |
|---------|-------------|
| 720p | 720p only (Bluray > WEBDL > WEBRip) |
| 1080p | 1080p only (Bluray > WEBDL > WEBRip) |
| 2160p | 2160p only (Bluray > WEBDL > WEBRip) |
| Best up to 1080p | Accepts 720p-1080p, upgrades toward Bluray-1080p |
| Best up to 2160p | Accepts 720p-2160p, upgrades toward Bluray-2160p |

### 6. Sync

```bash
# Preview changes (dry run)
docker exec recyclarr recyclarr sync --preview

# Apply changes
docker exec recyclarr recyclarr sync
```

Recyclarr runs automatically on a daily schedule after the container is started.

## Useful commands

```bash
# View logs
docker logs recyclarr

# List available config templates
docker exec recyclarr recyclarr config list templates

# View contents of the config volume
docker run --rm -v recyclarr_recyclarr-config:/config alpine cat /config/recyclarr.yml
```