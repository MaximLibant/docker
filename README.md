# Media Server Stack

Docker Compose stacks for a media server running on a remote host (Barad-dur), deployed via Portainer.

All services share an external `media` Docker network and use a single `/mnt/media/library:/data` volume mount to enable hardlinks and atomic moves between apps.

## Services

| Service | Port | Description |
|---------|------|-------------|
| [Sonarr](sonarr/) | 8989 | TV show management and automation |
| [Radarr](radarr/) | 7878 | Movie management and automation |
| [Prowlarr](prowlarr/) | 9696 | Indexer manager for Sonarr and Radarr |
| [Transmission](transmission/) | 9091 | Torrent client routed through Mullvad VPN via Gluetun |
| [Bazarr](bazarr/) | 6767 | Automatic subtitle downloader |
| [Recyclarr](recyclarr/) | — | Syncs TRaSH Guides quality profiles to Sonarr/Radarr |
| [FlareSolverr](flaresolverr/) | 8191 | Bypass Cloudflare protection for Prowlarr indexers |
| [Jellyseerr](jellyseerr/) | 5055 | Media request management |
| [Tdarr](tdarr/) | 8265 | Media transcoding (not currently in use) |
| [Watchtower](watchtower/) | — | Automatic Docker image updates |

## Architecture

```
Prowlarr (indexers) --> Sonarr/Radarr (management)
                              |
                              v
                    Gluetun (Mullvad VPN)
                              |
                              v
                      Transmission (downloads)
                              |
                              v
                        /mnt/media/library
                              |
                              v
                    Bazarr (subtitles), Tdarr (transcoding)

Recyclarr --> syncs quality profiles to Sonarr/Radarr
Jellyseerr --> request portal for Sonarr/Radarr
FlareSolverr --> Cloudflare solving for Prowlarr
```

## Deployment

Each service has its own `docker-compose.yml`. Deploy each stack individually through the Portainer web UI on the remote host.

### Prerequisites

1. Create the shared network: `docker network create media`
2. Ensure `/mnt/media/library` exists on the host with the following subdirectories:
   - `movies/` — Movie library
   - `tvshows/` — TV show library
   - `downloads/` — Active downloads
   - `incomplete/` — Incomplete downloads
   - `completed/` — Completed downloads

### Deploy order

1. **Gluetun/Transmission** — VPN + download client (requires Mullvad credentials)
2. **Prowlarr** — Indexer manager
3. **FlareSolverr** — Cloudflare solver (if needed by indexers)
4. **Sonarr** and **Radarr** — Media managers
5. **Recyclarr** — Quality profile sync
6. **Bazarr** — Subtitles
7. **Jellyseerr** — Request management

## Volume strategy

All services use **Docker named volumes** for config and a **bind mount** for the shared media library:

- Config: named volumes (e.g., `sonarr-config`, `radarr-config`)
- Media: `/mnt/media/library:/data`

The single `/data` mount across all services enables hardlinks and atomic moves, avoiding unnecessary disk usage when Sonarr/Radarr import completed downloads.
