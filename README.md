# NASAL — Lunar Landing Site Navigator

NASA Space Apps 2026 project. An interactive 3D web app for comparing lunar south-pole landing sites and dates by **Sun visibility** (solar power) and **Earth visibility** (direct-to-Earth radio geometry) relative to the local horizon.

Live prototype: https://claude.ai/artifact/GXQC23WF5bkzn5dDJPyU7V

## What is in this repo

| Path | What it is |
|---|---|
| `app/Main.dc.html` | The whole app: HTML, CSS and JavaScript in one file (ephemeris, terrain, WebGL engine, UI). Snapshot of artifact version `1794660935-c121`. |
| `app/canvas.json` | Canvas manifest that goes with `Main.dc.html`. |
| `docs/DATA_FORMAT.md` | The data contract for real terrain (LOLA) that replaces the procedural demo terrain. |

### Important: the app file is not a standalone page yet

`Main.dc.html` was built as a Claude design-canvas artifact. It uses that runtime's `<x-dc>` template syntax (`{{value}}`, `<sc-if>`, `<sc-for>`, `class Component extends DCLogic`) and loads its runtime scripts (`support.js`, `/_blob/...`). Opening it directly in a browser or on GitHub Pages will **not** work.

To run it outside the artifact, one of the tasks is to port it to plain HTML/JS or a small framework (React/Svelte/Vite). The logic is self-contained and has no external libraries except optional `d3` for chart paths, so the port is mostly mechanical:

- `eph()` — Sun/Earth ephemeris (Meeus lunar & solar series, IAU WGCCRE Moon orientation, TT = UTC + 69.184 s)
- `terrLib()` — procedural demo DEM, horizon profiles, horizon maps, slopes
- `series()`, `metrics()` — time series and per-site metrics (Sun %, Earth %, longest darkness, LQCIP, blackout)
- `heatAcc()`, `heat()` — landing-suitability heatmap, permanently shadowed regions
- `reachGrid()` — rover drive-time map (Dijkstra over slope)
- `renderVals()` — everything the UI shows
- `engLib()` — raw WebGL1 engine (Moon, terrain, sky, lander, rover)

## What is real and what is demo

**Real (approximate):** Sun and Earth positions over time (analytic ephemeris, not yet verified against JPL Horizons), site coordinates for Connecting Ridge, Shackleton–de Gerlache Ridge and the South Pole, NASA screening thresholds (sunlight > 65 %, Earth ≥ 50 %), the ESA LQCIP definition.

**Demo (not real data):** the 16 × 16 km terrain around each site is procedurally generated. Every terrain-dependent number is therefore illustrative: horizon blocking (G1 model), shadows, permanently shadowed regions, slopes, best landing spot, rover routes and the battery model.

Next step: replace the generator with LOLA DEM tiles in the format described in [`docs/DATA_FORMAT.md`](docs/DATA_FORMAT.md), and verify the ephemeris against JPL Horizons.

## Honest-labelling rules (from the brief)

Geometric visibility is not panel power and not a radio link budget. Nothing here is a landing-safety assessment. Keep these notes visible in the app.
