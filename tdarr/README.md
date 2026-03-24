# Tdarr

Media transcoding tool for cleaning up audio tracks and re-encoding high-bitrate video to save space.

## Setup

### 1. Deploy the stack

Deploy `docker-compose.yml` via your Docker web UI (e.g. Portainer).

### 2. Access the web UI

Open `http://<host-ip>:8265`.

### 3. Check the node

The internal node (`UnraidNode`) should appear under the Nodes tab since `internalNode=true` is set. Make sure it's enabled and has at least 1 transcode worker assigned.

### 4. Add libraries

- **Movies**: Source folder `/data/movies`, Transcode cache `/temp`
- **TV Shows**: Source folder `/data/tvshows`, Transcode cache `/temp`

### 5. Import the flow

Go to the Flows tab and import `clean-audio-flow.json` from this folder. Alternatively, create the flow manually (see below).

Assign the flow to each library.

## Flow: Clean Audio + Transcode REMUXes

The flow does two things:

1. **Audio cleanup** — Strips all audio tracks except English (`eng`), Japanese (`jpn`), and undefined (`und`). If no matching audio exists, the file is left untouched.
2. **Video transcode** — If the video bitrate exceeds 20,000 kbps (i.e. a REMUX or near-REMUX), re-encodes to HEVC 10-bit using CPU (x265). Files below the threshold are left as-is.

### Flow diagram

```
Input File
    |
Migz3CleanAudio (keep eng, und, jpn)
    |              |
  (done)        (no change)
    |              |
    +---> Check Video Bitrate (> 20,000 kbps) <---+
              |                        |
        (high bitrate)           (low bitrate)
              |                        |
     Migz1FFMPEG_CPU             Replace Original File
     (HEVC 10-bit)                     ^
         |        |                    |
       (done)  (no change)             |
         |        |                    |
         +--------+--------------------+
```

### Plugins used

| Plugin | Purpose |
|--------|---------|
| `Tdarr_Plugin_MC93_Migz3CleanAudio` | Remove audio tracks not matching `eng,und,jpn` |
| `checkVideoBitrate` | Check if bitrate is between 20,000 and 1,000,000 kbps |
| `Tdarr_Plugin_MC93_Migz1FFMPEG_CPU` | Transcode video to HEVC 10-bit (CPU/x265) |

### Bitrate threshold rationale

- **REMUXes**: 30,000+ kbps (20-75 GB per file) — these get transcoded
- **Good encodes**: 5,000-16,000 kbps (2-15 GB per file) — left alone
- **20,000 kbps** is the cutoff to safely catch REMUXes without touching existing encodes

## Volume mounts

| Container path | Host path | Description |
|----------------|-----------|-------------|
| `/data` | `/mnt/media/library` | Media library root |
| `/temp` | `/tmp/tdarr-transcode-cache` | Transcode working directory |

## Useful commands

```bash
# View logs
docker logs tdarr
```

## Notes

- The `/temp` cache needs enough free space for at least one full movie during transcoding.
- Video transcoding is CPU-only (x265). Expect 4-8 hours per 4K REMUX. If the server has a GPU, hardware transcoding can be enabled for much faster processing (requires adding the GPU device to the compose file).
- Audio cleanup is a remux operation (no re-encoding) — it's fast and lossless.
- The audio plugin keeps all audio if no English track exists, so foreign-language films won't lose their only audio track.
