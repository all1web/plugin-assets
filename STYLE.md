# Listing art style guide

Every plugin's `hero` and `flow` images share one visual system: the **dark
console style** — a deep navy canvas, a bold two-line headline whose second
line carries a blue→violet gradient, a syntax-coloured code chip, and a dark
product scene on the right with one or two glowing accent elements telling the
plugin's story. Reference implementations: `widgets/hero.png`,
`wifi-scan/hero.png`, `share-target/flow.png`, `geofence/hero.png` +
`geofence/flow.png` (the latter two have SVG masters in this repo).

Authored as SVG, rasterized to 2× PNG. The SVG is the master; the PNG is the
artifact READMEs and listings point at.

---

## The prompt

Paste this into a plugin session to produce art in this system. Replace the
bracketed parts.

> Draw this plugin's listing art in the shared dark console style defined in
> `all1web/plugin-assets/STYLE.md`. Produce `hero.svg` (880×360) and
> `flow.svg` (880×150), rasterize both to 2× PNG with ImageMagick, and write
> them to `plugin-assets/[plugin]/`. Use `plugin-assets/geofence/*.svg` as the
> structural reference.
>
> **Hero composition.** Dark navy rounded canvas. Left column: two-line
> headline — line 1 near-white, line 2 in the house blue→violet gradient;
> three lines of muted body copy; a dark code chip with the single most
> representative API call from this repo's actual facade (read the README,
> don't invent), syntax-coloured. Right two-thirds: a dark scene showing the
> plugin's surface — an iso floor and/or the product UI on a dark phone —
> with the story's key elements glowing in the plugin accent, and a bold
> cyan annotation line + muted subline in the bottom-right corner delivering
> the punchline.
>
> **The scene must tell the plugin's actual story**, not decorate. For
> [plugin] that story is: [one sentence — what the user gains, in the order
> it happens]. Platform honesty applies to art: never depict a platform the
> README doesn't claim.
>
> **Flow strip.** A dark strip with 3–4 rounded outline cards in a row, each
> a beat of the same story: small vector icon + bold title, muted subline
> beneath. Highlight the final card (the payoff) with a gradient border.
> Draw icons as vector paths — never emoji.
>
> Follow the palette and rendering rules in STYLE.md exactly. Then **look at
> the rendered PNGs and fix what reads badly** — clipped or overflowing
> titles, occluded focal points, elements off the canvas. Iterate until it
> reads at a glance. Then commit to plugin-assets, push, point the README at
> `https://github.com/all1web/plugin-assets/raw/main/[plugin]/{hero,flow}.png`,
> and prove both URLs return HTTP 200 via `curl -L`.

---

## Palette

| Role | Hex | Use |
|---|---|---|
| Canvas | `#1B2438` → `#141C2C` | hero background gradient; flow uses `#1A2337` → `#131B2B` |
| Panel | `#1D2740` stroke `#2E3D60` | flow cards, chips |
| Deep panel | `#10182A` stroke `#263452` | code chip |
| Headline | `#F4F7FC` | line 1 |
| House gradient | `#4EA3F7` → `#8B7CF8` | headline line 2 (userSpaceOnUse), highlight borders |
| Body | `#8E9BB5` / `#7E8BA8` | copy, sublines |
| Annotation | `#38BDF8` bold + `#64738F` sub | the bottom-right punchline |
| Iso floor | top `#232F4C`→`#1B2438`, edge `#101726`, grid `#2A3A5C` | dark scenes |
| Coral | `#FF6B5A` | pins, alerts, kill/power icons |
| Green | `#34C77B` | success checks |

**One accent per plugin** for the scene's glowing subject (geofence teal
`#2DD4BF`, wifi-scan cyan-blue, widgets amber/orange). The headline gradient
stays the house blue→violet on every plugin — that's the brand constant.

**Code chip syntax:** class `#4EC9B0` · `::` `#8E9BB5` · method `#4EA3F7` ·
string `#E8A87C` · punctuation `#C8D3E8`, on `#10182A`.

## The glow recipe

Draw the element twice: a blurred pass under a crisp pass.

```xml
<filter id="glow" x="-80%" y="-80%" width="260%" height="260%">
  <feGaussianBlur in="SourceGraphic" stdDeviation="5"/>
</filter>
<circle ... stroke="#2DD4BF" stroke-width="5" opacity="0.55" filter="url(#glow)"/>
<circle ... stroke="#2DD4BF" stroke-width="2.2" stroke-dasharray="10 7"/>
```

