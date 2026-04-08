# Music Setup — Lossless with Lidarr

## Quality Profile: Lossless

Lidarr is configured to download **only lossless formats**. All lossy formats are blocked.

| Format | Allowed |
|---|---|
| FLAC | ✓ |
| FLAC 24bit | ✓ |
| ALAC | ✓ |
| ALAC 24bit | ✓ |
| APE | ✓ |
| WavPack | ✓ |
| MP3 | ✗ |
| AAC | ✗ |
| OGG | ✗ |
| WMA | ✗ |

---

## Monitored Artists

### Desi Hip Hop
| Artist | MusicBrainz ID |
|---|---|
| DIVINE | `a4a6a48a-42a5-493a-9fa1-aaf6a82217e2` |
| Seedhe Maut | `485f576c-6ade-4c0f-9406-34720f15fb02` |
| Raftaar | `f5f329bd-7fce-4b28-8208-440a96eeee91` |
| Badshah | `cd67b8c8-8b2f-4d56-b564-fbd837490256` |
| Yo Yo Honey Singh | `0dc9c4bc-8bcc-42f1-9033-bec41160377f` |
| Emiway Bantai | `9c4dad17-d19c-4797-919f-fb462018300e` |
| MC STAN | `76bd48e2-e0d9-46e8-a702-ff411b4efea7` |

### Bollywood / Composers
| Artist | MusicBrainz ID |
|---|---|
| Arijit Singh | `ed3f4831-e3e0-4dc0-9381-f5649e9df221` |
| A.R. Rahman | `e0bba708-bdd3-478d-84ea-c706413bedab` |
| Pritam | `52db98c1-0cc9-4aab-9f2d-ea8d359234f3` |
| Vishal-Shekhar | `2df4ce6a-87d6-49e5-b140-4e71066f996c` |
| Shankar–Ehsaan–Loy | `d8d3e7de-793e-49fa-8bb8-6bc732d4c8aa` |

---

## Indexers for Music

Music indexers are managed in Prowlarr and auto-synced to Lidarr (Audio categories: 3000–3060).

| Indexer | Type | Notes |
|---|---|---|
| RuTracker | Semi-private | Best source for Indian FLAC, requires account |
| 1337x | Public | General, some Indian music |
| Knaben | Public | Meta-aggregator |
| MixtapeTorrent | Public | Music-focused |
| Nipponsei | Public | Anime/Japanese music |
| BT.etree | Public | Lossless live recordings |

**To add more indexers:** Prowlarr → Indexers → Add Indexer → search for tracker name.

---

## How to Download an Album

### Automatic (new releases)
Lidarr monitors all artists above. When a new album is released and appears on indexers, Lidarr grabs it automatically.

### Manual search
1. Go to Lidarr → Artists → select artist
2. Find the album → click **Search Album**
3. Lidarr searches all indexers and grabs the best FLAC result

### Interactive search (pick specific release)
1. Same as above but click **Interactive Search**
2. Browse all available results, compare quality/size
3. Click the download icon on the preferred release

---

## Download Path

```
qBittorrent downloads to:
  /media/jellyfin/torrents/lidarr/

Lidarr imports (hardlinks) to:
  /media/jellyfin/music/[Artist Name]/[Album Name (Year)]/
    01 - Track Name.flac
    02 - Track Name.flac
    ...
    cover.jpg
    album.nfo
```

---

## Listening on Phone

For lossless streaming on Android, use **Finamp** (free) or **Symfonium** (paid):
- Server: `http://100.82.94.50:8096`
- Both apps stream FLAC natively without transcoding

See [Mobile Streaming](mobile-streaming.md) for full setup.
