# SSXToolbox

A native Delphi FMX toolkit for parsing, browsing, and viewing EA SSX assets. Zero third-party dependencies.

SSXToolbox reads the original game data directly and keeps the entire inspection pipeline in one application: archives, textures, localization, metadata, and world geometry can be decoded and viewed without first converting everything through a collection of external tools.

<img width="2560" height="1392" alt="Screenshot 2026-08-24 023251" src="https://github.com/user-attachments/assets/4c887e74-cb51-4d6d-84a2-1d7188a40f7b" />

## The Problem

The modding and preservation scene for the PS2 SSX games is a fragmented mess. Usually, the workflow looks like this: run a script to unpack the archive, run another to convert the textures, and rely on an incomplete library like SSXLibrary just to get some of the geometry into Blender.

It's a brittle, multi-step pipeline built on scattered, unmaintained scripts that don't talk to each other.

## The Solution

I got tired of jumping through hoops just to see what I extracted. The turning point was realizing Delphi's FireMonkey (FMX) framework has native 3D support. It does OBJ out of the box.

That meant the extraction, decoding, and rendering could all live in one native app. No Blender round-trip. No middleware. Parse the binary, hand it to FMX, and look at the result on screen.

One toolbox instead of a pipeline.

<img width="2560" height="1392" alt="Screenshot 2026-08-24 074942" src="https://github.com/user-attachments/assets/df2b2ce0-1b0f-449d-993e-b83e896a791f" />

## What's Working

SSXToolbox is already well beyond simple file identification.

* **Disc, folder, or individual file workflows.** Game data can be inspected without requiring a pre-extracted project layout.
* **Native archive browsing.** EA BIG containers are parsed directly and their contents dispatched to the appropriate format handlers.
* **Native 3D world viewer.** Parsed terrain is tessellated and rendered directly inside an FMX `TViewport3D`.
* **Textures and lightmaps.** SSH imagery is decoded in memory and can be applied directly to rendered terrain.
* **Multiple render modes.** Solid, wireframe, and solid + wireframe views are available for inspecting geometry.
* **Interactive camera.** Orbit, pan, and zoom operate directly against the loaded world, with clipping distances adjusted dynamically for extremely large SSX courses.
* **Viewer controls.** Texture filtering, multisampling, and frame limiting can be changed from inside SSXToolbox instead of relying on GPU control-panel overrides.
* **Runtime texture browser.** Decoded SSH images can be inspected without dumping an intermediate directory full of converted files.
* **Localization browser.** LOCH/LOCL resources are decoded into searchable/viewable strings with locale information.
* **Game-aware metadata.** Supported SSX titles are treated as games with known layouts and resources rather than merely as arbitrary collections of files.

## Native Viewer

The renderer is deliberately part of the inspection pipeline rather than an export target.

World geometry, materials, textures, and lightmaps can be checked immediately after decoding. This makes visual errors considerably easier to diagnose: bad UVs, incorrect materials, malformed patches, missing textures, and parser mistakes can be seen without exporting the result into another application first.

<!-- Replace this URL after uploading Screenshot 2026-08-25 023620.png to GitHub -->

<img width="5120" height="1440" alt="Screenshot 2026-08-25 023620" src="https://github.com/user-attachments/assets/623deebc-164c-4989-a30f-f61b6d2cd4eb" />

The viewer is built entirely on Delphi FMX. There is no embedded Blender, external renderer, conversion daemon, or third-party 3D engine behind it.

## What It Handles

* **BIG Archives:** Read EA container files and expose their contents directly to the rest of the application.
* **RefPack / QFS:** Decompress the shared EA compression format used throughout the PS2 titles.
* **SSH Textures:** Decode directly into viewable and exportable images, including PS2-specific swizzling.
* **PBD / MAP / WDF Geometry:** Parse patch-based world data and render it natively through FMX.
* **MPF Metadata:** Read model-directory information used by multiple SSX titles.
* **LOC Localization:** Decode the chunked localization resources used by the games.

<img width="2560" height="1392" alt="Screenshot 2026-08-24 021816" src="https://github.com/user-attachments/assets/55a0ff14-1876-4e8e-8ddb-a450126a09fa" />

## Format Support

| Format                 | Titles                       | What's read                                                                                                |
| :--------------------- | :--------------------------- | :--------------------------------------------------------------------------------------------------------- |
| **BIG archives**       | SSX (2000) -> SSX (2012)     | All four container variants: C0FB, BIGF/BIG4, and NewBig (SSX 2012's indexed format)                       |
| **RefPack / QFS**      | SSX, SSX Tricky, SSX 3 (PS2) | Shared LZ77 codec behind BIG payloads, SSH textures, and LUI screens                                       |
| **SSH textures**       | PS2 titles (SHPS variant)    | FullColor, EightBit/EightBitCompressed, FourBit / PS2 GS swizzle handled                                   |
| **PBD curved patches** | SSX Tricky                   | Bezier terrain geometry, tessellated and handed straight to FMX                                            |
| **MAP / WorldPatch**   | SSX 3                        | SSX3's Bezier terrain                                                                                      |
| **WDF world data**     | SSX (2000), PS2              | Placed objects, terrain patches, spline segments, and lights, per grid cell                                |
| **MPF**                | SSX (2000), Tricky, SSX 3    | Model-directory metadata; SSX On Tour and SSX3 music MPFs are identified but not yet parsed                |
| **LOC**                | PS2 titles                   | Chunked localization strings (LOCH/LOCL)                                                                   |

That's 8 formats decoded end-to-end, spanning four titles.

BNK, MPC, SSS, LTG, SSF, ADL, AIP, SOP, WDX, WDR, WFX, WDS are already identified by signature for the file browser, waiting on parsers of their own.

<img width="2560" height="1392" alt="Screenshot 2026-08-24 021654" src="https://github.com/user-attachments/assets/bbc604c5-8136-4449-b69e-6e38529f95f0" />

*Note: Compression is handled separately from containers. ChunkZip segment metadata from SSX (2012) is parsed, but the Deflate payload itself isn't decompressed yet.*

## Under the Hood

* **Zero external dependencies.** Format handling and rendering live inside the application rather than depending on a chain of external conversion utilities.
* **Memory-first inspection.** Assets don't need to be dumped to disk merely to hand them from one stage of the pipeline to another.
* **Built to look at things.** If FMX can render or display it, you shouldn't need to hand it off to a second application just to prove the extraction worked.
* **Format-specific units.** Container, codec, texture, model, localization, and world parsers are kept separate from the FMX interface and renderer.
* **Real-data development.** Parsers are continuously checked against actual files from the supported games instead of treating one known sample as the entire specification.

## Project Direction

The immediate goal is not to turn SSXToolbox into another generic extractor.

The goal is a single workbench for understanding the original SSX data: identify it, decode it, browse it, visualize it, and eventually edit or export the parts that are sufficiently understood.

The reusable EA format support exists because SSX needs it.

## Build & Status

* **Status:** Active development. Check the Issues tab for what's currently broken or missing.
* **Building:** Requires Delphi with FireMonkey support. No package managers, just clone the repo and compile.
* **Dependencies:** None beyond Delphi/FMX and the operating-system APIs used by the application.
* **License:** GPLv3

---

*SSXToolbox is part of the ThisOldCPU preservation catalog, alongside DisketteBox (floppy disk preservation) and Beholden (Eye of the Beholder toolkit).*
