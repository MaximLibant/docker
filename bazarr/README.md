# Bazarr

Automatic subtitle downloader for Sonarr and Radarr. Monitors your library and fetches missing subtitles.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. Access the web UI

Open `http://<host-ip>:6767` and complete the initial setup.

### 3. Connect to Sonarr and Radarr

Go to Settings > Sonarr:
- Host: `http://sonarr:8989`
- API Key: your Sonarr API key (from Sonarr > Settings > General)

Go to Settings > Radarr:
- Host: `http://radarr:7878`
- API Key: your Radarr API key (from Radarr > Settings > General)

### 4. Add subtitle providers

Go to Settings > Providers and add:

| Provider | Use case | Account required |
|----------|----------|------------------|
| **OpenSubtitles.com** | Best general provider, largest database | Yes (free) |
| **Jimaku** | Anime subtitles | No |

### 5. Set languages

1. Go to Settings > Languages
2. Add your desired languages (e.g. English)
3. Create a default language profile

### 6. Apply language profile

Go to Settings > Sonarr (and Radarr) and assign your language profile as the default so Bazarr knows what to search for.

## Volume mounts

| Container path | Host path | Description |
|----------------|-----------|-------------|
| `/data` | `/mnt/media/library` | Media library root (must match Sonarr/Radarr) |

Subdirectories inside `/data`:
- `/data/tvshows` — TV show library
- `/data/movies` — Movie library

## Useful commands

```bash
# View logs
docker logs bazarr
```

## Notes

- Volume paths must match what Sonarr and Radarr use, otherwise Bazarr can't find the media files to match subtitles.
- Bazarr automatically searches for subtitles when new media is imported by Sonarr/Radarr.
