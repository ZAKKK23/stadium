# plane-moving-targets (stadium)

A Voronoi Tessellation Population (VTP) agent simulation with moving targets —
a MATLAB model and a dependency-light browser port of the same dynamics.
Targets here ride a closed **stadium track** — two straight sides joined by
two half-circles, like a running track — as opposed to the Tusi-couple,
independent-bouncing, aligned-lanes, or corridor variants of this project.

**[Live site →](#)** (enable GitHub Pages, see below)

## What it is

Agents ("cells") move under three local forces computed from their Delaunay
neighborhood:

- **repulsion** from the nearest neighboring agent
- **alignment** toward neighbors heading the same direction
- **homing** toward whichever target is currently closest

Each step is capped by an estimate of the agent's own Voronoi cell size, so a
crowded agent can never leap past its neighbors. Two constants shape that
balance and are live-editable on the *live simulation* tab (and in the MATLAB
control panel):

- **&nu;** — alignment strength (weight of the alignment force relative to
  repulsion + homing)
- **L** — interaction length scale (sets the distance at which repulsion/
  homing hand off, and caps how far a crowded agent can move per step)

### Target motion

You choose a number of targets (1–10). They ride a closed stadium track —
two straight segments joined by two half-circles — spaced evenly along it
and moving together in one direction. All targets share a single arc-length
position, offset by a constant spacing, so their along-track spacing is
exact and never drifts, even while spacing, speed, or the track's own shape
are changed live.

Four live-editable parameters describe the track and the targets on it:

- **Straight len** — length of each of the two straight sides
- **Corner radius** — radius of each of the two half-circle end caps
- **Spacing** — arc-length distance between consecutive targets along the
  track
- **Speed** — how fast targets travel around the loop

## Structure

```
index.html              site shell — tabs for about / live simulation / matlab version
assets/style.css        site styling
assets/sim.js           the simulation engine (Delaunay neighbor graph via d3-delaunay, force law, stadium-track target motion, canvas rendering)
assets/app.js           page wiring — tabs, sliders, MATLAB source viewer
assets/matlab_src.json  bundled MATLAB source (for the in-page code viewer)
matlab/                 original MATLAB implementation
```

## Running the web version

No build step. Either open `index.html` directly, or serve the folder:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000/`.

## Running the MATLAB version

Requires base MATLAB only (`delaunayTriangulation`, `polyshape` — no extra
toolboxes):

```
cd matlab
matlab -r dynamics
```

or open `matlab/dynamics.m` in the MATLAB editor and run it. You'll be
prompted for the number of targets on the loop (1–10). An interactive figure
then opens with a control panel (agent speed, &nu;, L, the track's straight
length and corner radius, target spacing, and shared track speed — Apply /
Pause / Reset Targets).

## Publishing to GitHub Pages

1. Create a new GitHub repository and push this folder to it (see commands
   below).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`,
   branch `main`, folder `/ (root)`.
4. Save — the site will be published at
   `https://<your-username>.github.io/<repo-name>/` within a minute or two.

```bash
cd plane-moving-targets-stadium
git init
git add .
git commit -m "Initial commit: VTP stadium-track moving-targets site"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

## Note on the JS port

The browser port implements the same force law as `dynamics.m` (repulsion +
alignment + homing, weighted by the `expReciprocal` transition function over
the Delaunay graph), with the same live-editable &nu; and L, and the same
`stadiumPos(s, straightLen, R)` arc-length parametrization for the track
(ported line-for-line from MATLAB to JS). One piece is approximated for
simplicity: the MATLAB version caps an agent's step by ray-casting its
intended direction onto the exact boundary of its Voronoi cell
(`voronoiProjectToBoundary.m`); the JS version approximates that cap as half
the distance to the nearest Delaunay neighbor. Visually and qualitatively
the dynamics match; if you need the exact cap, port
`voronoiProjectToBoundary.m` into `assets/sim.js`.
