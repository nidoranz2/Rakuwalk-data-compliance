# ODbL compliance approach

This document explains how RakuWalk handles database material derived from OpenStreetMap and Project PLATEAU.

## OpenStreetMap-derived routing database

RakuWalk builds its pedestrian-routing database from an OpenStreetMap extract. The resulting graph is not presented as an unchanged copy of OpenStreetMap; it is a derived routing database created by filtering, transforming and indexing the source data.

RakuWalk therefore:

- credits **© OpenStreetMap contributors**;
- identifies the source licence as **ODbL 1.0**;
- records the exact reviewed source snapshot and SHA-256 in `manifests/osm.json`;
- discloses that RakuWalk processes/transforms the data;
- publishes the transformation method in this repository so that the alterations can be understood and reproduced.

## PLATEAU-derived shade information

RakuWalk uses Project PLATEAU building geometry to estimate building shadows for a specified time. Those modelled values are then attached to edges in the OSM-derived routing database.

For this combined routing/shade database workflow, RakuWalk selects the **ODbL 1.0** licensing option made available by Project PLATEAU, while retaining:

**Project PLATEAU / MLIT** attribution.

The PLATEAU source snapshot, geographic coverage, release identifier and checksum are recorded in `manifests/plateau.json`.

## Transformation summary

The routing/shade database is produced by the following sequence:

1. Obtain the reviewed OSM Kantō PBF snapshot.
2. Restrict processing to the configured Tokyo 23-ward service extent/buffer.
3. Retain pedestrian-eligible OSM ways and convert them into directed graph edges.
4. Preserve relevant access, opening-hours, indoor, covered and tunnel tags as routing metadata.
5. Calculate geographic edge length and walking time.
6. Build spatial indexes for bounded route lookup.
7. Contract compatible degree-2 filler vertices while preserving the route geometry and summed edge metrics.
8. Convert reviewed PLATEAU CityGML building geometry and usable height information into a bounded building dataset.
9. Compute a solar-position-based building-shadow estimate for a specified evaluation time.
10. Intersect the modelled shadow geometry with pedestrian edges and record modelled shade distance / coverage on those edges.

Further implementation parameters are documented in [`REPRODUCING.md`](REPRODUCING.md).

## Availability statement

This repository is the public location for the transformation description and reviewed provenance metadata associated with the RakuWalk OSM-derived routing database. If a specific derivative-database build corresponding to a deployed RakuWalk snapshot is requested, the project can additionally provide the applicable build artifact or further build metadata where reasonably available.

This is a technical compliance description, not legal advice.
