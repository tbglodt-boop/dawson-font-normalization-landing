# dawson-font-normalization-landing

A static landing page for the Dawson **Deterministic Font → SVG Geometry** service.

Live site:  
https://tbglodt-boop.github.io/dawson-font-normalization-landing/

---

## What This Is

Traditional fonts are designed for typesetting engines — not for use as deterministic geometry libraries.

When glyphs are exported naively to SVG, you typically inherit:

- inconsistent viewBoxes  
- negative coordinate space  
- implicit baseline math  
- clipping under real renderers  
- runtime transforms and offset adjustments  

Dawson normalizes fonts into **stable, standalone SVG symbol libraries** designed for predictable system behavior.

---

## Output Model (What You Get)

When a font is submitted for normalization, the result is not a raw export dump.

It is a structured, deterministic SVG glyph package engineered to be:

- predictable  
- reusable  
- renderer-safe  
- easy to inspect  
- easy to integrate  

---

## Deliverables

### 01_Core/

Tight geometry.  
(0,0) origin.  
Baseline-baked lowercase.  
No padding.

Best for precision math and boolean workflows.

Note: CORE is intentionally tight and may reveal minor clipping in weak renderers.

---

### 02_Safe/

CORE geometry plus Safe padding (default P=6 units).

This is the production-friendly set designed to survive:

- browser rounding  
- rasterization  
- GPU antialiasing  
- export pipelines  
- scaling via `<use>`  

Padding exists only inside the viewBox.  
It does not modify advance width or glyph design.

---

### 03_Deck/

SAFE geometry centered into a shared maximum bounding box.

Every glyph shares the same viewBox.

Designed for:

- contact sheets  
- grid systems  
- tile placement  
- preview layouts  

---

### 04_Meta/

Machine-readable metadata for the run:

- `manifest.json` (namespace, counts, padding P, baseline reference, deck box size, etc.)  
- run notes / sanity stats  
- deterministic audit trail  

---

## Bonus: Test_Rigs/

Visual verification pages so geometry stability can be proven quickly.

Includes:

- paginated DECK contact sheets  
- clipping inspection pages  
- alignment checks  
- deterministic ordering (pages do not reshuffle between runs)  

If the geometry cannot be verified visually, it is not stable.

---

## Bonus: SAFE_Master_Defs.svg

A compiled defs-only SVG containing the entire SAFE set inside one `<defs>` block.

Why this matters:

- drop it into a project once  
- reference any glyph with `<use href="#id">`  
- no need to import thousands of files  
- ideal for SVG pipelines, web apps, icon systems, and libraries  

---

## Why Dawson Uses `<symbol>`

Dawson exports glyphs as `<symbol>` elements intentionally.

This is architectural.

### 1) `<symbol>` Is a Viewport

`<symbol>` supports its own `viewBox`.  
`<g>` does not.

Each glyph must define:

- its own coordinate system  
- its own bounds  
- its own origin  
- its own scaling behavior  

Deterministic geometry requires per-glyph viewports.

---

### 2) `<symbol>` + `<use>` = Clean Instancing

```html
<use href="#glyph-id" width="..." height="..." />
```

This provides:

- no geometry duplication  
- no runtime transforms  
- centralized definitions  
- reduced DOM weight  

---

### 3) Encapsulation

Baseline alignment, SAFE padding, and DECK centering are baked into geometry.

`<symbol>` keeps each glyph self-contained so it behaves the same in every environment.

---

### 4) Wrapper Flexibility

The geometry is fully baked.

If needed, `<symbol>` can be converted to:

- `<g id="...">`  
- `<path id="...">`  
- inline geometry  
- React components  
- CAD ingestion  
- game engine ingestion  

The coordinates do not change.

---

## SAFE Padding — Why 6 Units

Because 2 units is mathematically sufficient but operationally fragile.

Most fonts use UPM = 2048.

At 100px render:

- 2 units ≈ 0.1px (below rounding noise)  
- 6 units remains visually negligible but survives real rendering environments  

6 units:

- survives subpixel rounding  
- prevents edge clipping  
- tolerates stroke expansion  
- holds up under scaling  

Padding does not change glyph proportions or advance width.

It prevents support problems.

---

## Baseline Normalization

Raw font exports assume a typesetting engine will calculate baseline alignment.

Naive exports introduce:

- conditional Y-offset logic  
- runtime baseline detection  
- mixed-case alignment math  
- increased error surface  

Dawson bakes baseline alignment into geometry during export.

Result:

- no runtime baseline math  
- no conditional logic  
- mixed-case alignment works immediately  

Lowercase letterforms are not modified.  
Only coordinate space is normalized.

---

## Strokes and ViewBox Clipping

Stroking `<use>` references is inconsistent across renderers.

If stroke is required, apply it to the `<path>` inside the `<symbol>` using CSS variables.

Important:

A stroke expands outward from geometry.

Required padding ≥ stroke_width / 2

If heavy strokes are needed, export with a larger SAFE padding target.

Default exports optimize deterministic fill geometry, not arbitrary outline expansion.

---

## Repo Notes

This repository contains:

- Static HTML  
- Static CSS  
- SVG sidecar assets  

No build system.  
No framework.  
No backend.

Served directly via GitHub Pages from `main`.

---

## License / Usage

This repository is a public-facing landing page for a deterministic glyph normalization service.

If reused or forked, retain attribution and do not imply endorsement. 
