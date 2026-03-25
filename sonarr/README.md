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
2. Set the naming format (based on [TRaSH Guides](https://trash-guides.info/Sonarr/Sonarr-recommended-naming-scheme/)):
   - Standard Episode Format:
     ```
     {Series TitleYear} - S{season:00}E{episode:00} - {Episode CleanTitle} [{Custom Formats }{Quality Full}]{[MediaInfo VideoDynamicRangeType]}{[Mediainfo AudioCodec}{ Mediainfo AudioChannels]}{[MediaInfo VideoCodec]}{-Release Group}
     ```
     - Example: `The Series (2020) - S01E01 - Episode Title [WEBDL-1080p][HDR][AAC 2.0][x264]-GROUP`
   - Daily Episode Format:
     ```
     {Series TitleYear} - {Air-Date} - {Episode CleanTitle} [{Custom Formats }{Quality Full}]{[MediaInfo VideoDynamicRangeType]}{[Mediainfo AudioCodec}{ Mediainfo AudioChannels]}{[MediaInfo VideoCodec]}{-Release Group}
     ```
   - Anime Episode Format:
     ```
     {Series TitleYear} - S{season:00}E{episode:00} - {Episode CleanTitle} [{Custom Formats }{Quality Full}]{[MediaInfo VideoDynamicRangeType]}{[Mediainfo AudioCodec}{ Mediainfo AudioChannels]}{[MediaInfo VideoCodec]}{-Release Group}
     ```
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

- **1080p** — 1080p WEB only (WEBDL > WEBRip)
- **2160p** — 2160p WEB only (WEBDL > WEBRip)
- **Best up to 1080p** — 720p-1080p WEB (WEBDL > WEBRip)
- **Best up to 2160p** — 720p-2160p WEB (WEBDL > WEBRip)

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
