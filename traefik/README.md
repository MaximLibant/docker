# Traefik

Reverse proxy for all media server services. Provides HTTPS access via `*.myhome.local` hostnames with a self-signed wildcard certificate.

## Setup

### 1. Generate self-signed wildcard certificate

From this directory on the host:

```bash
mkdir -p certs
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout certs/local.key -out certs/local.crt \
  -subj "/CN=*.myhome.local" \
  -addext "subjectAltName=DNS:*.myhome.local,DNS:myhome.local"
```

### 2. Generate dashboard credentials

```bash
htpasswd -nB admin
```

Copy the output and set it in your `.env` file (or Portainer environment variables). Double all `$` signs for Docker Compose escaping:

```
TRAEFIK_DASHBOARD_AUTH=admin:$$2y$$05$$...
```

### 3. Configure DNS

In [NextDNS dashboard](https://my.nextdns.io) → **Rewrites**, add:

| Domain | Answer |
|--------|--------|
| `*.myhome.local` | Your server's LAN IP (e.g., `192.168.1.x`) |

If NextDNS does not support wildcard rewrites, add individual entries for each service hostname listed below.

### 4. Deploy

Deploy the Traefik stack via Portainer, then redeploy each service stack to pick up the new Traefik labels.

## Service Hostnames

| Service | URL |
|---------|-----|
| Traefik Dashboard | `https://traefik.myhome.local` |
| Sonarr | `https://sonarr.myhome.local` |
| Radarr | `https://radarr.myhome.local` |
| Prowlarr | `https://prowlarr.myhome.local` |
| Transmission | `https://transmission.myhome.local` |
| Bazarr | `https://bazarr.myhome.local` |
| Jellyseerr | `https://jellyseerr.myhome.local` |
| Tdarr | `https://tdarr.myhome.local` |

## Browser Certificate Warning

Since this uses a self-signed certificate, browsers will show a warning on first visit. You can either:

- Click through the warning each time
- Install `certs/local.crt` into your OS/browser trust store to suppress warnings permanently

## Useful Commands

```bash
# View Traefik logs
docker logs traefik

# Follow logs in real-time
docker logs -f traefik

# Restart Traefik
docker restart traefik

# Regenerate certificate (after expiry)
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout certs/local.key -out certs/local.crt \
  -subj "/CN=*.myhome.local" \
  -addext "subjectAltName=DNS:*.myhome.local,DNS:myhome.local"
docker restart traefik
```

## Notes

- HTTP (port 80) automatically redirects to HTTPS (port 443)
- Traefik discovers services via the Docker socket — all containers with `traefik.enable=true` labels are automatically routed
- Existing direct port access (e.g., `http://<ip>:8989` for Sonarr) still works alongside Traefik. Once Traefik is confirmed working, you can optionally remove `ports:` from each service's compose file
- Services without web UIs (Watchtower, Recyclarr) and internal-only APIs (FlareSolverr) do not have Traefik routes
