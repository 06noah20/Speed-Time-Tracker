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

The two-line mode can also start on **first motion** (Gerakan pertama): the athlete
stays still in a start box and the clock starts the instant they move.

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
