# Radarr

Movie management and automation. Monitors for new releases, sends requests to download clients, and organizes your library.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. Access the web UI

Open `http://<host-ip>:7878` and complete the initial setup (create a username and password).

### 3. Configure media management

Go to Settings > Media Management:

1. **Enable Rename Movies** and **Replace Illegal Characters**
2. Set the naming format (based on [TRaSH Guides](https://trash-guides.info/Radarr/Radarr-recommended-naming-scheme/)):
   - Standard Movie Format:
     ```
     {Movie CleanTitle} {(Release Year)} {imdb-{ImdbId}} {edition-{Edition Tags}} {[Custom Formats]}{[Quality Full]}{[MediaInfo 3D]}{[MediaInfo VideoDynamicRangeType]}{[Mediainfo AudioCodec}{ Mediainfo AudioChannels]}{[Mediainfo VideoCodec]}{-Release Group}
     ```
     - Example: `The Movie Title (2010) {imdb-tt1234567} [Bluray-1080p][HDR][DTS-HD MA 5.1][x264]-GROUP`
   - Movie Folder Format: `{Movie CleanTitle} ({Release Year}) [imdbid-{ImdbId}]`
     - Example: `The Movie Title (2010) [imdbid-tt1234567]`
   - Colon Replacement: **Smart Replace**
3. Under **Root Folders**, add `/data/movies`

### 4. Connect download client

1. Go to Settings > Download Clients > Add
2. Select **Transmission**
3. Host: `gluetun`, Port: `9091`
4. Test and save

### 5. Get your API key

Go to Settings > General — your API key is listed there. You'll need this for Prowlarr and Recyclarr.

### 6. Quality profiles

Quality profiles are managed by Recyclarr. See the [Recyclarr README](../recyclarr/README.md) for details. Available profiles:

- **1080p** — 1080p only (Bluray > WEBDL > WEBRip)
- **2160p** — 2160p only (Bluray > WEBDL > WEBRip)
- **Best up to 1080p** — 720p-1080p, upgrades toward Bluray-1080p
- **Best up to 2160p** — 720p-2160p, upgrades toward Bluray-2160p

## Volume mounts

| Container path | Host path | Description |
|----------------|-----------|-------------|
| `/data` | `/mnt/media/library` | Media library root |

Subdirectories inside `/data`:
- `/data/movies` — Movie library
- `/data/downloads` — Active downloads
- `/data/completed` — Completed downloads

## Useful commands

```bash
# View logs
docker logs radarr
```

## Notes

- Indexers are managed by Prowlarr and synced automatically — no need to add them in Radarr directly.
- The download client host is `gluetun` (not `transmission`) because Transmission uses Gluetun's network stack.
