# Dinesh Ram's Science Corner (dineshram.in)

Personal learning site for the user's son Dinesh Ram. Static single-page site on GitHub Pages, sharing a backend with the sister site `rukminipriya.in` (Priya's Math Lab — see `../temp0802/CLAUDE.md`).

## Stack & hosting
- **Everything is one file**: `index.html` — all CSS and JS inline, no build step, no bundler.
- Hosted on **GitHub Pages**, repo `rsamineedi/dineshram`.
- Custom domain `dineshram.in`, DNS on **Hostinger**. `CNAME` file in repo root sets the custom domain. This domain **also hosts the shared backend** at the `api.` subdomain — see below.
- `dinesh-avatar.png` — his photo, header avatar (`object-position: center 25%` to frame the face after an earlier "face not centered" fix).
- `assets/` — space/science themed SVGs (atom, planet, flask, microscope, test tubes, molecule, rocket, star) used as background decoration.

## Design system
- Fonts: **Fredoka** (rounded, kid-friendly, headings) + **Inter** (body) + **Patrick Hand** (handwriting, used only in the answer-sheet modal).
- Palette: dark navy "space" theme (`--cream: #060c1f` is actually the near-black base — misleadingly named, inherited from the light-theme sibling site's variable naming). Accents: `--teal` (cyan, primary), `--panel`/`--panel-2` (card surfaces), `--green`/`--red` (correct/wrong).
- Background has a CSS-only starfield (repeating radial-gradients) plus floating SVG decorations.

## Page structure (as of last session)
Header (name, avatar) → a fixed **top-right Login button** (shows "Login" or "👤 Name") → **4 centered top-level tabs**: `About Me | Posts | Games | Quiz`, each a `.top-panel` div toggled via `data-toptab` + JS (`activateTopTab`).

- **About Me** (`#aboutPanel`) — draft placeholder card; Dinesh intends to write this himself later.
- **Posts** (`#postsPanel`) — blog list, currently empty.
- **Games** (`#gamesPanel`) — **"Build the Car"**: a real pointer-events-based drag-and-drop puzzle (works with mouse/touch/pen). Drag 4 correct car parts (Wheels, Chassis, Roof, Headlights) from a tray of 7 chips (3 decoys are other-vehicle parts) onto matching dashed drop-zones on a CSS-drawn car. On success the car animates off-screen and a "Build Again" button appears.
- **Quiz** (`#quizPanel`) — **Science Questions**: 5-question rounds from a local bank (`scienceBank` in the `<script>`, topics: Space/Body/Chemistry/Biology/Physics).
- **Login** (`#loginPanel`) — name+PIN login form; when logged in, shows a consolidated **"📜 Past Tests"** list (all games) plus the per-quiz "🏆 Scoreboard / 📜 My Trials" widget inside the Science Questions section.

Clicking any past-attempt row opens `#sheetModal` — the same paper/school-answer-sheet-styled popup as Priya's site (shared visual language, independently implemented in each file).

## History / things that were tried and reverted
This site went through several distinct entry-experience iterations before landing on its current form — worth knowing so you don't reintroduce them:
1. A full-screen **science-trivia gate** you had to answer correctly to "enter" the site.
2. A **3-question "mission control" rocket-launch trivia gate**.
3. A **"Build the Car" trivia-replaced-by-puzzle gate** (still a blocking full-screen popup).
4. User explicitly rejected the "gate to enter" concept entirely (*"Dont treat it like a game to enter... Just one section"*) → became the current **inline, non-blocking drag-and-drop section** under the Games tab, no popup, no lock.

## Backend integration
Talks to the **shared** backend at `https://api.dineshram.in` (see `../learning-api/CLAUDE.md`) — this backend is deployed on the same domain this site owns, but on a different subdomain/server (OCI instance, not GitHub Pages). Auto-detects `localhost`/`127.0.0.1` and uses `http://localhost:3000` instead for local testing.
- `LA_SITE = 'dinesh'`.
- Any name+PIN combo works — first login auto-registers.
- `localStorage` keys: `dr_science_best`, `la_token_dinesh`, `la_name_dinesh`.

## Testing locally
```
python3 -m http.server 8080
```
Then open `http://localhost:8080/`. Start `../learning-api` locally too if testing login/sync (see that repo's CLAUDE.md).

## Deploying
```
git add index.html   # add specific files, not -A
git commit -m "..."
git push
```
GitHub Pages rebuilds automatically. If `https://dineshram.in` ever starts serving a `*.github.io` wildcard cert instead of its own (cert lapsed), re-trigger issuance:
```
gh api -X PUT repos/rsamineedi/dineshram/pages -f "cname="
gh api -X PUT repos/rsamineedi/dineshram/pages -f "cname=dineshram.in"
# wait for https_certificate.state == "approved", then:
gh api -X PUT repos/rsamineedi/dineshram/pages -F "https_enforced=true"
```
This happened once already (this exact "poke" sequence fixed it both times, on both sites).
