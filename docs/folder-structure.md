# Folder Structure

## Media Library

```
/media/jellyfin/
├── movies/                  ← Radarr imports here (Jellyfin: Movies library)
│   ├── Avatar - Fire and Ash (2025)/
│   │   └── Avatar.Fire.and.Ash.2025.2160p.MA.WEB-DL.DDP5.1.Atmos.DV.HDR-DVT.mkv
│   ├── Fountain of Youth (2025)/
│   └── ... (105 GB+)
│
├── webseries/               ← Sonarr imports TV shows here (Jellyfin: Webseries library)
│   ├── Fallout/
│   ├── Game of Thrones/
│   ├── Bridgerton/
│   └── ... (140 GB+)
│
├── anime/                   ← Sonarr imports anime here (Jellyfin: Anime library)
│   ├── Jujutsu Kaisen/
│   ├── One Piece/
│   ├── LIAR GAME (2026)/
│   └── ... (14 GB+)
│
├── music/                   ← Lidarr imports here (Jellyfin: Music library)
│   └── [Artist]/[Album]/[track].flac   (populated as Lidarr downloads)
│
├── books/                   ← Reserved for Readarr (empty)
│
└── torrents/                ← qBittorrent download staging area
    ├── radarr/              ← Radarr category downloads
    ├── sonarr/              ← Sonarr category downloads
    ├── lidarr/              ← Lidarr category downloads
    └── temp/                ← In-progress downloads
```

## Config & Compose

```
/home/sai/docker/arr-stack/
├── docker-compose.yml
└── config/
    ├── radarr/
    ├── sonarr/
    ├── lidarr/
    ├── prowlarr/
    ├── bazarr/
    ├── jellyseerr/
    ├── autobrr/
    └── recyclarr/
```

## Jellyfin Data

```
/var/lib/jellyfin/           ← Jellyfin system data (runs as jellyfin user)
├── data/
│   └── jellyfin.db          ← Main database
├── root/default/            ← Library virtual folders
│   ├── Movies/
│   ├── Webseries/
│   ├── Anime/
│   └── Music/
│       ├── music.mblink     ← Points to /media/jellyfin/music
│       └── options.xml
└── plugins/
```

---

## How Hardlinks Work

When a download completes, *arr apps do **not** move or copy the file. They create a **hardlink**:

```
/media/jellyfin/torrents/radarr/Avatar.Fire.and.Ash.2025.mkv   ← qBittorrent seeds this
/media/jellyfin/movies/Avatar - Fire and Ash (2025)/Avatar.Fire.and.Ash.2025.mkv  ← Jellyfin reads this
```

Both paths point to the **same inode** (same data on disk). No extra storage used. qBittorrent keeps seeding, Jellyfin has the file — zero waste.

**Requirement**: Source (`/torrents`) and destination (`/movies`, `/anime`, etc.) must be on the **same filesystem**. All paths are under `/media/jellyfin/` so this is always satisfied.

---

## Storage Summary

| Folder | Size |
|---|---|
| Movies | ~105 GB |
| Webseries | ~140 GB |
| Anime | ~14 GB |
| Music | Empty (Lidarr downloading) |
| Torrents (staging) | ~42 GB |
| **Total** | **~301 GB** |

Disk: 998 GB total, ~476 GB free.
