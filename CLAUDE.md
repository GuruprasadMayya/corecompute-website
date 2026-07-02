# CoreCompute — Dark Enterprise Variant

Static marketing site for **CoreCompute FZE**, a deep-technology infrastructure
company that designs, deploys and operates AI data centres and GPU
infrastructure. Positioned alongside Nscale, Core42, NVIDIA Enterprise, OCI
and Dell in tone: engineering authority over sales pitch.

This is the **dark** design variant. A second, light/elegant variant lives at
`../CoreComputeWebsite-Beige/` — same content and structure, different theme.
See that folder's own `CLAUDE.md` for its specifics.

## Stack

Plain HTML/CSS/vanilla JS. No build step, no framework, no package.json —
open any `.html` file after `python3 -m http.server` and it just works. This
was a deliberate choice (no Node/npm available in the original build
environment) and has been kept since it makes the site trivially portable to
any static host.

```
index.html / about.html / services.html / contact.html   — the 4 pages
assets/css/style.css                                      — single stylesheet, whole design system
assets/js/main.js                                          — single script, all interactivity
assets/images/                                              — photos, logo, favicon
test-links.py                                                — link/portability validator, see below
```

## Pages

- **index.html** — Home: hero, company overview, capability cards, "why us"
  cards, industries served, 9-step delivery timeline, final CTA.
- **about.html** — Company narrative, stats, experience, expertise, team,
  vision.
- **services.html** — 10 service cards (`id="data-centre-design"` etc., used
  as footer deep-links from other pages) + the same delivery timeline.
- **contact.html** — Contact info cards, real Hamriyah Free Zone office photo
  with map-link overlay, validated enquiry form.

All 4 pages share the identical `<header class="navbar">` and `<footer
class="footer">` markup block, copy-pasted per page (no templating). When
editing nav/footer, **grep across all 4 files** — there is no single source
of truth for that markup.

## Design system (dark variant)

CSS custom properties in `:root` at the top of `style.css` — change the
palette by editing those, not by hunting for hardcoded colors (though a few
literal `rgba()` values remain where a specific overlay/photo-vignette
needed a value outside the token set — search for `rgba(6, 7, 10` to find
the deliberate near-black photo-fade overlays).

- **Base**: near-black (`--bg-base: #06070a`) with slightly lighter raised
  surfaces (`--bg-raised`, `--bg-card`) for section/card differentiation.
