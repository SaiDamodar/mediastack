# Indexers

All indexers are managed in **Prowlarr** and auto-synced to the *arr apps based on content categories.

## Category Mapping

| Category ID | Type | Synced To |
|---|---|---|
| 2000-series | Movies | Radarr |
| 5000-series | TV Shows | Sonarr |
| 5070 | Anime | Sonarr |
| 3000-series | Audio/Music | Lidarr |
| 7000-series | Books/Ebooks | Readarr |

---

## Configured Indexers

### General (Movies / TV)
1337x, EZTV, Knaben, LimeTorrents, kickasstorrents.ws, and many others.

### Music-Capable (synced to Lidarr)
| Indexer | Type | Notes |
|---|---|---|
| RuTracker | Semi-private | Excellent FLAC catalog |
| 1337x | Public | General, covers music |
| Knaben | Public | Meta-search aggregator |
| MixtapeTorrent | Public | Music-focused |
| Nipponsei | Public | Anime/Japanese music |
| BT.etree | Public | Lossless live recordings |
| Nyaa.si | Public | Anime (music singles/OSTs) |
| NoNaMe Club | Semi-private | Russian releases |

### Book-Capable (synced to Readarr)
| Indexer | Type | Notes |
|---|---|---|
| 1337x | Public | Has ebook category (7020) |
| Knaben | Public | Meta-search, includes book results |
| kickasstorrents.ws | Public | General, includes books |
| Demonoid Clone | Public | General |
| NorTorrent | Public | General |
| NoNaMe Club | Semi-private | Russian releases |

---

## Adding a New Indexer

1. Go to Prowlarr → Indexers → Add Indexer
2. Search for the tracker name
3. Fill in credentials (username/password or API key) if required
4. Prowlarr auto-syncs to all connected apps within ~5 minutes

---

## Private Trackers Worth Adding

| Tracker | Type | Notes |
|---|---|---|
| MyAnonamouse (MAM) | Private | Best book/audiobook tracker, requires interview |
| Redacted | Private | Best music tracker, invite-only |
| Orpheus | Private | Music, invite-only |
| DesiTorrents | Private | Indian content, invite-only |
| LosslessClub | Private | Russian lossless music |

---

## FlareSolverr

Prowlarr is configured to use FlareSolverr (`http://localhost:8191`) as a proxy for Cloudflare-protected indexers. Enabled automatically when needed — no manual action required.
