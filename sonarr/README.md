# Sonarr

TV show management and automation. Monitors for new episodes, sends requests to download clients, and organizes your library.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. Access the web UI

Open `http://<host-ip>:8989` and complete the initial setup (create a username and password).

### 3. Configure media management

Go to Settings > Media Management:

1. **Enable Rename Episodes** and **Replace Illegal Characters**
2. Set the naming format:
   - Standard Episode Format: `{Series TitleYear} - S{season:00}E{episode:00} - {Episode CleanTitle} [{Quality Full}]`
     - Example: `The Series (2020) - S01E01 - Episode Title [WEBDL-1080p]`
   - Season Folder Format: `Season {season:00}`
   - Series Folder Format: `{Series TitleYear}`
   - Specials Folder Format: `Specials`
3. Under **Root Folders**, add `/data/tvshows`

### 4. Connect download client

1. Go to Settings > Download Clients > Add
2. Select **Transmission**
3. Host: `gluetun`, Port: `9091`
4. Test and save

### 5. Get your API key

Go to Settings > General — your API key is listed there. You'll need this for Prowlarr and Recyclarr.

### 6. Quality profiles

Quality profiles are managed by Recyclarr. See the [Recyclarr README](../recyclarr/README.md) for details. Available profiles:

- **720p** — 720p only (Bluray > WEBDL > WEBRip)
- **1080p** — 1080p only (Bluray > WEBDL > WEBRip)
- **2160p** — 2160p only (Bluray > WEBDL > WEBRip)
- **Best up to 1080p** — 720p-1080p, upgrades toward Bluray-1080p
- **Best up to 2160p** — 720p-2160p, upgrades toward Bluray-2160p

## Volume mounts

| Container path | Host path | Description |
|----------------|-----------|-------------|
| `/data` | `/mnt/media/library` | Media library root |

Subdirectories inside `/data`:
- `/data/tvshows` — TV show library
- `/data/downloads` — Active downloads
- `/data/completed` — Completed downloads

## Useful commands

```bash
# View logs
docker logs sonarr
```

## Notes

- Indexers are managed by Prowlarr and synced automatically — no need to add them in Sonarr directly.
- The download client host is `gluetun` (not `transmission`) because Transmission uses Gluetun's network stack.
