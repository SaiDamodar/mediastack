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
- Remote access via Tailscale at `http://<your-tailscale-ip>:8096`

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

**Volume mounts:**
- `/music` → `/media/jellyfin/music`
- `/downloads` → `/media/jellyfin/torrents`

**Remote path mapping:**
- `localhost:/media/jellyfin/torrents/lidarr/` → `/downloads/lidarr/`

---

## Readarr

**Port**: 8787 | **Container**: `linuxserver/readarr:0.4.19-nightly`

Book automation — the book equivalent of Radarr. Monitors authors, searches for books across configured indexers, and downloads ebooks (EPUB, PDF, MOBI) to the books library.

**Volume mounts:**
- `/books` → `/media/jellyfin/books`
- `/downloads` → `/media/jellyfin/torrents`

**Remote path mapping:**
- `localhost:/media/jellyfin/torrents/readarr/` → `/downloads/readarr/`

**Indexers (synced from Prowlarr):** 1337x, Knaben, kickasstorrents.ws, Demonoid Clone, NorTorrent, NoNaMe Club

**Note**: Readarr uses `api.bookinfo.club` as its metadata provider (a Goodreads proxy). If this service is unavailable, use Prowlarr's search to find books manually and add them directly to qBittorrent with save path `/media/jellyfin/books/`.

---

## Kavita

**Port**: 5000 | **Container**: `jvmilazz0/kavita:latest`

Self-hosted ebook reader with a clean, mobile-friendly web UI. Reads directly from the books library — no separate app needed on your phone, just a browser.

**Supported formats:** EPUB, PDF, CBZ, CBR, and more.

**Volume mounts:**
- `/books` → `/media/jellyfin/books`

**Access:** `http://localhost:5000` (or `http://<your-tailscale-ip>:5000` remotely)

**Setup:** First launch prompts you to create an admin account. Then add a library pointing to `/books`.

---

## Prowlarr

**Port**: 9696 | **Container**: `lscr.io/linuxserver/prowlarr:latest`

The indexer hub. Aggregates 80+ torrent indexers and exposes them to all *arr apps via a unified Torznab API. When any *arr app searches, it queries Prowlarr which fans out to all relevant indexers.

**Connected applications:**
| App | Sync Categories |
|---|---|
| Radarr | Movies (2000-series) |
| Sonarr | TV (5000-series), Anime (5070) |
| Lidarr | Audio/Music (3000-series) |
| Readarr | Books (7000-series) |

**FlareSolverr** is configured as a proxy for indexers behind Cloudflare protection.

---

## qBittorrent

**Port**: 8080 | **Type**: Native (not in Docker)

The actual download client. Receives torrent/magnet links from the *arr apps, downloads to category-specific folders, and seeds after completion.

**Download categories → paths:**
| Category | Save Path |
|---|---|
| `radarr` | `/media/jellyfin/torrents/radarr/` |
| `sonarr` | `/media/jellyfin/torrents/sonarr/` |
| `lidarr` | `/media/jellyfin/torrents/lidarr/` |
| `readarr` | `/media/jellyfin/torrents/readarr/` |

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
