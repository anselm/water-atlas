# 2026-10-19 — CARTO basemap now needs a key; switched to OpenFreeMap

Anselm noticed the live site saying "API KEY REQUIRED" across the background
map. Diagnosed and fixed from the manager session, no room spawned.

## What happened

The basemap was CARTO's keyless raster tiles (`basemaps.cartocdn.com/rastertiles/voyager`).
CARTO now requires an API key for its basemaps: keys registered before
2026-09-23 keep working until 2026-11-30, and keyless requests get a 256 px
placeholder tile that says "API KEY REQUIRED / carto.com/basemaps/apikey".
Fetched one directly on 2026-10-18 and looked at it; that is what every visitor
saw. Nothing in this repo changed; the provider did. Free keys exist (5M
requests a month for non-commercial use) but need an email sign-up.

## The fix

`site/src/lib/map.js`: the inline raster style is replaced by the OpenFreeMap
vector style `https://tiles.openfreemap.org/styles/positron` (OpenMapTiles
schema over OpenStreetMap data; no key, no account, attribution carried by its
TileJSON). The FAQ's two attribution sentences now name OpenMapTiles,
OpenStreetMap and OpenFreeMap instead of CARTO. The data layers are added on
top exactly as before.

Verified by building and screenshotting headlessly (Playwright Chromium with
SwiftShader): the West Coast renders in the light style with the attribution
line, no console errors. Data layers were empty in that check only because this
box has no `data/water.db`; the VM exports its own.

## Also affected

`saltspring-ontology` points at the same CARTO tiles and will show the same
placeholder. Same one-line change applies there; not done in this pass.

Autodeploy is on for this project, so the push that publishes this is Anselm's.
(This project keeps its dated entries in `notes/`, not `devlog/`; left as is.)

## Follow-up, same day

The cluster-count symbol layer had no `text-font`, so MapLibre asked the new
glyph endpoint for its default "Open Sans Regular, Arial Unicode MS Regular",
which OpenFreeMap does not serve (one 404 per glyph range; the numbers still
drew). Set `text-font` to `Noto Sans Bold`, which the positron style itself uses.
