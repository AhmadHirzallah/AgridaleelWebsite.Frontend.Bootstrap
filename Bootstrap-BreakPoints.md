Here’s a compact “how to think about it” guide you can reuse anytime.

## 1. Bootstrap is **mobile-first**

Rules are written for **small screens first**, then **bigger** breakpoints **add or override** behavior.

Breakpoints (Bootstrap 5 defaults):

| Prefix | Min width                                                          |
| ------ | ------------------------------------------------------------------ |
| (none) | 0 — applies to **all** widths unless a larger breakpoint overrides |
| `sm`   | ≥576px                                                             |
| `md`   | ≥768px                                                             |
| `lg`   | ≥992px                                                             |
| `xl`   | ≥1200px                                                            |
| `xxl`  | ≥1400px                                                            |

So **`col-12`** with no prefix means: “from 0px upward, this column is 12/12 (full row width)” **until** another class at a **larger** breakpoint changes it.

---

## 2. What **`col-12 col-md-auto`** means

You read it **in order of viewport width**:

1. **`col-12`**
    - From **0px** up: column = **full width** (100% of the row).
    - That includes what people call “xs” and everything **below `md`** (so &lt;768px), and also includes **`sm`** (576–767px) unless you add something like `col-sm-6` in between.

2. **`col-md-auto`**
    - From **768px and up** (`md`, `lg`, `xl`, `xxl` all count): column width = **`auto`** — only as wide as its content (like “shrink-wrap”), not forced to 12/12.

So your understanding is **almost** right:

- **Below `md` (&lt;768px):** behaves like **`col-12`** (full row), so yes: extra-small and small (and anything under 768) use that unless you add `col-sm-*` etc.
- **`md` and above (≥768px):** **`col-md-auto`** applies and stays in effect for **`lg`**, **`xl`**, **`xxl`** too — **unless** you add another class like `col-lg-6` that overrides it from that breakpoint up.

There is no separate “xs” class in Bootstrap 5; the default / unprefixed classes are the “xs” behavior.

---

## 3. How this ties to what we did (mental recipe)

**Problem:** Two things side by side on a wide row → on a narrow screen the row doesn’t wrap and the page scrolls sideways.

**Recipe:**

1. **Row:** allow wrapping on small screens, one line on large:  
   `flex-wrap flex-md-nowrap` (or on Bootstrap’s grid row, pair columns with widths that stack).

2. **Columns:**
    - Small: **`col-12`** → each block is its own full row.
    - From `md`: **`col-md`** (grow) + **`col-md-auto`** (badges only as wide as needed) so you get side-by-side again.

3. **`min-w-0`** on flex children sometimes helps text not “blow out” the width (advanced tip).

---

## 4. Quick reference patterns

| Goal                                       | Typical classes      |
| ------------------------------------------ | -------------------- |
| Full width mobile, half from tablet        | `col-12 col-md-6`    |
| Full width mobile, natural width from `md` | `col-12 col-md-auto` |
| Full width until large, then 4 columns     | `col-12 col-lg-4`    |

---

## 5. How to verify yourself

1. Open DevTools → toggle device toolbar.
2. Drag width across **768px** (`md`).
3. Watch which column “snaps” from full width to auto/half.

Official docs: [Bootstrap Grid system](https://getbootstrap.com/docs/5.3/layout/grid/) and [Breakpoints](https://getbootstrap.com/docs/5.3/layout/breakpoints/).

If you want this applied to more sections step by step, say which block (e.g. nav, hero, footer); in **Agent mode** I can edit the file for you.