## Projection (for iso scenes)

Flat planes use `transform="matrix(0.866,0.5,-0.866,0.5,TX,TY)"`; work in
iso-space inside the group. Screen-space offsets (float height, extrusion)
go on a wrapping translate outside it. A point `(u,v)` in slab space lands at
`x = TX + 0.866(u−v)`, `y = TY + 0.5(u+v)` — use that to plant upright
elements (pins, chips) on iso features. Iso is optional: `widgets/hero` is a
straight-on flat phone and equally canonical.

## Rendering

```bash
magick -density 192 hero.svg -resize 1760x720 hero.png
magick -density 192 flow.svg -resize 1760x300 flow.png
```

Canonical sizes: **hero 1760×720**, **flow 1760×300** (2× of the 880-wide
viewBox).

**Stacked heroes.** A hero may run taller than 360 when the plugin's story
needs more than one scene: keep the 880 width and add whole 360-tall panels
below, each with its own eyebrow, headline, scene and annotation, separated
by the faded hairline rule. `actions-anywhere` is the first (880×1080 →
2× 1760×2160: promise, then iOS Spotlight, then Android Quick Settings).
Two things to get right: rasterize with no `-resize` so the density alone
sets the size, and **repair the seam** — restarting the `bg` gradient per
panel leaves a step of roughly 14/255 in blue, so fade a matched tint back
in over the first ~80px of each lower panel (see `seamFix` in
`actions-anywhere/hero.svg`). Only reach for this when a single panel
genuinely cannot carry the story; the marketplace grid is built for
banners, so a tall hero is a deliberate trade (see the note in README).

## Rules the renderer enforces

ImageMagick rasterizes through **librsvg 2.40**, which is strict and old:

- **Never put `--` inside an XML comment.** `<!-- ----- -->` is invalid XML
  and fails the whole render with a misleading "unable to read image data".
  Use `<!-- === section === -->`.
- Shadows/glows via `feGaussianBlur` (+ `feOffset`/`feComponentTransfer` for
  drop shadows). `feDropShadow` does not exist here.
- **Vector icons only — no emoji.** Pango on this box won't reliably render
  colour emoji (older PNGs that show emoji came from a different pipeline).
- Gradient fill on `<text>` works — use `gradientUnits="userSpaceOnUse"`
  with coordinates spanning the text run (verified in `geofence/hero.svg`).
- Presentation attributes only — no `<style>` blocks, no CSS variables.
- Fonts: `Segoe UI, Helvetica Neue, Arial, sans-serif`; code in
  `Consolas, Menlo, monospace`.

## Legibility

- Titles and anything a buyer must read stay **horizontal in screen space**.
- Inside an iso transform, text ≥8px at the 880 viewBox, short strings only
  (`wifi-scan`'s network list is the ceiling); prefer abstract rounded bars
  for filler UI.
- Centre icon+title pairs as a unit: title `text-anchor="middle"` offset
  ~+11px right of card centre, icon hand-placed left of the title's start.
  Check the render — bold Segoe runs ~7.4px/char at 13.5px.

## Composition checks

Before shipping, look at the PNG and confirm: nothing clips a card or canvas
edge; the focal glow isn't occluded; annotation doesn't collide with the
scene; upright elements sit on their iso anchor points with a contact shadow.

## Linking the result

READMEs reference art by absolute URL through the `github.com` redirect form:

```
https://github.com/all1web/plugin-assets/raw/main/<plugin>/hero.png
```

This 302s to the raw host. Relative paths and private-repo URLs both 404 on
the public marketplace listing — **this repo must stay public** for any of it
to render.

## Current conformance

| Plugin | hero | flow |
|---|---|---|
| widgets | ✅ dark (flat phone) | ⚠️ legacy light-iso |
| wifi-scan | ✅ dark (iso floor) | (check) |
| share-target | ⚠️ legacy light-iso | ✅ dark |
| geofence | ✅ dark (SVG master) | ✅ dark (SVG master) |
| actions-anywhere | ✅ dark, 880×1080 stacked (SVG master) | ✅ dark (SVG master) |

Legacy light-iso pieces get redrawn with the prompt above — but **don't churn
art for a listing that's mid-review** (share-target); redraw after approval.
