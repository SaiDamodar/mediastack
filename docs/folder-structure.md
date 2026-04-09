# Folder Structure

## Media Library

```
/media/jellyfin/
├── movies/                  ← Radarr imports here (Jellyfin: Movies library)
│   ├── Movie Title (Year)/
│   │   └── Movie.Title.Year.2160p.mkv
│   └── ...
│
├── webseries/               ← Sonarr imports TV shows here (Jellyfin: Webseries library)
│   ├── Show Name/
│   │   └── Season 01/
│   └── ...
│
├── anime/                   ← Sonarr imports anime here (Jellyfin: Anime library)
│   ├── Anime Name/
│   └── ...
│
├── music/                   ← Lidarr imports here (Jellyfin: Music library)
│   └── [Artist]/[Album]/[track].flac
│
├── books/                   ← Readarr downloads here (Kavita library)
│   └── [Book Title by Author]/
│       └── book.epub
│
└── torrents/                ← qBittorrent download staging area
    ├── radarr/              ← Radarr category downloads
    ├── sonarr/              ← Sonarr category downloads
    ├── lidarr/              ← Lidarr category downloads
    ├── readarr/             ← Readarr category downloads
    └── temp/                ← In-progress downloads
```

## Config & Compose

```
/path/to/arr-stack/
├── docker-compose.yml
└── config/
    ├── radarr/
    ├── sonarr/
    ├── lidarr/
    ├── prowlarr/
    ├── bazarr/
    ├── jellyseerr/
    ├── autobrr/
    ├── recyclarr/
    ├── readarr/
    └── kavita/
```

## Jellyfin Data

```
/var/lib/jellyfin/           ← Jellyfin system data (native install)
├── data/
│   └── jellyfin.db          ← Main database
└── root/default/            ← Library virtual folders
    ├── Movies/
    ├── Webseries/
    ├── Anime/
    ├── Music/
    └── Books/               ← Optional: add Jellyfin books library too
```

---

## How Hardlinks Work

When a download completes, *arr apps do **not** move or copy the file. They create a **hardlink**:

```
/media/jellyfin/torrents/radarr/Movie.Title.2025.mkv   ← qBittorrent seeds this
/media/jellyfin/movies/Movie Title (2025)/Movie.Title.2025.mkv  ← Jellyfin reads this
```

Both paths point to the **same inode** (same data on disk). No extra storage used. qBittorrent keeps seeding, Jellyfin has the file — zero waste.

**Requirement**: Source (`/torrents`) and destination (`/movies`, `/anime`, etc.) must be on the **same filesystem**. All paths are under `/media/jellyfin/` so this is always satisfied.

> **Note for books**: Readarr copies (not hardlinks) ebook files since they're small and the original torrent folder structure isn't needed for seeding small files.

---

## Remote Path Mapping

qBittorrent runs natively and reports host paths. The *arr apps run in Docker and see container paths. A mapping is required in each app:

| App | Host path (qBittorrent reports) | Container path (arr sees) |
|---|---|---|
| Radarr | `/media/jellyfin/torrents/radarr/` | `/downloads/radarr/` |
| Sonarr | `/media/jellyfin/torrents/sonarr/` | `/downloads/sonarr/` |
| Lidarr | `/media/jellyfin/torrents/lidarr/` | `/downloads/lidarr/` |
| Readarr | `/media/jellyfin/torrents/readarr/` | `/downloads/readarr/` |
