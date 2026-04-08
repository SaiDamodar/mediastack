# Service Details

## Jellyfin

**Port**: 8096 | **Type**: Native systemd service | **Data**: `/var/lib/jellyfin/`

The media streaming server. Scans configured library folders and streams content to any device. Supports direct play (no transcoding) for maximum quality.

**Libraries:**
| Name | Type | Path |
|---|---|---|
| Movies | movies | `/media/jellyfin/movies` |
| Webseries | tvshows | `/media/jellyfin/webseries` |
| Anime | tvshows | `/media/jellyfin/anime` |
| Music | music | `/media/jellyfin/music` |

**Key settings:**
- Real-time monitor enabled on all libraries
- Metadata: TMDB (movies/TV), MusicBrainz + TheAudioDB (music)
- Remote access via Tailscale at `http://100.82.94.50:8096`

---

## Jellyseerr

**Port**: 5055 | **Container**: `fallenbagel/jellyseerr:latest`

The request front-end. Users browse and request movies/shows here. Jellyseerr forwards approved requests to Radarr (movies) or Sonarr (TV). Integrates with Jellyfin to show what's already available.

**Flow**: User requests → Jellyseerr → Radarr/Sonarr → Prowlarr → qBittorrent

---

## Radarr

**Port**: 7878 | **Container**: `lscr.io/linuxserver/radarr:latest`

Automates movie downloads. Monitors a wanted list, searches indexers via Prowlarr, sends grabs to qBittorrent, and imports completed downloads into the movies library using **hardlinks** (original file stays in `/torrents/radarr/` for seeding, hardlink appears in `/movies/`).

**Volume mounts (container → host):**
- `/movies` → `/media/jellyfin/movies`
- `/downloads` → `/media/jellyfin/torrents`

**Remote path mapping:**
- `localhost:/media/jellyfin/torrents/radarr/` → `/downloads/radarr/`
  *(Required because qBittorrent runs natively and reports host paths, while Radarr runs in Docker)*

---

## Sonarr

**Port**: 8989 | **Container**: `lscr.io/linuxserver/sonarr:latest`

Same as Radarr but for TV shows and Anime. Handles season packs, episode monitoring, and series-level organisation.

**Volume mounts:**
- `/tv` → `/media/jellyfin/webseries`
- `/anime` → `/media/jellyfin/anime`
- `/downloads` → `/media/jellyfin/torrents`

**Remote path mapping:**
- `localhost:/media/jellyfin/torrents/sonarr/` → `/downloads/sonarr/`

**Note**: Anime is typed as `anime` series type in Sonarr for correct Absolute episode numbering. Sonarr routes anime to `/anime` and standard shows to `/tv` based on series type.

---

## Lidarr

**Port**: 8686 | **Container**: `lscr.io/linuxserver/lidarr:latest`

Music automation — the music equivalent of Radarr. Monitors artists, searches for albums, and downloads in the configured quality format.

**Quality Profile**: **Lossless** (default)
- Allowed formats: FLAC, ALAC, APE, WavPack, FLAC 24bit, ALAC 24bit
- All lossy formats (MP3, AAC, OGG, WMA) blocked

**Monitored Artists** (12 total):
- Desi Hip Hop: DIVINE, Seedhe Maut, Raftaar, Badshah, Yo Yo Honey Singh, Emiway Bantai, MC STAN
- Bollywood: Arijit Singh, A.R. Rahman, Pritam, Vishal-Shekhar, Shankar–Ehsaan–Loy

**Volume mounts:**
- `/music` → `/media/jellyfin/music`
- `/downloads` → `/media/jellyfin/torrents`

**Remote path mapping:**
- `localhost:/media/jellyfin/torrents/lidarr/` → `/downloads/lidarr/`

---

## Prowlarr

**Port**: 9696 | **Container**: `lscr.io/linuxserver/prowlarr:latest`

The indexer hub. Aggregates 80+ torrent indexers and exposes them to Radarr, Sonarr, and Lidarr via a unified Torznab API. When any *arr app searches, it queries Prowlarr which fans out to all relevant indexers.

**Connected applications:**
| App | Sync Categories |
|---|---|
| Radarr | Movies (2000-series) |
| Sonarr | TV (5000-series), Anime (5070) |
| Lidarr | Audio/Music (3000-series) |

**Key indexers for music:**
- **RuTracker** — semi-private Russian tracker, excellent FLAC catalog including Indian music
- **1337x** — general public tracker
- **Knaben** — meta-search aggregator
- **MixtapeTorrent** — music-focused
- **Nipponsei** — anime music / Japanese releases
- **BT.etree** — lossless live recordings

**FlareSolverr** is configured as a proxy for indexers behind Cloudflare protection.

---

## qBittorrent

**Port**: 8080 | **Type**: Native (not in Docker) | **Username**: sai

The actual download client. Receives torrent/magnet links from the *arr apps, downloads to category-specific folders, and seeds after completion.

**Download categories → paths:**
| Category | Save Path |
|---|---|
| `radarr` | `/media/jellyfin/torrents/radarr/` |
| `sonarr` | `/media/jellyfin/torrents/sonarr/` |
| `lidarr` | `/media/jellyfin/torrents/lidarr/` |

Default save path: `/media/jellyfin/torrents/`

Files are **not deleted after import** — seeding continues for ratio health.

---

## Bazarr

**Port**: 6767 | **Container**: `lscr.io/linuxserver/bazarr:latest`

Automatic subtitle downloader. Monitors the movies, webseries, and anime folders and fetches subtitles from configured providers (OpenSubtitles, etc.) for new content.

**Monitored paths:**
- `/movies` → `/media/jellyfin/movies`
- `/tv` → `/media/jellyfin/webseries`
- `/anime` → `/media/jellyfin/anime`

---

## Autobrr

**Port**: 7474 | **Container**: `ghcr.io/autobrr/autobrr:latest`

Real-time torrent automation. Monitors IRC announce channels of trackers and grabs torrents the instant they're announced — before they appear on the tracker's web UI. Useful for ratio-sensitive private trackers where early grabs mean better seeding opportunities.

---

## FlareSolverr

**Port**: 8191 | **Container**: `ghcr.io/flaresolverr/flaresolverr:latest`

Headless browser proxy that solves Cloudflare CAPTCHA challenges for indexers that use Cloudflare protection. Prowlarr routes requests through FlareSolverr automatically when needed.

---

## Recyclarr

**Container**: `ghcr.io/recyclarr/recyclarr:latest`

Syncs quality profiles and custom formats from the TRaSH Guides (community-maintained best-practice configs) into Radarr and Sonarr automatically. Ensures naming conventions, quality cutoffs, and preferred release groups stay up to date without manual configuration.

Config: `/home/sai/docker/arr-stack/config/recyclarr/`
