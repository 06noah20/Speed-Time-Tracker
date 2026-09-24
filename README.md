# Speed Time Tracker (PECUT SOLO)

A single-file web app that turns one phone into a sprint / photo-finish timer — no
extra hardware, no accounts, no server. Everything runs in the browser and all data
stays on the device. The interface is in Malay (Bahasa Melayu).

> **PECUT SOLO** — *Pemasa pecut dengan satu telefon* ("Sprint timer with one phone").

## What it does

Point a phone across the track at the finish line and it detects when a runner's
**body** (not swinging arms or legs) crosses the yellow line, timed to sub-frame
precision using `requestVideoFrameCallback`.

### Three ways to start a run

| Mode | Malay | How the clock starts | Good for |
|------|-------|----------------------|----------|
| **Pistol** | Pistol | Phone speaks *"Ke garisan… Sedia…"* then fires a BANG; clock starts on the shot (includes reaction time) | up to ~40 m |
| **Clap / whistle** | Tepukan | Mic detects a clap/whistle/board at the start line; subtracts sound travel time | 60–100 m |
| **Two lines (zone)** | Dua garisan | Both start line (A) and finish line (B) are visible in frame | 10 m up to 100 m with an ultra-wide lens |
| **Track (jejak)** | Jejak | Follows the runner along a marked path and counts the remaining distance down to 0 m | 10–100 m, static camera |

The two-line mode can also start on **first motion** (Gerakan pertama): the athlete
stays still in a start box and the clock starts the instant they move.

### Bib-colour filter — one or many runners (two-line mode)

Because the detector reacts to *any* torso-sized movement, other people crossing the
frame can trigger a false time. To time only the intended runner(s), turn on
**"Hanya kesan pelari berbib warna tertentu"** in two-line mode and tap a runner's
bib/shirt in the camera view to sample its colour. You can **add several colours**
(up to 6), name each one, and delete any. After that, the A/B lines only fire when the
crossing blob carries a saved colour — other movers show a **blue** bar (ignored)
instead of the usual **red** (counted).

- With **two or more** colours saved, a **"Masakan"** dropdown appears: pick one
  runner to time, or **"Mana-mana pelari (auto)"** — the app times whoever crosses
  and **auto-labels the record** with that runner's name (and locks the finish to the
  same colour that started the run).
- Colour matching is hue-based (robust to brightness), with an adjustable
  **"Toleransi warna bib"** range in Settings. Best with a bright, saturated bib and
  good lighting; avoid white/black/grey.

### Track mode with live distance countdown (jejak)

A different approach for when a thin trip-line is hard to place or the runner looks
small: mark the course, then let the app **follow the runner** and show the distance
remaining, ticking down like a live measurement.

1. Choose **Jejak**, turn on the camera, and drag the **T** (finish) marker onto the
   real finish line, and the green **M** marker directly **onto the runner's body /
   shirt** at the start (a magnifier appears while dragging for precise placement).
2. Enter the **real distance** from start to finish once (e.g. 100 m) — the app can't
   measure true metres from pixels, so you supply the known distance.
3. Tap **Mula jejak**. The app samples the colour under **M** and **M then follows the
   runner** as they move. When the runner leaves the start the clock starts; a big
   **remaining-distance** number rides along with M (100 → 0 m) while the timer runs;
   when the runner reaches **T** the time is recorded. If the colour is lost it falls
   back to motion tracking within a search window.

**Honest limits.** This is *not* AR — the iPhone Measure app works because it uses
ARKit/LiDAR, which browsers cannot access. Distance labels in the *middle* of the path
are an approximation (camera perspective), but the **final time is triggered at the
real start/finish markers**, so it stays accurate. Tracking a small, fast runner in a
phone video is genuinely hard: use a static camera (tripod), a bright saturated bib,
good light, and treat the mode as **experimental** — tune sensitivity in Settings and
verify against a known time before relying on it. For true AR-grade ranging you'd need
a native iOS app (Swift + ARKit), which is a separate project.

### Auto lane lock (two-line mode)

Instead of dragging the box top/bottom marks by hand, tap **"Kunci lorong (auto)"**,
then have a runner walk once through the target lane. The detection box snaps to the
vertical band that runner occupied (plus a small margin), so only that lane is watched.
**"Buka semula"** resets the box to full height.

## Features

- **Body-based detection** — measures the longest vertical run of changed pixels so
  a torso triggers the line while a swinging arm does not.
- **Sub-frame timing** — interpolates the crossing between frames.
- **Sound-distance compensation** — subtracts the ~0.29 s that sound takes to travel
  100 m, using an air-temperature setting for the speed of sound.
- **Photo finish** — saves a thumbnail of the crossing frame with each result.
- **Records** — per-run list and a best/average table grouped by athlete, distance
  and mode; export to **CSV**.
- **Draggable finish line + magnifier loupe** for placing the line exactly on a cone.
- **Adjustable sensitivity** — motion threshold, minimum body size, clap strength,
  camera-latency correction.
- Screen wake-lock, camera picker, pinch/zoom support, voice announcements.

## Interface

The camera is the full screen. Live info (timer, status, remaining distance) and the
**Mula / Batal** button float as an overlay on top of the video. All setup — mode,
distance, athlete name, bib colours, lane lock, camera picker, records — lives in a
slide-in **Kawalan** (Controls) drawer toggled from the header. The drawer auto-closes
when a run starts so the camera has the whole screen. Use the phone in **landscape** on
a tripod for the largest view.

## Usage

1. Open `index.html` in **Chrome or Safari over HTTPS** (camera access requires a
   secure context). The easiest route is GitHub Pages (see below).
2. Place the phone at the finish line, camera pointing across the lane.
3. Drag the yellow line onto the real finish line.
4. Pick a start mode, enter the athlete's name and the distance, then tap **Mula**.

All runs are stored in `localStorage` on that device only — nothing is uploaded.

## Run it

It is a single static file, so any static host works:

- **GitHub Pages** — enable Pages on this repo (Settings → Pages → deploy from
  `main`, root). The app is served at `index.html`.
- **Locally** — just open `index.html`, or serve the folder:
  ```bash
  python -m http.server 8000
  ```
  then visit `http://localhost:8000` (camera works on `localhost` too).

## Tech

Plain HTML, CSS and vanilla JavaScript in one file — no build step, no dependencies
(only Google Fonts loaded over the network). Uses the MediaDevices, Canvas,
Web Audio (AudioWorklet), Wake Lock and Speech Synthesis APIs.

## Files

- `index.html` — the app (served by GitHub Pages).
- `pecut-solo.html` — identical copy under the original filename.
