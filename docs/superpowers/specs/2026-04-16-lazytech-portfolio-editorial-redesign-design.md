# Lazy Tech Portfolio — Editorial Redesign

**Date:** 2026-04-16
**Author:** Kai
**Status:** Implemented

---

## 1. Goal

Redesign the Lazy Tech single-page portfolio so it:

- Reads as a premium editorial piece rather than a generic dev-portfolio template.
- Carries enough content weight to justify higher project prices.
- Keeps the single-file, no-build-step deployment model.

## 2. Direction

**Modern Editorial + warm hand-drawn accents.**

- Backbone: magazine-style typography, strict section rhythm, wide serif headlines, mono metadata.
- Warmth layer: LXGW WenKai hand-written Chinese font applied to key accented words, paper-textured cream background, imperfect SVG wave underlines, "torn page" card shadows.

Rejected alternatives:

- **Luxury minimal** — too thin for the amount of new content.
- **Classic Swiss** — risk of looking over-designed rather than expensive.

## 3. Visual System

### Palette (warm editorial)

| Token | Value | Purpose |
|---|---|---|
| `--paper` | `#f5f1e8` | Page background (warm cream) |
| `--paper-card` | `#faf6ec` | Card surfaces |
| `--paper-deep` | `#ede7d6` | Inset blocks, dividers |
| `--ink` | `#1a1a1a` | Body text |
| `--ink-soft` | `#5a5651` | Secondary text |
| `--rule` | `#d9d2c2` | Section dividers |
| `--brick` | `#c2410c` | Accent — replaces original blue |
| `--indigo` | `#1e3a8a` | Link color (sparingly) |

Brick on paper = 7.4:1 contrast (AAA). Accent chosen for its "printer's ink" tone — reads as premium on warm background.

### Typography

| Role | Font | Weight |
|---|---|---|
| Display headings (CJK) | Noto Serif TC | 900 |
| Display headings (Latin) | Fraunces | 700–900 |
| Body | Noto Sans TC / Inter | 400 |
| Metadata / labels | JetBrains Mono | 500 |
| Hand-drawn accents | **LXGW WenKai** (霞鶩文楷) | 700 |

CJK fonts intentionally listed **first** in the stack to avoid Fraunces forcing CJK glyphs to fallback. LXGW WenKai is loaded from jsDelivr (`lxgw-wenkai-webfont@1.7.0`) and applied to `<em>` elements inside display headings — this is where the "warm hand-drawn" flavor enters the Modern Editorial skeleton.

### Decorative elements

- **SVG wave underline** — drawn via quadratic Bézier, stroke-dasharray animated on load (1.1s ease).
- **Drop cap** — first-letter of About lead paragraph, 96px, brick red, LXGW WenKai.
- **Paper noise** — inline SVG `feTurbulence` overlay at 35% opacity, multiply blend mode, fixed-position.
- **Section label star (✦)** — inline SVG, brick color, uppercase mono label to the right.
- **Torn-page shadow on cards** — `box-shadow: 6px 8px 0 var(--rule)` on hover. Flat, offset shadow reads as "card lifted off paper".

### Container widths (the Editorial rhythm tool)

- **Narrow (680px):** About, FAQ, long prose. Ensures comfortable reading measure.
- **Main (1000px):** Services, Works, Pricing, Writing, Process, Stack.
- **Wide (1200px):** Reserved (currently unused, available for future full-width visuals).

Mixing widths is what makes the layout read as "edited" rather than "stacked".

## 4. Information Architecture

```
01  Nav              — logo, language toggle, Contact
02  Hero             — display title + wave underline + status tag
03  Stats            — 4 editorial metrics (5×, 3–7, 857, ∞)
04  About            — drop-cap narrative, 680px
05  Services         — 5 cards, numbered SERVICE · 01–05
06  Case Study       — PromptLite, 5 technical decisions + pull quote
07  Works            — PromptLite featured (magazine side-by-side)
08  Pricing          — Starter / Pro / Custom (Pro is dark-featured)
09  Writing          — 4 teaser titles (placeholder, linkable later)
10  Process          — 4 steps with deliverables sidebar
11  Stack            — mono-styled tag wall (15 tools)
12  FAQ              — 8 questions, accordion, aria-compliant
13  CTA              — big "Ready to start?" with email-only buttons
14  Footer           — mono, single line
```

### Content decisions

- **No Fiverr gating** — hero button goes to `#pricing`, all plan CTAs are email. Keeps conversion path on-site.
- **Works grid collapsed to single magazine feature** — one live project (PromptLite) reads as premium; two empty placeholder cards would read as padding.
- **Placeholder-but-believable copy** — About, Writing, FAQ filled with realistic voice so the page is shippable now; Kai edits later when real content is ready.

