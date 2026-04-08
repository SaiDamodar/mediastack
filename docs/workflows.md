# How the Services Work Together

## 1. Movie / TV Request Flow (via Jellyseerr)

```
User opens Jellyseerr (port 5055)
  │
  ├─ Searches for a movie/show
  ├─ Clicks Request
  │
  ▼
Jellyseerr checks Jellyfin — already exists? → No action needed
  │
  ▼
Jellyseerr forwards request to:
  ├─ Radarr  (if movie)
  └─ Sonarr  (if TV/Anime)
  │
  ▼
Radarr/Sonarr adds item to monitored list
  │
  ▼
Radarr/Sonarr searches Prowlarr (Torznab API)
  │
  ▼
Prowlarr fans out query to all relevant indexers:
  ├─ 1337x, Knaben, RuTracker ...
  └─ Returns ranked results filtered by quality profile
  │
  ▼
Radarr/Sonarr picks best result matching quality profile
  │
  ▼
Sends torrent/magnet to qBittorrent API
  │  category: "radarr" or "sonarr"
  │
  ▼
qBittorrent downloads to:
  ├─ /media/jellyfin/torrents/radarr/   (movies)
  └─ /media/jellyfin/torrents/sonarr/   (TV)
  │
  ▼
qBittorrent notifies Radarr/Sonarr → download complete
  │
  ▼
Radarr/Sonarr imports file (hardlink):
  ├─ /media/jellyfin/movies/Movie Name (Year)/file.mkv
  └─ /media/jellyfin/webseries/Show/Season X/file.mkv
  │
  ▼
Jellyfin auto-detects new file (real-time monitor)
  │
  ▼
Bazarr detects new file → fetches subtitles
  │
  ▼
Content appears in Jellyfin — ready to stream ✓
```

---

## 2. Music Flow (via Lidarr)

```
Lidarr monitors artist list (12 artists)
  │
  ├─ New album released? → triggers search
  └─ Manual search triggered by user
  │
  ▼
Lidarr searches Prowlarr (Audio/3000-series categories)
  │
  ▼
Prowlarr searches music indexers:
  ├─ RuTracker (semi-private, excellent FLAC)
  ├─ 1337x
  ├─ Knaben
  ├─ MixtapeTorrent
  └─ Nipponsei (anime music)
  │
  ▼
Lidarr picks best FLAC/Lossless result
  │
  ▼
Sends to qBittorrent (category: "lidarr")
  │
  ▼
Downloads to /media/jellyfin/torrents/lidarr/
  │
  ▼
Lidarr imports → hardlinks to:
  /media/jellyfin/music/Artist/Album/Track.flac
  │
  ▼
Jellyfin Music library auto-scans → appears in app ✓
```

---

## 3. Prowlarr ↔ *arr Sync

Prowlarr doesn't just proxy searches — it actively syncs indexer configs to each *arr app:

```
Prowlarr (source of truth for indexers)
  │
  ├─ Radarr  ← syncs indexers with categories: Movies (2000-series)
  ├─ Sonarr  ← syncs indexers with categories: TV (5000-series), Anime (5070)
  └─ Lidarr  ← syncs indexers with categories: Audio (3000-series)
```

Each *arr app gets a Torznab endpoint per indexer. When you add/remove an indexer in Prowlarr, all connected apps update automatically within minutes.

---

## 4. Autobrr Real-Time Grabbing

```
Private tracker announces new torrent (IRC channel)
  │
  ▼
Autobrr (port 7474) receives announcement instantly
  │
  ▼
Checks against configured filters (title, quality, size)
  │
  ├─ Match? → sends directly to qBittorrent
  └─ No match → ignores
```

This is faster than Prowlarr RSS polling (which checks every X minutes). Useful for grabbing rare or popular releases before they get snatched.

---

## 5. FlareSolverr Integration

```
Prowlarr needs to search indexer X (e.g. behind Cloudflare)
  │
  ▼
Prowlarr routes request through FlareSolverr (port 8191)
  │
  ▼
FlareSolverr launches headless Chromium
Solves Cloudflare challenge
Returns cookies/response to Prowlarr
  │
  ▼
Prowlarr completes the search normally
```

---

## 6. Recyclarr Quality Sync

```
Recyclarr runs on schedule
  │
  ▼
Fetches latest TRaSH Guides (community quality configs)
  │
  ▼
Pushes to Radarr & Sonarr:
  ├─ Custom Formats (HDR, Dolby Vision, REMUX, etc.)
  ├─ Quality Profile scores
  └─ Release group preferences
```

Ensures your quality settings always match community best practices without manual updates.

---

## Remote Path Mapping — Why It's Needed

qBittorrent runs **natively** (not in Docker). It reports file paths using **host paths**:
```
/media/jellyfin/torrents/radarr/Movie.mkv
```

Radarr/Sonarr/Lidarr run **in Docker** and see the torrents folder as `/downloads`:
```
/downloads/radarr/Movie.mkv
```

Without Remote Path Mapping, the *arr apps can't find the files qBittorrent downloaded.

**Configured mappings:**
| App | Host Path (qBit reports) | Container Path (arr sees) |
|---|---|---|
| Radarr | `/media/jellyfin/torrents/radarr/` | `/downloads/radarr/` |
| Sonarr | `/media/jellyfin/torrents/sonarr/` | `/downloads/sonarr/` |
| Lidarr | `/media/jellyfin/torrents/lidarr/` | `/downloads/lidarr/` |
