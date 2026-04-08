# 🎬 Mediastack — Self-Hosted Media Automation

A fully automated, self-hosted media server running on **CachyOS Linux** (Arch-based). Downloads, organises, subtitles, and streams Movies, TV Shows, Anime, and Lossless Music — all on autopilot.

---

## Architecture Overview

```
                        ┌─────────────────┐
                        │   Jellyseerr    │  ← Request movies/shows (port 5055)
                        └────────┬────────┘
                                 │ requests
                    ┌────────────▼────────────┐
                    │        Prowlarr         │  ← Indexer aggregator (port 9696)
                    │   (81+ torrent sites)   │
                    └──┬──────────┬───────────┘
                       │          │ torznab
           ┌───────────▼──┐  ┌────▼──────┐  ┌──────────┐
           │    Radarr    │  │  Sonarr   │  │  Lidarr  │
           │   Movies     │  │  TV/Anime │  │  Music   │
           │  (port 7878) │  │(port 8989)│  │(port 8686│
           └──────┬───────┘  └────┬──────┘  └────┬─────┘
                  │               │               │
                  └───────────────┼───────────────┘
                                  │ adds torrent
                         ┌────────▼────────┐
                         │  qBittorrent    │  ← Download client (port 8080)
                         └────────┬────────┘
                                  │ saves to
                    ┌─────────────▼──────────────┐
                    │    /media/jellyfin/torrents  │
                    │  ├── radarr/                 │
                    │  ├── sonarr/                 │
                    │  └── lidarr/                 │
                    └─────────────┬──────────────┘
                                  │ hardlinks to
              ┌───────────────────┼───────────────────┐
              │                   │                   │
    ┌─────────▼──────┐  ┌─────────▼──────┐  ┌────────▼───────┐
    │    /movies     │  │  /webseries    │  │    /music      │
    │    /anime      │  │                │  │                │
    └─────────┬──────┘  └─────────┬──────┘  └────────┬───────┘
              └───────────────────┼───────────────────┘
                                  │ scanned by
                         ┌────────▼────────┐
                         │    Jellyfin     │  ← Media server (port 8096)
                         └────────┬────────┘
                                  │ streams to
                    ┌─────────────▼──────────────┐
                    │  Clients (LAN / Tailscale)  │
                    │  Phone · TV · Browser       │
                    └────────────────────────────┘
```

---

## Services at a Glance

| Service | Port | Role |
|---|---|---|
| [Jellyfin](docs/services.md#jellyfin) | 8096 | Media streaming server |
| [Jellyseerr](docs/services.md#jellyseerr) | 5055 | Request management UI |
| [Radarr](docs/services.md#radarr) | 7878 | Movie automation |
| [Sonarr](docs/services.md#sonarr) | 8989 | TV & Anime automation |
| [Lidarr](docs/services.md#lidarr) | 8686 | Music automation (Lossless) |
| [Prowlarr](docs/services.md#prowlarr) | 9696 | Indexer aggregator |
| [qBittorrent](docs/services.md#qbittorrent) | 8080 | Torrent download client |
| [Bazarr](docs/services.md#bazarr) | 6767 | Subtitle automation |
| [Autobrr](docs/services.md#autobrr) | 7474 | Real-time torrent automation |
| [FlareSolverr](docs/services.md#flaresolverr) | 8191 | Cloudflare bypass |
| [Recyclarr](docs/services.md#recyclarr) | — | Quality profile sync |

---

## Quick Links (Local)

- Jellyfin: http://localhost:8096
- Jellyseerr: http://localhost:5055
- Radarr: http://localhost:7878
- Sonarr: http://localhost:8989
- Lidarr: http://localhost:8686
- Prowlarr: http://localhost:9696
- qBittorrent: http://localhost:8080
- Bazarr: http://localhost:6767
- Autobrr: http://localhost:7474

## Remote Access (Tailscale)

All services accessible from any device on your Tailscale network:

```
http://100.82.94.50:<port>
```

See [Mobile Streaming Guide](docs/mobile-streaming.md) for phone setup.

---

## Documentation

- [Service Details](docs/services.md) — What each service does and how it's configured
- [Folder Structure](docs/folder-structure.md) — Media library layout and download paths
- [How They Work Together](docs/workflows.md) — End-to-end request → download → stream flow
- [Music Setup (Lossless)](docs/music-setup.md) — Lidarr FLAC/lossless configuration
- [Mobile Streaming](docs/mobile-streaming.md) — Streaming from phone via Tailscale
- [Indexers](docs/indexers.md) — Configured torrent indexers

---

## Stack

- **OS**: CachyOS Linux (Arch-based, kernel 6.x)
- **Containers**: Docker + Docker Compose (`/home/sai/docker/arr-stack/`)
- **Jellyfin**: Native systemd service
- **qBittorrent**: Native (not in Docker)
- **Networking**: All containers use `network_mode: host`
