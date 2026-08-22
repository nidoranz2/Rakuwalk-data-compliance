# Reproducing the RakuWalk routing/shade database

This document records the reproducibility method for the compliance snapshot referenced in `README.md`.

## 1. Reviewed source inputs

Use the exact reviewed source releases recorded in:

- `manifests/osm.json`
- `manifests/plateau.json`

Verify the recorded SHA-256 before conversion. Do not silently replace the source files with a newer release and still describe the output as the same snapshot.

## 2. OSM pedestrian graph conversion

The RakuWalk OSM converter performs these operations:

- accepts OSM XML or OSM PBF;
- applies the configured Tokyo service bounding extent;
- evaluates OSM ways for pedestrian eligibility;
- retains ways that are walkable, conditionally walkable, or temporarily closed by `opening_hours` so that runtime rules can evaluate them;
- rejects forbidden/private/non-pedestrian segments;
- respects `oneway` directionality;
- creates directed edges between consecutive OSM nodes;
- stores relevant OSM tags on each edge;
- treats `covered=yes|arcade`, qualifying `indoor=*`, and `tunnel=yes|building_passage` as explicit indoor/covered distance;
- computes geographic edge length and walking time;
- records OpenStreetMap provenance and ODbL metadata on the graph;
- builds node and edge spatial indexes plus edge-tile indexes for bounded route lookup.

The reviewed converter version for the current snapshot is `convert-osm-v4`.

## 3. Graph contraction

The full graph is simplified by losslessly contracting maximal chains of compatible degree-2 filler vertices. A chain is contracted only where routing/access metadata is compatible. The resulting edge:

- concatenates geometry in traversal order;
- sums length and walking time;
- sums indoor/covered distance;
- preserves relevant tags/access/opening-hours semantics;
- preserves route connectivity between surviving vertices.

For the reviewed 23-ward build, this step reduced the graph from approximately **4.33 million directed edges / 1.92 million nodes** to approximately **2.40 million directed edges / 910,000 nodes**.

## 4. PLATEAU building conversion

The reviewed PLATEAU source uses the Tokyo 23-ward CityGML release described in `manifests/plateau.json`.

The conversion:

- uses the reviewed source CRS and axis order from the manifest;
- processes building geometry from `udx/bldg/*.gml`;
- uses explicit `measuredHeight` where available;
- otherwise uses a valid LOD1 z-range where appropriate;
- rejects unusable/invalid building heights;
- transforms accepted geometry to EPSG:4326;
- records dataset year, release version, source, licence, coverage meshes and checksum in the derived metadata.

The reviewed converter version is `convert-plateau-v2`.

## 5. Modelled shade materialization

RakuWalk estimates building shadow for a specified evaluation timestamp. The shade process:

- computes solar position for the evaluation time;
- casts building shadows using the accepted PLATEAU footprint/height data;
- spatially intersects those shadows with pedestrian graph edges;
- records `modeledShadeM` and `shadeCoverageM` on each affected edge;
- never credits more shade than available outdoor edge distance;
- grants no shade credit where PLATEAU coverage is missing;
- records the shade-model version, evaluation time and PLATEAU provenance in the graph metadata.

The production pipeline uses a tiled, bounded and resumable SQLite materialization so the full Tokyo 23-ward graph can be processed without loading the entire graph and all shadow geometry into memory at once.

## 6. Important model limitations

The resulting shade value is a **modelled estimate**, not a measured air-temperature or measured street-level shade observation. RakuWalk does not treat missing data as shade, does not infer tree canopies from point data, and does not describe a route as guaranteed safe.

## 7. Source-code correspondence

This public compliance description corresponds to private RakuWalk snapshot:

`dcd2e2661e121360601adda3a1bcaa6ac4521e82`

The principal private implementation files corresponding to this method are:

- `scripts/convert_osm.py`
- `scripts/simplify_pedestrian_graph.py`
- `scripts/convert_plateau.py`
- `scripts/build_edge_shade_tiled.py`
- `scripts/converter_common.py`

This repository intentionally excludes deployment credentials, Cloudflare configuration, local runtime paths and unrelated application code.