## 5. Case Study Structure

Five technical decisions, each ~80 words:

1. **4,285 static pages, zero backend** — scale via build time, not runtime.
2. **Monorepo-lite: web + extension share one dataset** — Vite alias `@data/*` into the Next.js site.
3. **i18n without a framework** — middleware + plain TypeScript modules, 5 locales.
4. **Manifest V3 with one permission** — `storage` only. No activeTab, no content scripts.
5. **Dynamic OG via Edge Runtime** — `next/og` generates 4000+ cards from one route.

Frames PromptLite as a *decision log*, not a feature list — positions Kai as someone who makes deliberate tradeoffs, which is what higher-budget clients buy.

Pull quote at close: *"懶不是偷懶，是對複雜度的節制。"* (Lazy isn't laziness — it's restraint in the face of complexity.)

## 6. Interactions & Motion

Restrained — Editorial animations should feel invisible in isolation, considered in aggregate.

- **Scroll progress bar** (top, 2px brick) — updated via `requestAnimationFrame`.
- **Reveal on scroll** — `IntersectionObserver` with 0.12 threshold and `-60px` rootMargin; each section's label/title/subtitle/content stagger via inline `transitionDelay`.
- **Hero wave underline** — SVG path with `stroke-dasharray: 350`, animated to `stroke-dashoffset: 0` over 1.1s ease at 0.7s delay.
- **Drop cap entrance** — scale 0.92→1 + opacity 0→1, triggered by reveal class.
- **Case-study number parallax** — `.case-num` translateY scaled by normalized viewport position, bounded ±9px.
- **Page fade-in** — body `opacity 0→1` over 500ms to hide font swap.
- **Reduced motion** — media query resets all of the above to instant; scroll-behavior: auto; wave drawn to full.

## 7. Accessibility

- FAQ uses `aria-expanded`, `aria-controls`, linked `role="region"` answers.
- Semantic headings (`h1` → `h2`) for each section.
- Sufficient contrast (verified: brick on paper = AAA, ink on paper = AAA).
- `prefers-reduced-motion` respected globally.
- `alt` text on OG image (`og:image:alt`).
- Hero button keyboard-navigable; `onClick` scroll uses native behavior.

## 8. SEO & Metadata

- `<title>`, `<meta name="description">` with bilingual pitch.
- Open Graph: `og:type`, `og:title`, `og:description`, `og:image`, `og:image:width`/`height`/`alt`, `og:locale`, `og:locale:alternate`.
- Twitter: `summary_large_image` card with matching image.
- JSON-LD `ProfessionalService` schema with service types and provider info.
- SVG favicon inlined as data URI (brick red `L` on paper).

## 9. OG Image

- **Spec:** 1200 × 630 PNG (social standard).
- **Source:** [og-preview.html](../../../og-preview.html) — full HTML+CSS reproduction at exact dimensions.
- **Output:** [og.png](../../../og.png) — generated via Playwright screenshot at viewport 1200×630.
- **Regeneration:** edit `og-preview.html`, reload in browser at 1200×630 viewport, save as `og.png`. No build pipeline.

Content: logo top-left, "Available for Work" tag top-right, huge "Your idea, built fast" headline with brick-red "built fast" in LXGW WenKai + wave underline, url and tagline at bottom.

## 10. Technical Delivery

- **Single file:** `index.html` — React 18 UMD + Babel standalone from CDN, no build step.
- **Fonts:** Google Fonts (Fraunces, Inter, JetBrains Mono, Noto Sans/Serif TC) + jsDelivr (LXGW WenKai).
- **No dependencies installed** — editable on any machine with a browser and a text editor.
- **Static hosting-ready:** drop into Vercel / Cloudflare Pages / any static host.
- **Preview locally:** `python -m http.server 5173` → `http://localhost:5173/`.

## 11. Deferred

- Real content for About, Writing posts, FAQ voice refinement.
- Update `og:image` to absolute URL once domain is live.
- Fiverr profile link if/when activated (current CTAs email-only).
- Additional work case studies as projects ship.
- Favicon → full ICO pack if needed (currently SVG only, sufficient for modern browsers).

## 12. Files Changed

| File | Change |
|---|---|
| `index.html` | Full rewrite — Editorial redesign replaces original blue/white layout |
| `og-preview.html` | New — source HTML for OG image generation |
| `og.png` | New — 1200×630 rendered OG image |
| `docs/superpowers/specs/2026-04-16-lazytech-portfolio-editorial-redesign-design.md` | This document |
