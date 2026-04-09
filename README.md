# Mediastack — Self-Hosted Media Automation

A fully automated, self-hosted media server running on **CachyOS Linux** (Arch-based). Downloads, organises, subtitles, and streams Movies, TV Shows, Anime, Lossless Music, and Books — all on autopilot.

---

## Architecture Overview

```
                        ┌─────────────────┐
                        │   Jellyseerr    │  ← Request movies/shows (port 5055)
                        └────────┬────────┘
                                 │ requests
                    ┌────────────▼────────────┐
                    │        Prowlarr         │  ← Indexer aggregator (port 9696)
                    │     (80+ indexers)      │
                    └──┬───────┬───────┬──────┘
                       │       │       │ torznab
           ┌───────────▼─┐ ┌───▼────┐ ┌▼────────┐ ┌──────────┐
           │   Radarr    │ │ Sonarr │ │ Lidarr  │ │ Readarr  │
           │   Movies    │ │TV/Anime│ │  Music  │ │  Books   │
           │  port 7878  │ │  8989  │ │  8686   │ │  8787    │
           └──────┬──────┘ └───┬────┘ └────┬────┘ └────┬─────┘
                  │             │           │            │
                  └─────────────┼───────────┴────────────┘
                                │ adds torrent
                       ┌────────▼────────┐
                       │  qBittorrent    │  ← Download client (port 8080)
                       └────────┬────────┘
                                │ saves to
                   ┌────────────▼──────────────┐
                   │  /media/jellyfin/torrents  │
                   │  ├── radarr/               │
                   │  ├── sonarr/               │
                   │  ├── lidarr/               │
                   │  └── readarr/              │
                   └────────────┬──────────────┘
                                │ hardlinks / copies to
          ┌─────────────────────┼──────────────────────┐
          │                     │                      │
┌─────────▼──────┐  ┌───────────▼──────┐  ┌───────────▼──────┐
│    /movies     │  │   /webseries     │  │    /music        │
│    /anime      │  │                  │  │    /books        │
└─────────┬──────┘  └───────────┬──────┘  └───────────┬──────┘
          └─────────────────────┼──────────────────────┘
                                │ scanned by
                       ┌────────▼────────┐     ┌──────────────┐
                       │    Jellyfin     │     │    Kavita    │
                       │  (port 8096)    │     │  (port 5000) │
                       └────────┬────────┘     └──────┬───────┘
                                └──────────┬───────────┘
                                           │ streams to
                              ┌────────────▼───────────┐
                              │  Clients (LAN/Tailscale)│
                              │  Phone · TV · Browser   │
                              └────────────────────────┘
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
| [Readarr](docs/services.md#readarr) | 8787 | Book automation |
| [Prowlarr](docs/services.md#prowlarr) | 9696 | Indexer aggregator |
| [qBittorrent](docs/services.md#qbittorrent) | 8080 | Torrent download client |
| [Kavita](docs/services.md#kavita) | 5000 | Ebook reader (web UI) |
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
- Readarr: http://localhost:8787
- Prowlarr: http://localhost:9696
- qBittorrent: http://localhost:8080
- Kavita: http://localhost:5000
- Bazarr: http://localhost:6767
- Autobrr: http://localhost:7474

## Remote Access (Tailscale)

All services are accessible from any device on your Tailscale network — no port forwarding needed:

```
http://<your-tailscale-ip>:<port>
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
- **Containers**: Docker + Docker Compose
- **Jellyfin**: Native systemd service (not in Docker)
- **qBittorrent**: Native (not in Docker)
- **Networking**: All containers use `network_mode: host`

---

## Getting Started

1. Install Docker and Docker Compose
2. Clone this repo
3. Install Jellyfin and qBittorrent natively
4. Run `docker compose up -d`
5. Configure each service — see [Service Details](docs/services.md)
6. Add indexers in Prowlarr — they auto-sync to all *arr apps
7. Set up Jellyfin libraries pointing to your media folders