- **Accent**: electric blue (`--blue-bright: #3d8bff`) is the primary
  accent — buttons, links, headings, the logo mark. `--accent-amber:
  #ffab40` is the sparing complementary accent (blue's wheel-complement) —
  used for "status" highlights only (a few network-canvas particles, one
  via-dot in the logo, timeline pulse), never as a dominant color.
- **Typography**: Plus Jakarta Sans (`--font-display`, headings/UI labels) +
  IBM Plex Sans (`--font-body`, paragraph text), both loaded from Google
  Fonts in each page's `<head>`.
- **Logo**: original hand-authored SVG "circuit-C" mark (a ring with a 38°
  gap + two circuit traces into via-dots), inline in every `brand-mark`
  span — **not** a raster image. It deliberately does *not* reuse the real
  corecompute.ae logo; see the beige variant's `CLAUDE.md` for why that one
  does.

## Hero: background photo + brain data-flow animation

The hero (`index.html`) and the Services page header both use a full-bleed
photo background with a dark gradient overlay for text legibility:

```css
.hero {
  background:
    linear-gradient(100deg, rgba(6,7,10,.95) 0%, rgba(6,7,10,.82) 32%, rgba(6,7,10,.5) 62%, rgba(6,7,10,.68) 100%),
    url("../images/photo-dc-hall-bg.jpg");
  background-size: cover;
}
```

**CSS `url()` paths are relative to the CSS file's own location**
(`assets/css/style.css`), not the HTML page — hence `../images/...`, not
`assets/images/...`. Getting this wrong silently 404s with no visible error
beyond "the background just doesn't show." `test-links.py` now catches this
specific class of bug (see below) — it was a real bug shipped and caught
only by screenshotting.

On top of the photo, `<canvas class="hero-canvas" data-brain-flow>` runs
`initBrainFlowCanvas()` in `main.js`: an organic "brain"-shaped node
cluster (procedurally generated — a wobbled ring of ~26 nodes + ~14 interior
nodes, connected to their 2 nearest neighbours) sits on the right side of
the hero. Particles continuously spawn at the canvas edges and travel a
quadratic-bezier path into a random brain node, leaving a fading trail; on
arrival the target node pulses amber. This is the "data flowing into a
brain" visual. Page-headers on the other 3 pages keep the older, simpler
`initNetworkCanvas()` ambient particle field (`data-network-canvas`).

**Known CSS trap, already fixed once — don't reintroduce it**: `<canvas>`
(and `<img>`) are *replaced elements*. `position: absolute; inset: 0;`
does **not** stretch a replaced element to fill its container the way it
does a `<div>` — the canvas silently keeps its default 300×150 intrinsic
size and renders in the top-left corner. Both `.hero-canvas` and
`.page-header-canvas` need explicit `width: 100%; height: 100%;` alongside
`inset: 0`. This was the root cause of an early "the image is stuck in a
corner" bug report — looked like a JS/animation bug, was actually pure CSS.
If you add another full-bleed `<canvas>`, add those two lines or it will
recur.

## Images

All photography is real, sourced from Wikimedia Commons (CC-licensed) and
Pexels (free license, commercial use OK, no attribution required) — no
AI-generated or stock-placeholder imagery. `photo-hfza-port.jpg` is a real
photo of the actual Hamriyah Free Zone port, pulled from HFZA's own
official site (hfza.ae) since it depicts the company's real registered
address — that one is their copyrighted promotional photo, not a
freely-licensed asset; swap it for owned photography before any real
commercial launch. **Genuine liquid-cooled/immersion-cooled datacenter
photography is not available under free licenses** (it's essentially all
proprietary NVIDIA/hyperscaler press imagery) — the current photos are the
closest free alternative (dramatic blue/teal-lit server hardware, a real
GPU cluster with visible InRow cooling units, a wide cabling-hall
corridor). Revisit if the client can supply licensed photography.

The real corecompute.ae logo files (`logo.png`, `logo-source.jpeg`) are
still present in `assets/images/` but are **unused** in this variant
(superseded by the inline SVG mark) — left on disk rather than deleted.

## Copy rules (enforced, don't regress)

- **"CoreCompute FZE"** appears exactly **once** across the whole site — in
  `about.html`'s "Company" narrative section. Everywhere else (all page
  titles, meta tags, nav, footers, JSON-LD, forms) use **"CoreCompute"**
  alone.
- **No "EMEA"** anywhere — the company's footprint is described as
  "worldwide" / "around the world" / "Global", not regional.
- The canonical company/footer sentence (reused verbatim, with or without
  "FZE" depending on the rule above) is: *"CoreCompute [FZE] designs,
  implements, operates and maintains AI data centres and GPU infrastructure
  for enterprises, governments and sovereign entities across the world. We
  engineer the physical and technical foundation that AI inference,
  training and high-performance computing workloads depend on."*

## Validation: `test-links.py`

Stdlib-only Python script (no dependencies). Run from this directory:

```bash
python3 test-links.py
```

Checks, across every `*.html` file:
1. HTML tag balance (no unclosed/mismatched tags).
2. Every local `href`/`src` resolves to a file that exists on disk.
3. Every `#fragment` link resolves to a real `id="..."` in the target page.
4. **No internal link is absolute** (`/foo`) or protocol-relative
   (`//foo`) — everything must be a relative path so the entire site can be
   copied into a subdirectory, a different domain, or any static host
   without touching a single link.
5. CSS `url()` references in `assets/css/*.css` are resolved relative to
   the CSS file itself (not the site root) and checked for existence.
6. External links (`http(s)://`, `mailto:`, `tel:`) are reported but not
   treated as failures.

Exit code 0 + `ALL CHECKS PASSED` when clean; exit 1 with an itemized list
otherwise. Run this after any edit that touches links, IDs, or asset paths
— it would have caught the `url()`-relative-path bug above immediately.

## Local preview

```bash
python3 -m http.server 8765
open http://localhost:8765/index.html
```

No build step. Edit, refresh, done.

## Deployment

Pushed to GitHub Pages from the `main` branch, repo root as the Pages
source (`gh api repos/<owner>/corecompute-website/pages -f
"source[branch]=main" -f "source[path]=/"`). Live at
`https://<owner>.github.io/corecompute-website/`.
