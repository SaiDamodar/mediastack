# Mobile Streaming via Tailscale

## Setup

All services are accessible remotely through Tailscale — no port forwarding needed. Works on WiFi and mobile data anywhere.

**Server Tailscale IP**: your machine's Tailscale IP (find it with `tailscale ip -4`)

**Access URL format**: `http://<tailscale-ip>:<port>`

---

## Movies & TV (Jellyfin)

1. Install the **Jellyfin** app (Android / iOS)
2. Add server: `http://<tailscale-ip>:8096`
3. Log in with your Jellyfin account

**For best quality / no transcoding:**
- Settings → Playback → Max streaming bitrate: **Max**
- Settings → Playback → Preferred audio: **Original**

For 4K HDR/Dolby Vision content, your phone must support the codec for direct play. If it doesn't, Jellyfin will transcode on the server side.

---

## Music (Finamp — Recommended, Free)

1. Install **Finamp** from Play Store / F-Droid
2. Add server: `http://<tailscale-ip>:8096`
3. Log in → select Music library

**Why Finamp over the stock Jellyfin app:**
- Streams FLAC natively (no lossy transcoding)
- Gapless playback
- Offline download support
- Better music UI with artist/album browsing

---

## Music (Symfonium — Premium)

1. Install **Symfonium** from Play Store
2. Add Jellyfin server: `http://<tailscale-ip>:8096`
3. Supports: gapless, ReplayGain, lossless, offline sync, car integration

---

## Books (Kavita)

1. Open a browser on your phone
2. Navigate to `http://<tailscale-ip>:5000`
3. Log in with your Kavita account
4. Read directly in the browser — Kavita has a full mobile reading interface

Supports EPUB, PDF, CBZ, CBR and more. No app install needed.

---

## Bandwidth Notes

- FLAC audio: ~5–10 Mbps. Fine on 4G/5G.
- 1080p video direct play: ~15–25 Mbps. Works on good 4G/5G or WiFi.
- 4K UHD direct play: ~40–80 Mbps. Needs WiFi or 5G. On mobile data, Jellyfin will transcode to a lower bitrate automatically.

To force transcoding to a specific bitrate for mobile data, set a max bitrate in the Jellyfin app settings.
