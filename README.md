# plugin-assets

Public listing artwork for [ALL 1](https://all1.ai) NativePHP plugins.
READMEs and marketplace listings hot-link these files, so **this repo stays
public** — moving or renaming a file breaks live listings.

| Plugin | Hero | Flow |
|---|---|---|
| `share-target` | [hero.png](share-target/hero.png) | [flow.png](share-target/flow.png) |
| `widgets` | [hero.png](widgets/hero.png) | [flow.png](widgets/flow.png) |
| `wifi-scan` | [hero.png](wifi-scan/hero.png) | [flow.png](wifi-scan/flow.png) |
| `geofence` | [hero.png](geofence/hero.png) | [flow.png](geofence/flow.png) |
| `actions-anywhere` | [hero.png](actions-anywhere/hero.png) | [flow.png](actions-anywhere/flow.png) |

Canonical link form (302s to the raw host):

```
https://github.com/all1web/plugin-assets/raw/main/<plugin>/hero.png
```

Sizes: hero **1760×720**, flow **1760×300** — 2× renders of 880-wide SVG
masters (committed beside the PNGs where they exist).

One exception: `actions-anywhere` stacks three 360-tall panels into a
**1760×2160** hero (promise → iOS Spotlight → Android Quick Settings).
It is a portrait image, not a banner, so anywhere the listing crops or
letterboxes to a wide slot it will shrink rather than fill — check it in
the marketplace grid before assuming the extra panels are readable there.

The visual system — palette, layout, glow recipe, renderer rules, and the
prompt for producing new art — is defined in [STYLE.md](STYLE.md).
