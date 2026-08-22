# RakuWalk data & licence compliance

This public repository documents the open-data provenance, attribution, licensing choices, and reproducibility method used by **RakuWalk**, a Tokyo walking-route prototype.

The main application repository remains private. This repository exists so that the open-data sources and the transformations applied to them can be inspected independently.

## Core disclosure

RakuWalk processes and transforms open datasets rather than redistributing untouched upstream downloads. In the application this is disclosed as:

> **RakuWalkが加工・変換して利用**

For the pedestrian routing database:

- **OpenStreetMap** data is used under **ODbL 1.0**.
- **Project PLATEAU / MLIT** building data is used to derive modelled shade information. For PLATEAU-derived information incorporated into the OSM-derived routing database, RakuWalk selects the **ODbL 1.0** licence option offered by PLATEAU, while retaining Project PLATEAU / MLIT attribution.
- This repository publishes the source metadata and the complete transformation method needed to understand and reproduce the database alterations.

## Compliance snapshot

This repository corresponds to RakuWalk private-repository snapshot:

`dcd2e2661e121360601adda3a1bcaa6ac4521e82`

## Repository contents

- `ATTRIBUTION.md` — provider, source, licence and attribution details
- `ODBL-COMPLIANCE.md` — OSM/PLATEAU database-licensing approach
- `REPRODUCING.md` — step-by-step transformation method and commands
- `manifests/` — reviewed source manifests used by RakuWalk

Raw OSM and PLATEAU downloads are intentionally not committed because they are large upstream datasets. They should be obtained from their original providers using the source/download URLs recorded in the manifests.

## Important scope note

This is a technical transparency and reproducibility repository. It is not legal advice and does not replace the authoritative licence text or the original providers' terms of use.
