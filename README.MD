# dawson-font-normalization-landing

A static landing page for the Dawson **Deterministic Font → SVG Geometry** service.

Live site: https://tbglodt-boop.github.io/dawson-font-normalization-landing/

---

## What this is

Traditional fonts are designed for **typesetting engines**, not for use as standalone geometry assets.

If you export glyphs to SVG naively, you usually inherit:

- inconsistent viewBoxes
- negative coordinates and “floating” glyphs
- baseline math that must be computed at runtime
- clipping in real renderers (browsers, GPU rasterizers, export pipelines)
- unpredictable bounding behavior and extra transforms

Dawson’s goal is the opposite: **deterministic glyph geometry** you can drop into systems that expect assets, not fonts.

---

## Output model (what you get)

When you submit a font for normalization, you don’t get a raw export dump.

You get a structured, deterministic SVG glyph package designed to be:

- predictable
- reusable
- renderer-safe
- easy to inspect
- easy to integrate

### Deliverables (Folder Set)

**01_Core/**  
Tight geometry.  
(0,0) origin, no padding, baseline-baked lowercase.  
Best for precision math and boolean workflows.  
*Note: CORE is intentionally tight and may reveal minor clipping in cheap renderers.*

**02_Safe/**  
CORE + safe padding (default **P=6** units).  
Production-friendly set designed to survive browser rounding, rasterization, and export pipelines.

**03_Deck/**  
SAFE geometry centered into a shared max bounding box.  
Every glyph shares the same viewBox.  
Perfect for contact sheets, grid layouts, and consistent “tile” placement.

**04_Meta/**  
Machine-readable metadata for the run:
- `manifest.json` (namespace, counts, padding P, baseline reference, deck box size, etc.)
- run notes / sanity stats (deterministic audit trail)

### Bonus: Test Rigs Package

**Test_Rigs/**  
Visual verification pages so you can validate output quickly.

Includes:
- paginated DECK contact sheets (labeled)
- quick inspection pages for spotting clipping, offsets, and alignment issues
- deterministic ordering so pages don’t reshuffle between runs

If you can’t prove the geometry is stable, it isn’t stable.  
We ship proof.

### Bonus: SAFE Master Defs Library

**SAFE_Master_Defs.svg**  
A compiled “defs-only” SVG containing the entire SAFE set in one `<defs>` block.

Why this matters:
- drop it into a project once
- reference any glyph instantly with `<use href="#id">`
- no need to import thousands of files individually
- ideal for SVG pipelines, web apps, icon systems, and libraries

---

## Why Dawson uses `<symbol>` (not `<g>`, not raw paths)

Dawson exports glyphs as `<symbol>` elements on purpose. This is architectural.

### 1) `<symbol>` is a viewport — `<g>` is not
`<symbol>` supports a `viewBox`.  
`<g>` inherits whatever coordinate system it’s placed into.

Deterministic exports require each glyph to carry its own:
- origin behavior
- bounds
- padding policy
- scaling semantics when instanced

That requires a per-glyph viewport → `<symbol>`.

### 2) `<symbol>` + `<use>` = instanceable geometry
The whole point is reuse:

```html
<use href="#glyph-id" width="..." height="..." />