# Modelling a house — ArcGIS Maps SDK for JavaScript 5.0 + React

A single-page 3D app that builds a two-storey house in seven stages on a real plot, using the
ArcGIS Maps SDK for JavaScript 5.0 (`<arcgis-scene>` component + core API) with a React 19 UI.

1. **Site survey** – cleared plot, boundary, setting-out pegs and string lines
2. **Foundations** – strip footings, blockwork and slab (the ground turns translucent to show them)
3. **Ground floor** – brick walls generated around their openings
4. **Upper floor** – first-floor deck and timber-clad walls
5. **Roof** – gables, 40° slate pitches, ridge cap and chimney
6. **Windows and doors** – frames, glazing, front door and canopy
7. **Landscape** – lawn, paving, hedge and low-poly trees

Features: animated build-up of each element, a "Blueprint" mode that shows future stages as a
translucent wireframe, play/pause through the sequence, camera presets per stage, orbit mode,
time-of-day sun and shadows, and click-to-measure (volume of each element computed from its mesh).

## Run it

It is one file, `index.html`, with no build step. Everything loads from CDNs
(`js.arcgis.com/5.0`, `esm.sh` for React, Google Fonts), so it needs an internet connection.

**On GitHub Pages:** repo **Settings → Pages → Build and deployment → Deploy from a branch**,
choose `main` and `/ (root)`, save, and open the URL GitHub shows (usually
`https://<user>.github.io/<repo>/`).

**Locally:** serve the folder over HTTP (opening the file directly with `file://` won't load ES modules):

```bash
python3 -m http.server 8080    # or: npx serve .
# then open http://localhost:8080
```

## Change it

All the knobs are near the top of the `<script type="module">` block in `index.html`:

- `SITE` – longitude/latitude of the plot (default: open grassland in Phoenix Park, Dublin)
- `D`, `Z` – footprint, wall thickness, storey heights, roof pitch and overhang
- `OPENINGS` – doors and windows per floor and side (centre, width, sill, head)
- `STAGES` – names, copy, swatch colours and camera preset (heading / tilt / distance) per stage
- `MAT` – material colours

## How it works

- Every element is a `Mesh` in a **local vertex space** (`MeshLocalVertexSpace`) anchored at the
  plot's ground elevation (queried with `ground.queryElevation`), so all geometry is authored in
  plain metres.
- Boxes, roof slabs and gables are 8-vertex prisms; volumes come straight from the triangles.
- Lawn, paving and the cleared site are `on-the-ground` draped polygons.
- Build animations rebuild each element's mesh per frame (rise, drop, slide, grow).
- The React UI is written with [`htm`](https://github.com/developit/htm) tagged templates, which
  gives JSX-like syntax without a bundler.
