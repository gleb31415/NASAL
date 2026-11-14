# Terrain data format (task 0)

Goal: the app loads real LOLA terrain for a site from files instead of the procedural generator, without changing the rest of the UI.

One folder per site: `data/sites/<key>/`, for example `data/sites/connecting-ridge/`.

## 1. `meta.json`

```json
{
  "key": "connecting-ridge",
  "name": "Connecting Ridge",
  "lat": -89.467, "lon": -136.667,
  "N": 241, "L_km": 16, "cell_m": 66.67,
  "projection": "south polar stereographic, R = 1737.4 km, centred on the site",
  "grid": "row 0 = south edge, column 0 = west edge; x = east, y = north of the projection",
  "height_units": "m, relative to the centre pixel",
  "source": "LOLA DEM: exact product name, version, resolution and download link",
  "created": "YYYY-MM-DD"
}
```

## 2. `dem.f32`

Height map: 241 × 241 Float32 values, little-endian, row-major, in metres. Crop a 16 × 16 km square around the site and resample to 241 × 241 (about 67 m per cell), the same as the current demo grid. A finer grid can come later.

## 3. `horizon_h2.json`, `horizon_h10.json`, `horizon_h20.json`

Horizon elevation at the site centre for an observer 2, 10 and 20 m above the ground.

```json
{ "step_deg": 0.5, "elev_deg": [720 numbers] }
```

Azimuth 0° = north, increasing clockwise. Compute the horizon from terrain within at least ~100 km, not only the 16 km square: distant mountains decide whether the Sun is visible at the pole.

## 4. `psr.u8`

Permanently shadowed region mask: 241 × 241 bytes, 1 = never sunlit, 0 = sometimes sunlit. Take it from published LRO maps or compute it.

## Build script

`tools/build_site.py <lat> <lon> <key>` should produce the folder above for any site in one command.

## Acceptance checks

- Height at the centre matches the source LOLA map; the top-down view is recognisable against LROC imagery.
- The horizon profile is checked against published values for Connecting Ridge (illumination fraction or horizon profile from papers).
- The script builds a second site (Shackleton–de Gerlache Ridge) without manual edits.
- The data source (product, version, link) is recorded in the README.

## App side

A `loadSite(key)` function will read these files and return the same structure the generator returns now (`{ N, L, cell, h, min, max }` plus the horizon profile and PSR mask), so swapping the generator is one function change.
