# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this is

A single-page marketing site for **Cheam Village Dental Studios**, a boutique
private dental practice in Cheam Village, Surrey. It's a hand-authored,
animation-rich static landing page deployed via GitHub Pages. There is **no
build step, no framework, and no package manager** — everything lives in one
`index.html`.

## Repository layout

```
index.html      # The entire site: markup + <style> + <script>, ~1700 lines
README.md       # Short project overview + how to run
images/         # Local JPEGs (hero slides, before/after gallery, founders)
```

There is no `package.json`, no `.github/` workflows, no linter/formatter
config, and no tests. Do not invent a build pipeline unless explicitly asked.

## Running locally

It's a static file — open `index.html` directly, or serve the folder so
relative paths and lazy-loading behave:

```sh
npx serve .
```

## Architecture of `index.html`

The file is a single document with three parts, in order:

1. **`<style>`** (top of file, ~line 11 onward) — all CSS, organised into
   clearly delimited banner-comment blocks:
   `/* ============ TOKENS ============ */`,
   `HERO`, `SERVICES`, `STATEMENT`, `PROCESS`, `SMILE GALLERY`,
   `TESTIMONIALS`, `VISIT`, `RESPONSIVE`, `reduced motion`, etc.
2. **`<body>` markup** — semantic sections, each preceded by an HTML banner
   comment (`<!-- ============ HERO ============ -->`). Section order:
   preloader → nav/header → mobile menu → `main` (hero, marquee, intro/studio,
   services/treatments, statement/approach, process, smile gallery,
   testimonials/voices, visit/CTA + footer) → floating Google reviews badge →
   persistent contact FAB (WhatsApp + live chat).
3. **`<script>`** (bottom, after the CDN library tags) — all behaviour in one
   IIFE.

### Page sections and their anchor IDs

Nav links jump to these in-page anchors:

| Section          | `id`         |
|------------------|--------------|
| Intro / studio   | `studio`     |
| Services         | `treatments` |
| Statement        | `approach`   |
| Smile gallery    | `gallery`    |
| Testimonials     | `voices`     |
| Visit / contact  | `visit`      |

## Design system

All colours, fonts, and easing live as CSS custom properties under
`/* ============ TOKENS ============ */` in `:root`. **Always reference and
extend these tokens rather than hard-coding values.**

- Palette (brand-matched): `--porcelain` warm ivory (never pure white),
  `--porcelain-deep` cream cards, `--ink` darkened brand teal (near-black),
  `--sage` deep teal accent, `--sage-soft`, `--earth` warm wood,
  `--gold` brass, `--coral` coral highlight.
- Type: `--serif` = Cormorant Garamond, `--sans` = Montserrat (loaded from
  Google Fonts). Use the `.serif` helper class to switch a node to the serif.
- Motion: `--ease` is the shared cubic-bezier; `--nav-h` is the header height.
- A fixed SVG film-grain overlay is applied via `body::after`.

## Animation system

Third-party libraries are loaded from CDNs (no local copy, no bundler):
**GSAP 3.12.5**, **ScrollTrigger**, and **Lenis 1.1.14** smooth scroll.

Animations are driven declaratively by `data-` attributes that the script
scans with `gsap.utils.toArray(...)`. When adding animated content, reuse
these hooks instead of writing bespoke tweens:

- `data-fade` — fade + rise in on scroll
- `data-lines` — line-by-line masked text reveal (JS wraps an inner element)
- `data-parallax` — gentle parallax on images
- `data-rotate="<deg>"` — rotate-in
- `data-count="<n>"` / `data-decimals="<n>"` — animated number counter
- `data-img="images/..."` on `.service-row` — cursor-following service image

### Conventions to preserve

- **Accessibility / reduced motion is mandatory.** The script reads
  `window.matchMedia('(prefers-reduced-motion: reduce)')` into a `reduced`
  flag and there's a `@media(prefers-reduced-motion:reduce)` CSS block. Any
  new motion must degrade gracefully when `reduced` is true, and interactive
  widgets must keep their `aria-*` attributes (`aria-hidden`, `aria-expanded`,
  `role="tablist"`, `aria-label`, etc.) in sync.
- **Fail-safe scripting.** The IIFE bails early if GSAP is absent
  (`if(!window.gsap){ ... return; }`) and hides the preloader so the page is
  always usable without JS animation.
- The script targets older syntax (`var`, `function(){}`) for broad
  compatibility — match the surrounding style rather than introducing ES
  modules or arrow-function-heavy rewrites.

## Images & content

- Photography in `images/` is brand-matched but **placeholder** (originally
  Unsplash); contact details, phone numbers, ratings, and review counts are
  **placeholders** too. Don't present them as real business data.
- Reference images with relative paths (`images/foo.jpg`), add descriptive
  `alt` text, and use `loading="lazy"` on below-the-fold images (the first
  hero slide is eager; later slides are lazy).

## Editing conventions

- Keep everything in the single `index.html`; preserve the banner-comment
  structure (`/* ===== NAME ===== */` in CSS, `<!-- ===== NAME ===== -->` in
  markup) when adding a section so the file stays navigable.
- Match the existing two-space indentation and the compact, token-driven CSS
  style already in the file.
- Edit the `<style>`/`<script>` blocks in place — do not extract CSS/JS into
  separate files unless the user asks.

## Git & deployment workflow

- Active development branch for this work: `claude/claude-md-docs-lto4oj`.
  Create it locally if missing; never push to a different branch without
  explicit permission.
- Commit messages in history are short, imperative, and descriptive
  (e.g. "Use real husband-and-wife team photo in founders section",
  "Match colours to Cheam Village brand guidelines"). Match that style.
- The site is published with **GitHub Pages** from the repo (the live URL is
  in the repo sidebar). Pushing to the deployed branch updates the live site,
  so treat changes to `index.html` as production-facing.
- Push with `git push -u origin <branch-name>`. Do **not** open a pull
  request unless the user explicitly asks.
