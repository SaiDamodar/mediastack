# Indexers

All indexers are managed in **Prowlarr** and auto-synced to Radarr, Sonarr, and Lidarr based on content categories.

## Category Mapping

| Category ID | Type | Synced To |
|---|---|---|
| 2000-series | Movies | Radarr |
| 5000-series | TV Shows | Sonarr |
| 5070 | Anime | Sonarr |
| 3000-series | Audio/Music | Lidarr |

---

## Configured Indexers (82 total)

### Music-Capable Indexers (synced to Lidarr)
| Indexer | Type | Notes |
|---|---|---|
| **RuTracker** | Semi-private | Best FLAC source, Indian music catalog. Account: snigamon |
| 1337x | Public | General, covers Indian music |
| Knaben | Public | Meta-search aggregator |
| MixtapeTorrent | Public | Music-focused |
| Nipponsei | Public | Anime/Japanese music |
| BT.etree | Public | Lossless live recordings |
| Nyaa.si | Public | Anime (music singles/OSTs) |
| dmhy | Public | Chinese releases |
| NoNaMe Club | Semi-private | Russian releases |
| Tokyo Toshokan | Public | Japanese media |

### General Indexers (movies/TV)
1337x, EZTV, Knaben, LimeTorrents, kickasstorrents.ws, and 70+ others.

---

## Adding a New Indexer

1. Go to Prowlarr → Indexers → Add Indexer
2. Search for the tracker name
3. Fill in credentials (username/password or API key)
4. Prowlarr auto-syncs to Radarr/Sonarr/Lidarr within ~5 minutes

---

## Trackers to Add (Pending Credentials)

| Tracker | URL | Type | Notes |
|---|---|---|---|
| DesiTorrents | torrent.desi | Private | Indian content, invite-only |
| LosslessClub | losslessclub.com | Private | Russian lossless music |
| Redacted | redacted.ch | Private | Best music tracker, invite-only |
| Orpheus | orpheus.network | Private | Music, invite-only |

---

## FlareSolverr

Prowlarr is configured to use FlareSolverr (`http://localhost:8191`) as a proxy for Cloudflare-protected indexers. Enabled automatically when needed — no manual action required.
