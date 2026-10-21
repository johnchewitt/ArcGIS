# Modelling the house — ArcGIS Maps SDK for JavaScript 5.0 + React

A single-page 3D app that builds a five-wing house (modelled from drone captures) in seven stages on a real plot, using the
ArcGIS Maps SDK for JavaScript 5.0 (`<arcgis-scene>` component + core API) with a React 19 UI.

1. **Site survey** – cleared plot, boundary, setting-out pegs and string lines for all five wings
2. **Foundations** – strip footings, blockwork and slabs (the ground turns translucent to show them)
3. **Ground floor** – roughcast-rendered walls generated around their openings
4. **Upper floor** – floor decks and upper walls, including the tall stair window
5. **Roof** – five tiled roofs and a front gable meeting in valleys, three chimneys, gutters and downpipes
6. **Windows and doors** – timber windows on stone sills, glazed entrance and gable window, garage doors, roof lights
7. **Landscape** – tarmac drive, curved front path, gravel planter, lawn and trees

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

- **Position:** click **Move house**, click the map where the house stands and turn it until it lines up
  with the roof in the imagery. The position is kept in the URL (`?at=longitude,latitude,rotation`), so
  bookmark that link, or copy the values into `DEFAULT_PLACE` to make them the default.
- `BLOCKS` – each wing's footprint, eave height, pitch, ridge direction and its windows and doors
  (side, centre, width, sill and head heights)
- `CHIMNEYS`, `DOWNPIPES`, `DRIVE`, `PATH`, `BED`, `PAVING`, `TREES` – the remaining details
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
