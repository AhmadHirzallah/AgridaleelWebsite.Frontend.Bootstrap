# Owl Carousel 2 — Site guide & reuse kit

This project uses **Owl Carousel 2.3.4** with a small, opinionated pattern (**`site-owl`**) so you can copy one block, wire IDs in JavaScript once, and control **layout** (dots on / under slides, vertical dots) and **look** (sizes, colors) mostly with **classes + CSS variables**—without pasting Owl’s *generated* DOM (`owl-stage`, `owl-item`, …) into HTML.

Official references (keep these handy):

- [Options](https://owlcarousel2.github.io/OwlCarousel2/docs/api-options.html)  
- [Classes](https://owlcarousel2.github.io/OwlCarousel2/docs/api-classes.html) (what Owl *outputs* after init)  
- [Events](https://owlcarousel2.github.io/OwlCarousel2/docs/api-events.html) (`trigger`, `on`, callbacks)

---

## Table of contents

1. [What you write vs what Owl creates](#1-what-you-write-vs-what-owl-creates)  
2. [The `site-owl` HTML contract](#2-the-site-owl-html-contract)  
3. [JavaScript checklist (every new carousel)](#3-javascript-checklist-every-new-carousel)  
4. [CSS variables (appearance)](#4-css-variables-appearance)  
5. [Layout modifiers (position & direction)](#5-layout-modifiers-position--direction)  
6. [Recipes](#6-recipes)  
7. [Common mistakes](#7-common-mistakes)  
8. [Optional: full custom dots (advanced)](#8-optional-full-custom-dots-advanced)

---

## 1. What you write vs what Owl creates

| You write (static HTML) | Owl creates at runtime (do **not** copy into HTML) |
|-------------------------|-----------------------------------------------------|
| One root: `.owl-carousel` + direct children (e.g. `.item` > `img`) | `.owl-stage-outer` > `.owl-stage` > `.owl-item` … |
| Optional: empty host `#…-dots` + `dotsContainer` in JS | `<button class="owl-dot"><span></span></button>` × *n* |
| Optional: your own prev/next `<button>`s | Default `.owl-nav` (if `nav: true`) |

Owl **counts** slides and **builds** dot buttons from an internal template (see `Navigation.prototype.initialize` / `draw` in `css/OwlCarousel2-2.3.4/dist/owl.carousel.js`). Your file only supplies the **container** and **IDs** for wiring.

---

## 2. The `site-owl` HTML contract

**Block root** — must include **`owl-theme`** if dots (or dot styling that uses `.owl-theme .owl-dots …`) live **outside** the `.owl-carousel` node. In this kit, dots sit next to the carousel inside `.site-owl__slides`, so theme rules still apply cleanly.

```html
<div
  class="site-owl site-owl--dots-bottom owl-theme"
  data-site-owl="hero"
>
  <section class="site-owl__slides" aria-label="…">
    <div id="site-owl-hero" class="owl-carousel site-owl__carousel">
      <div class="item">…</div>
    </div>

    <section class="site-owl__dots" aria-label="…">
      <div id="site-owl-hero-dots" class="owl-dots site-owl__dots-host"></div>
    </section>
  </section>

  <section class="site-owl__arrows" aria-label="…">
    <button type="button" id="site-owl-hero-prev" class="site-owl__arrow">…</button>
    <button type="button" id="site-owl-hero-next" class="site-owl__arrow">…</button>
  </section>
</div>
```

**BEM summary**

| Class | Role |
|-------|------|
| `site-owl` | Block; add **modifiers** here (`site-owl--dots-overlay`, …) |
| `site-owl__slides` | Slide area + **dots host** (so overlay positions relative to images) |
| `site-owl__carousel` | The node passed to `$(…).owlCarousel({…})` (keep `owl-carousel`) |
| `site-owl__dots` | Wrapper around the **empty** dots host |
| `site-owl__dots-host` | Empty div; Owl fills it; keep `owl-dots` for compatibility |
| `site-owl__arrows` | Your prev/next row |
| `site-owl__arrow` / `site-owl__arrow-icon` | Button + inner icon styling |

**Naming a second carousel** — duplicate the block and use a new suffix everywhere: `site-owl-news`, `#site-owl-news`, `#site-owl-news-dots`, `#site-owl-news-prev`, etc.

---

## 3. JavaScript checklist (every new carousel)

1. **jQuery** then **`owl.carousel.min.js`** then your inline init.  
2. **Slides only** inside `.owl-carousel` (no hand-written `owl-item`).  
3. **`owlCarousel({ … })`** with at least:

| Option | Typical value | Notes |
|--------|---------------|--------|
| `items` | inside `responsive: { 0: { items: 1 } }` | or top-level `items` |
| `loop` | `true` / `false` | |
| `rtl` | `true` for Arabic `dir="rtl"` | |
| `nav` | `false` if you use **custom** arrows | |
| `dots` | `true` | |
| `dotsContainer` | `"#site-owl-hero-dots"` | **Must** match the empty host `id` |
| `autoplay` | optional | + `autoplayTimeout`, `autoplayHoverPause` |

4. **Wire custom arrows** (if `nav: false`):

```js
$("#site-owl-hero-prev").on("click", function () {
  $("#site-owl-hero").trigger("prev.owl.carousel", [600]);
});
```

5. **Programmatic control** — same carousel jQuery object:  
   `.trigger('next.owl.carousel')`, `.trigger('to.owl.carousel', [index, speed])` — see [Events](https://owlcarousel2.github.io/OwlCarousel2/docs/api-events.html).

---

## 4. CSS variables (appearance)

Set on **`.site-owl`** (or a modifier-specific copy) in `css/style.css` or a page scope:

| Variable | Default (this project) | Effect |
|----------|------------------------|--------|
| `--site-owl-slide-bg` | `#fff` | Slide strip background |
| `--site-owl-slide-min-height` | `220px` | Min height for slide images (`object-fit: cover`) |
| `--site-owl-dot-size` | `10px` | Dot diameter (the `span` inside `button.owl-dot`) |
| `--site-owl-dot-gap` | `14px` | Horizontal spacing between dots |
| `--site-owl-dot-bg` | `#d6d6d6` | Inactive dot |
| `--site-owl-dot-bg-active` | `var(--brand-main)` | Active / hover |
| `--site-owl-dot-radius` | `30px` | Pill / circle |
| `--site-owl-dots-offset` | `0.75rem` | Distance from bottom when **overlay** |
| `--site-owl-arrow-size` | `1.25rem` | Icon scale for custom arrows |

**Example — larger dots on one instance**

```html
<div class="site-owl site-owl--dots-bottom owl-theme site-owl--promo">
```

```css
.site-owl--promo {
  --site-owl-dot-size: 14px;
  --site-owl-dot-gap: 18px;
}
```

---

## 5. Layout modifiers (position & direction)

Add modifiers on the **same element** as `site-owl` (the block root).

| Modifier | Effect |
|----------|--------|
| `site-owl--dots-bottom` | **Default** — dots under the carousel, in normal flow (inside the white slide strip). |
| `site-owl--dots-overlay` | Dots **on top of** the slide: `position: absolute` at the bottom of `.site-owl__slides`. Requires dots to stay **inside** `.site-owl__slides` (as in this repo). |
| `site-owl--dots-vertical` | Stacks dots vertically (`flex-direction: column` on the host). Combine with `--dots-overlay` if you want a side / corner stack on the image. |

**Switch layout**

```html
<!-- Under slides -->
<div class="site-owl site-owl--dots-bottom owl-theme">

<!-- On slides -->
<div class="site-owl site-owl--dots-overlay owl-theme">

<!-- On slides + vertical stack -->
<div class="site-owl site-owl--dots-overlay site-owl--dots-vertical owl-theme">
```

Then adjust `--site-owl-dots-offset` / flex alignment in your own CSS if you need dots **left**, **right**, or **center** (e.g. `text-align` on `.site-owl__dots`, or `align-items` on overlay flex).

---

## 6. Recipes

### A. Second carousel on another page

1. Copy the `site-owl` HTML block.  
2. Rename **all** ids: carousel, dots, prev, next.  
3. Duplicate the init block (or factor a small function `initSiteOwl(suffix)`).  
4. Point `dotsContainer` at the new dots `id`.

### B. Dots under slides (default)

- Classes: `site-owl site-owl--dots-bottom owl-theme`  
- No extra positioning.

### C. Dots on the image (overlay)

- Add **`site-owl--dots-overlay`** on the block root.  
- Keep dots **inside** `.site-owl__slides` (already required in this kit).  
- Tune `--site-owl-dots-offset`.

### D. Vertical dots

- Add **`site-owl--dots-vertical`**.  
- Often combined with overlay; use extra CSS for `inset-inline-end` vs `start` if you want a side rail.

### E. Built-in Owl nav instead of custom arrows

Set `nav: true`, remove `site-owl__arrows` section, remove click handlers, optionally set `navText: ['…','…']` **or** style `.owl-nav` under `.site-owl__carousel` — note `navText` is HTML **strings** in JS (see official docs).

---

## 7. Common mistakes

1. **Pasting Owl’s post-init HTML** (`owl-loaded`, `owl-stage`, …) into the file — breaks init.  
2. **`dotsContainer` id typo** — dots never fill or clicks do nothing.  
3. **No `owl-theme` ancestor of external dots** — dots look like ugly native buttons; theme targets `.owl-theme .owl-dots …`.  
4. **Overlay dots outside `.site-owl__slides`** — `position: absolute` anchors to the wrong box.  
5. **Destroying the carousel** — with `dotsContainer`, read Owl’s `destroy` behavior (external nodes may be removed); for static marketing sites this rarely matters.

---

## 8. Optional: full custom dots (advanced)

If you need **each dot written by you** in HTML:

- Set **`dots: false`**.  
- Build your own indicators and on `click` call `$('#carousel').trigger('to.owl.carousel', [slideIndex, speed])`.  
- Listen to **`changed.owl.carousel`** to sync the active state.

Only worth it when marketing/design demands fixed markup; otherwise **`dotsContainer` + CSS variables** is simpler and stays in sync with slide count.

---

## File map (this repo)

| File | Role |
|------|------|
| `js/jquery.min.js` | Dependency |
| `js/owl.carousel.min.js` | Owl core + plugins (incl. autoplay) |
| `css/owl.carousel.min.css` | Core layout (stage, items) |
| `css/owl.theme.default.min.css` | Default dot / nav skin (partially mirrored in `style.css`) |
| `css/style.css` | **`site-owl`** tokens + modifiers |
| `index.html` | Reference instance **`site-owl-hero`** |
| **`Carousel.html`** | **Standalone starter page** — minimal markup + init; copy the file (and assets above) into the next project |

---

*Last aligned with project structure: hero header carousel (`#site-owl-hero`).*
