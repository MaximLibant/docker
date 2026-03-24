# Transmission + Mullvad VPN

Transmission torrent client routed through a Mullvad VPN tunnel via Gluetun.

## Setup

### 1. Get Mullvad WireGuard credentials

1. Log into your [Mullvad account](https://mullvad.net/en/account/wireguard-config)
2. Generate a WireGuard configuration
3. Note the **private key** and **address** from the generated config

### 2. Set environment variables

Create a `.env` file in the stack or set these in your Docker web UI:

| Variable | Required | Example | Description |
|----------|----------|---------|-------------|
| `MULLVAD_WIREGUARD_PRIVATE_KEY` | Yes | `abcdef1234...` | WireGuard private key from Mullvad |
| `MULLVAD_WIREGUARD_ADDRESS` | Yes | `10.x.x.x/32` | WireGuard address from Mullvad |
| `MULLVAD_SERVER_CITY` | No | `Toronto` | Preferred server city (empty = auto) |
| `TZ` | No | `America/Toronto` | Timezone (default: `America/Toronto`) |

### 3. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 4. Verify the VPN is working

```bash
# Check Gluetun health
docker logs gluetun

# Verify your public IP is the VPN's IP, not your real IP
docker exec gluetun wget -qO- https://am.i.mullvad.net/connected
```

### 5. Access Transmission

Open `http://<host-ip>:9091` for the Transmission web UI.

### 6. Configure download paths

In the Transmission web UI, change the default download paths:

1. Open the preferences/settings
2. Set **Download to:** `/data/completed`
3. Enable **Use temporary folder** and set it to `/data/incomplete`

This may change depending on your specific volumes / mounts

## How it works

- **Gluetun** creates the WireGuard VPN tunnel to Mullvad
- **Transmission** uses `network_mode: "service:gluetun"`, meaning all its traffic goes through Gluetun's network (the VPN tunnel)
- Ports are exposed on the Gluetun container since Transmission shares its network stack

## Configure in Sonarr/Radarr

1. Go to Settings > Download Clients > Add
2. Select **Transmission**
3. Set host to `gluetun` and port to `9091`
4. Test and save

## Volume mounts

| Container path | Host path | Description |
|----------------|-----------|-------------|
| `/data` | `/mnt/media/library` | Media library root |

Subdirectories inside `/data`:
- `/data/downloads` — Active downloads
- `/data/incomplete` — Incomplete downloads
- `/data/completed` — Completed downloads

## Useful commands

```bash
# View Gluetun logs (VPN status)
docker logs gluetun

# View Transmission logs
docker logs transmission

# Check public IP through the VPN
docker exec gluetun wget -qO- https://am.i.mullvad.net/connected

# Restart VPN connection
docker restart gluetun
```