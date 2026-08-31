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
