# SSXToolbox

A native Delphi FMX toolkit for parsing and viewing EA SSX (PS2) assets. Zero third-party dependencies.

<img width="2560" height="1392" alt="Screenshot 2026-08-24 023251" src="https://github.com/user-attachments/assets/4c887e74-cb51-4d6d-84a2-1d7188a40f7b" />

## The Problem
The modding and preservation scene for 
the PS2 SSX games is a fragmented mess. Usually, the workflow looks like this: run a script to unpack the archive, run another to convert the textures, and rely on an incomplete library (like SSXLibrary) just to get some of the geometry into Blender. 

It's a brittle, multi-step pipeline built on scattered, unmaintained scripts that don't talk to each other.

## The Solution
I got tired of jumping through hoops just to see what I extracted. The turning point was realizing Delphi's FireMonkey (FMX) framework has native 3D support. It does OBJ out of the box. 

That meant the extraction, decoding, and rendering could all live in one native app. No Blender round-trip. No middleware. Parse the binary, hand it to FMX, and look at the result on screen. One toolbox instead of a pipeline.

<img width="1922" height="1112" alt="Screenshot 2026-08-20 040949" src="https://github.com/user-attachments/assets/5abd2ef4-fa49-4694-8f5b-572deb034ba0" />

## What It Handles
*   **BIG Archives:** Unpack EA's container files.
*   **SSH Textures:** Decode straight into viewable, exportable images.
*   **PBD / WDF Geometry:** Parse and render patch-based mesh data natively via FMX.

## Format Support
Parsed unit-by-unit against real disc data - not assumed from documentation. Currently:

| Format | Titles | What's read |
| :--- | :--- | :--- |
| **BIG archives** | SSX (2000) -> SSX (2012) | All four container variants: C0FB, BIGF/BIG4, and NewBig (SSX 2012's indexed format) |
| **RefPack / QFS** | SSX, SSX Tricky, SSX 3 (PS2) | Shared LZ77 codec behind BIG payloads, SSH textures, and LUI screens |
| **SSH textures** | PS2 titles (SHPS variant) | FullColor, EightBit/EightBitCompressed, FourBit - PS2 GS swizzle handled |
| **PBD curved patches** | SSX Tricky | Bezier terrain geometry, tessellated and handed straight to FMX |
| **MAP / WorldPatch** | SSX 3 | SSX3's Bezier terrain continuation format - verified field-for-field against all 30,644 patches in BAM.BIG |
| **WDF world data** | SSX (2000), PS2 | Placed objects, terrain patches, spline segments, and lights, per grid cell |
| **MPF** | SSX (2000), Tricky, SSX 3 | Model-directory metadata; SSX On Tour and SSX3 music MPFs are identified but not yet parsed |
| **LOC** | PS2 titles | Chunked localization strings (LOCH/LOCL) |

That's 8 formats decoded end-to-end, spanning four titles. Another dozen extensions - BNK, MPC, SSS, LTG, SSF, ADL, AIP, SOP, WDX, WDR, WFX, WDS - are already identified by signature for the file browser, waiting on parsers of their own. 

*Note: Compression is handled separately from containers: ChunkZip segment metadata (SSX 2012) is parsed, but the Deflate payload itself isn't decompressed yet.*

## Under the Hood
*   **Zero external dependencies.** Every format is parsed from scratch based on the actual binary structure, rather than wrapping around someone else's partial implementation.
*   **Built to look at things.** If FMX can render it, you shouldn't need to hand it off to a second application just to prove the extraction worked.

## Build & Status
*   **Status:** Active development. Check the Issues tab for what's currently broken or missing.
*   **Building:** Requires Delphi (FMX). No package managers - just clone the repo and hit compile.
*   **License:** GPLv3

---
*SSXToolbox is part of the ThisOldCPU preservation catalog, alongside DisketteBox (floppy disk preservation) and Beholden (Eye of the Beholder toolkit).*
