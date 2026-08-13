# Listing art style guide

Every plugin's `hero` and `flow` images share one visual system: a **soft
isometric diorama** — a pale backdrop, a rounded ground slab in 2:1 isometric,
the product's surface floating above it, and flat UI cards orbiting on thin
connector lines.

Authored as SVG, rasterized to 2× PNG. The SVG is the master; the PNG is the
artifact READMEs and listings point at.

---

## The prompt

Paste this into a plugin session to convert its art to this system. Replace the
bracketed parts.

> Redraw this plugin's listing art in the shared isometric-diorama style
> defined in `all1web/plugin-assets/STYLE.md`. Produce `hero.svg` (880×360) and
> `flow.svg` (880×150), rasterize both to 2× PNG with ImageMagick, and write
> them to `plugin-assets/[plugin]/`.
>
> **Hero composition.** Left third: headline in two lines (second line in the
> plugin accent colour), three lines of body copy, and a dark code chip showing
> the single most representative API call. Right two-thirds: an isometric
> diorama — a rounded ground slab, the plugin's primary surface (a phone, a
> widget, a device) floating above it, and 3–4 flat UI cards orbiting, joined to
> the surface by thin connector lines with small white square nodes. Add one or
> two small props (a tree, a building block) standing on the slab for depth.
>
> **The diorama must tell the plugin's actual story**, not decorate. For
> [plugin] that story is: [one sentence — what the user gains, in the order it
> happens]. Each floating card should be a beat in that story.
>
> **Flow strip.** Three isometric tiles in a row, each carrying one object, with
> arrows between them. Labels go **horizontally in screen space beneath each
> tile** — bold title plus a muted sub-line — never inside the isometric
> transform.
>
> Follow the palette, projection, and rendering rules in STYLE.md exactly.
> Then **look at the rendered PNG and fix what reads badly** — overlapping
> elements, props floating off the slab, occluded focal points, text too small
> once skewed. Iterate until it reads at a glance.

---

## Palette

| Role | Hex | Use |
|---|---|---|
| Backdrop | `#F3F7FF` → `#DCE5F7` | page gradient, top-left to bottom-right |
| Slab top | `#FFFFFF` → `#E6EDFA` | ground plane |
| Slab edge | `#BFCCE5` | the extruded thickness under the slab |
| Ink | `#1B2440` | headlines, dark chips, device bodies |
| Muted ink | `#5B6785` / `#6B7794` | body copy, sub-labels |
| Structure | `#9DB0CE` / `#C3D0E6` | connectors, grid lines, placeholder bars |
| Cobalt | `#3B6FF5` (shadow `#2C55C9`) | data, network, server |
| Coral | `#FF6B5A` (shadow `#E0553F`) | events, alerts, transitions, map pins |

**One accent per plugin**, used for the headline's second line and the primary
subject. Geofence uses teal `#17B8A6` (deep `#0E9E8E`). Pick a distinct accent
per plugin; keep cobalt and coral as the shared supporting pair.

## Projection

2:1 isometric. Every flat plane uses the same matrix:

```
transform="matrix(0.866,0.5,-0.866,0.5,TX,TY)"
```

Work in iso-space coordinates inside that group and let the matrix do the work.
Screen-space offsets (floating height, extrusion depth) go on a **wrapping**
`<g transform="translate(...)">` outside it, since those are not in the plane.

Give every solid its thickness by drawing the same shape twice: a darker copy
offset `+6..10px` in screen space, then the top face over it.

## Rendering

```bash
magick -density 192 hero.svg -resize 1760x720 hero.png
magick -density 192 flow.svg -resize 1760x300 flow.png
```

Canonical sizes: **hero 1760×720**, **flow 1760×300** (2× of the 880-wide
viewBox).

## Rules the renderer enforces

ImageMagick rasterizes through **librsvg 2.40**, which is strict and old:

- **Never put `--` inside an XML comment.** `<!-- ----- section ----- -->` is
  invalid XML and fails the whole render with a misleading
  "unable to read image data". Use `<!-- === section === -->`.
- Use `feGaussianBlur` + `feOffset` + `feComponentTransfer` for shadows.
  `feDropShadow` does not exist in this version.
- Use presentation attributes, not `<style>` blocks or CSS variables.
- Stick to fonts that exist on the build machine: `Segoe UI, Helvetica Neue,
  Arial, sans-serif`, and `Consolas, Menlo, monospace` for code.

## Legibility

Isometric text is skewed text, and skew eats small type:

- Inside an iso transform: **one short word per card**, `≥9px` at the 880
  viewBox, bold. Everything else becomes abstract rounded bars — that reads as
  UI without asking to be read.
- Anything a buyer must actually read — flow labels, headlines, code — stays
  **horizontal in screen space**.

## Composition checks

Before shipping, look at the PNG and confirm:

- Props stand **on** the slab, not off its edge, each with a soft contact
  ellipse.
- The focal point (pin, badge, primary card) is not occluded by a floating card.
- Connector lines terminate **on** the elements they join.
- Nothing clips the canvas edge.
- The diorama is balanced across the right two-thirds, not crowded into a corner.

## Linking the result

READMEs reference art by absolute URL through the `github.com` redirect form:

```
https://github.com/all1web/plugin-assets/raw/main/<plugin>/hero.png
```

This 302s to the raw host. Relative paths and private-repo URLs both 404 on the
public marketplace listing — **this repo must stay public** for any of it to
render.
