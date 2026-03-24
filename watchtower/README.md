# Watchtower

Automatically updates running Docker containers to the latest image version.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. That's it

Watchtower monitors all running containers by default. It checks for new images daily at 4:00 AM and automatically pulls and restarts updated containers.

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `WATCHTOWER_SCHEDULE` | `0 0 4 * * *` | Cron schedule for update checks (default: daily at 4 AM) |
| `WATCHTOWER_CLEANUP` | `true` | Remove old images after updating |

### Monitor specific containers only

By default, Watchtower updates **all** running containers. To limit it to specific containers, add a label to the containers you want monitored:

```yaml
labels:
  - com.centurylinklabs.watchtower.enable=true
```

And add this environment variable to Watchtower:

```
- WATCHTOWER_LABEL_ENABLE=true
```

## Useful commands

```bash
# View logs (see what was updated)
docker logs watchtower

# Force an update check now
docker exec watchtower /watchtower --run-once
```

## Notes

- Watchtower needs access to the Docker socket to manage containers.
- It does not need to be on the `media` network since it communicates via the Docker API, not the network.
- Updates may briefly restart containers — the 4 AM schedule minimizes impact.
- If an update breaks something, you'll need to manually roll back to a specific image tag.
