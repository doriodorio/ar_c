# הגנת המגדל — AR Booth Game

An AR tower-defense mini-game built for the סמינר הקיבוצים exhibition booth.
Visitors scan a QR code, the game locks onto the printed backdrop using real
image tracking (MindAR), and they defend the tower from attacking spaceships
using a fixed center reticle — aim by moving the phone, tap to shoot.

## File structure

```
/
├── index.html          ← the whole game (HTML + CSS + JS)
└── assets/
    ├── ship.png        ← spaceship sprite
    └── targets.mind    ← compiled MindAR tracking target (from your backdrop artwork)
```

## Running it locally

Camera access requires a secure context (`https://` or `localhost`) — opening
`index.html` directly from disk (`file://`) will NOT show the camera, it'll
silently fall back to a placeholder background. Use one of:

- `npx serve .` (or any static file server) and open `http://localhost:...`
- Deploy to GitHub Pages (below) and test the real `https://` URL

## Deploying to GitHub Pages (recommended for the booth)

1. Create a new repo on GitHub (public or private both work for Pages on
   most plans — check your plan if private).
2. Push these files as-is, keeping the folder structure:
   ```
   git init
   git add .
   git commit -m "AR tower defense booth game"
   git branch -M main
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```
3. In the repo: **Settings → Pages → Build and deployment → Source: Deploy
   from a branch → Branch: main / (root)** → Save.
4. GitHub will give you a URL like:
   `https://<you>.github.io/<repo>/`
   (takes a minute or two to go live after the first push)
5. Point your booth's QR code at that URL.

## Re-generating `targets.mind` if the backdrop artwork changes

If you update `assets_source/for_game.png` (the backdrop design), you'll need
a fresh `.mind` file:

1. Go to https://hiukim.github.io/mind-ar-js-doc/tools/compile
2. Upload the new backdrop image
3. Download the compiled `targets.mind`
4. Replace `assets/targets.mind` with the new file

Note: if the building's position/size within the backdrop artwork changes,
the calibration constants in `index.html` (`B_TL`, `B_TR`, `B_BL`, `B_BR`,
`TARGET`, defined near the top of the `<script>` block) will also need to be
recalculated from the new artwork's pixel coordinates — ask Claude to redo
this if needed, referencing the updated artwork.

## Known tuning knobs (in `index.html`)

- `RETICLE_R` — radius (px) of the center crosshair's hit zone
- `spawnInterval` / the `1500 - elapsed*30` ramp — how fast ships spawn as
  the round progresses
- `dist0`, `duration` ranges in `spawnShip()` — how far ships start and how
  long they take to reach the tower
- Round length / lives: `timeLeft = 30`, `lives = 3` in `resetGame()`

## Credits / stack

- [MindAR](https://hiukim.github.io/mind-ar-js-doc/) v1.1.5 for image tracking
  (intentionally pinned to this version — v1.2.0+ switched to requiring ES
  modules/import maps instead of a plain `window.MINDAR` global, which isn't
  compatible with this file's plain-script setup. v1.1.5 also bundles its own
  Three.js internally, so no separate Three.js script is needed.)
- No build step — everything is plain HTML/CSS/JS, so any static host works
  (GitHub Pages, Netlify, Vercel, etc.)
