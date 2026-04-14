# BeyondXBeyond 📺
### A synchronized anime broadcast — Toonami-style

Two HTML files. No server. No backend. Drop on GitHub Pages and it works.

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | Public broadcast viewer — the TV |
| `admin.html` | Password-protected control room |

---

## How the sync works

Every viewer's browser reads `Date.now()` (UTC), calculates how many seconds into the current scheduled slot we are, loads the video source for that slot, and seeks it to that position. No WebSockets, no server — it works exactly like a real TV broadcast. Whoever tunes in late catches up automatically.

---

## Deploy to GitHub Pages

1. Create a GitHub repo and push both HTML files to the root
2. **Settings → Pages → Deploy from branch → main / (root)**
3. Your broadcast is live at `https://yourusername.github.io/yourrepo/`
4. Admin panel lives at `.../admin.html`

### Making the schedule shared across all visitors

By default the schedule is stored in `localStorage` (per-browser). For everyone to see the same schedule:

**Option A — Commit `schedule.json`** *(recommended)*
1. Build your schedule in the admin panel
2. Click **Export schedule.json**
3. Commit that file to your repo root
4. `index.html` automatically tries to fetch `./schedule.json` if localStorage is empty

**Option B — Hardcode it**
Replace the `loadSchedule()` function body in `index.html`:
```js
function loadSchedule() {
  schedule = [ /* paste exported JSON array here */ ];
}
```

---

## Admin Panel

**Default password:** `toonami2000`  
Change it under the Settings tab.

### Adding a show
1. Show Name (autocompletes with classic titles)
2. Episode Title (optional)
3. **Embed URL** — see below for how to find these
4. Duration (minutes + seconds — be precise)
5. Start Time UTC (or leave blank to auto-follow previous slot)
6. Click **ADD TO LINEUP**

### Bumpers
Switch to Bumper mode and pick from quick-insert buttons:
- "Coming up next…" (15s)
- "We'll be right back." (30s)
- "Don't touch that dial." (10s)
- etc.

---

## Finding Video Sources

The admin panel's **Source Guide tab** has full instructions, but in short:

### YouTube (easiest, best sync)
Just paste any YouTube URL — the player ID is extracted and it seeks automatically.
```
https://www.youtube.com/watch?v=VIDEO_ID   ← works
https://youtu.be/VIDEO_ID                  ← works
```
Many classic anime episodes (Bebop, DBZ, Slayers, etc.) are on YouTube.

### Raw player URLs from streaming sites
1. Open an episode on AniCrush (or similar)
2. Press `F12` → Elements tab → `Ctrl+F` → search for `iframe`
3. Find the iframe whose `src` points to a player domain like `s3taku.com`, `gogoanime CDN`, `vidstreaming.io`
4. Copy that `src` URL — paste it as the Embed URL

### HLS streams (.m3u8)
1. Open DevTools → Network tab
2. Reload the page, filter by `Media` or search `.m3u8`
3. Copy the stream URL
4. Paste it — BeyondXBeyond loads HLS.js automatically and seeks to the correct position

> **Note:** Raw CDN URLs often expire within a few hours. Plan to update them day-of for scheduled broadcasts.

---

## Supported source types

| Type | Seek accuracy | Notes |
|------|--------------|-------|
| YouTube embed | ✅ Exact | Best option — use whenever possible |
| HLS (.m3u8) | ✅ Exact | Loads via HLS.js, native on Safari |
| MP4 / WebM | ✅ Exact | Direct file URL |
| Generic iframe | ⚠️ Best-effort | Sends `?t=N` hint; player may ignore it |

---

## Color scheme
- **Yellow:** `#FFE600`
- **White:** `#F5F5F0`
- **Black:** `#0A0A0A`
