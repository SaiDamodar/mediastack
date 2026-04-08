# Mobile Streaming via Tailscale

## Setup

**Server Tailscale IP**: `100.82.94.50`
**Jellyfin port**: `8096`
**Access URL**: `http://100.82.94.50:8096`

Your phone (realme GT 6T) is already connected to the Tailscale network. No port forwarding needed — works on WiFi and mobile data anywhere.

---

## Movies & TV (Jellyfin Android)

1. Install **Jellyfin** from Play Store
2. Add server: `http://100.82.94.50:8096`
3. Log in with your Jellyfin account

**For lossless / no transcoding:**
- Settings → Playback → Max streaming bitrate: **Max**
- Settings → Playback → Preferred audio: **Original**
- Disable "Allow transcoding" if the option is available

For 4K HDR/Dolby Vision content, your phone must support the codec for direct play. If it doesn't, Jellyfin will transcode — this uses CPU on the server and may reduce quality.

---

## Music (Finamp — Recommended, Free)

1. Install **Finamp** from Play Store / F-Droid
2. Add server: `http://100.82.94.50:8096`
3. Log in → select Music library

**Why Finamp over stock Jellyfin app:**
- Streams FLAC natively (no lossy transcoding)
- Gapless playback
- Offline download support
- Better music UI with artist/album browsing

---

## Music (Symfonium — Premium)

1. Install **Symfonium** from Play Store (paid ~₹500)
2. Add Jellyfin server: `http://100.82.94.50:8096`
3. Supports: gapless, ReplayGain, lossless, offline sync, car integration

---

## Other Devices on Tailscale

| Device | Tailscale Status | Jellyfin URL |
|---|---|---|
| realme GT 6T (phone) | Active | `http://100.82.94.50:8096` |
| Panasonic 4K TV | Idle | `http://100.82.94.50:8096` |
| Raspberry Pi 4 | Offline | — |
| OnePlus Nord 2 | Offline | — |

For the TV, install the **Jellyfin for Android TV** app and add the same server URL.

---

## Bandwidth Notes

- FLAC audio: ~5–10 Mbps depending on bitrate. Fine on 4G/5G.
- 1080p video direct play: ~15–25 Mbps. Works on good 4G/5G or WiFi.
- 4K UHD direct play: ~40–80 Mbps. Needs WiFi or 5G home network. If on mobile data, Jellyfin will transcode to a lower bitrate.

To force transcoding to a specific bitrate for mobile data, set a max bitrate in the Jellyfin app settings.
