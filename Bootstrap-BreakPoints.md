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

## 4. The “12 slices” mental model (read this once)

Bootstrap splits each **row** into **12 equal slices** (units), not inches or device names.

| Class fragment | Meaning |
| -------------- | ------- |
| `col-12` | This column uses **all 12** slices → **full width** of the row (one block per line). |
| `col-6` | Uses **6** slices → **half** the row. Two `col-6` columns fit **side by side** (6+6=12). |
| `col-4` | Uses **4** slices → **one third** of the row. Three `col-4` fit in one row (4+4+4=12). |
| `col-md-6`, `col-lg-4`, … | The **`md` / `lg`** part means: “**apply this slice count only from that breakpoint’s width upward**.” Below it, a **smaller** rule (often `col-12`) still applies. |
| `col-md-auto` | From **`md` up**: column is only as wide as its **content** (images, text…), not forced to a slice count. |

So **`col-12 col-md-6`** reads as: “**Always** full width **until** `md`; **from `md` onward**, use half the row.”

---

## 5. Pattern cheat sheet (what each combo **actually** does)

Use this when you pick classes for a column.

| What you want (plain English) | Typical classes | Below `md` (&lt;768px) unless you add `sm` rules | From the named breakpoint upward | Important detail |
| ----------------------------- | --------------- | ------------------------------------------------ | ---------------------------------- | ---------------- |
| Stack on phone, **two equal** columns from tablet | `col-12 col-md-6` | Full row (`12` slices) | Each column **6** slices → **half** row, **2** per row | Same from `md` through `xxl` until you add e.g. `col-lg-4`. |
| Stack on phone, **only as wide as content** from tablet (e.g. icons, store badges) | `col-12 col-md-auto` | Full row | **Shrink-wrapped** width (flex `0 0 auto` style behavior) | Good next to a `col-12 col-md` “main” column that grows. |
| Stack on phone + tablet, **three equal** columns only on large desktops | `col-12 col-lg-4` | Full row | **4** slices each → **three** columns per row (4+4+4=12) | The number **4** is slices out of 12 → **3** blocks per row, not “4 columns.” |
| Always one block per line | `col-12` | Full row | Full row (no larger override) | Simplest “mobile column.” |
| Main area grows, sidebar stays tight (footer-style) | `col-12 col-md` + `col-12 col-md-auto` | Both **stacked** full width | First grows, second stays **content-sized** | Often pair with `flex-wrap flex-md-nowrap` on the **row**. |

**Remember:** numbers are **out of 12**, not “number of columns” except when 12÷number tells you how many fit (e.g. 12÷4 = **3** widgets per row).

---

## 6. Real phones & tablets vs Bootstrap (why 768 / 992 feel random)

Bootstrap only cares about **CSS viewport width in pixels** (what you see in DevTools when you resize). It does **not** know “iPhone” or “Samsung.”

**Official cutoffs (Bootstrap 5)** — same as section 1:

| Bootstrap name | Activates at (min width) | Rough meaning |
| ---------------- | ------------------------- | ------------- |
| default (no prefix) | 0px | Your “base” styles — phones and everything up until a bigger rule overrides. |
| `sm` | ≥576px | Large phones / small phablets in **portrait**. |
| `md` | ≥768px | **Tablet portrait** and up; many iPads cross here. |
| `lg` | ≥992px | **Tablet landscape** / small laptop. |
| `xl` | ≥1200px | Laptops / desktops. |
| `xxl` | ≥1400px | Wide desktops. |

**Typical CSS widths (portrait, approximate)** — devices vary by model, zoom, and browser UI:

| Example device / situation | Approx. CSS width (portrait) | Usually lands in… |
| ---------------------------- | ----------------------------- | ------------------- |
| Small phone (e.g. many Android) | 360px | below `sm` |
| iPhone SE, common layouts | 375px | below `sm` |
| iPhone 12 / 13 / 14 | 390px | below `sm` |
| Large phones | 412–430px | below `sm` |
| “Phablet” / large phone | 540–600px | **`sm`** range (≥576) |
| iPad mini portrait | ~768px | **right on `md`** — test both sides! |
| iPad / tablet portrait | 768–834px | **`md` to `lg`** |
| Tablet landscape / small laptop | 1024–1280px | **`lg` / `xl`** |
| Desktop | 1280px+ | **`xl` / `xxl`** |

**Why this matters for you:** a viewport like **759×506** is **just under `md` (768px)** → only your **default** and **`sm`** rules apply; **`col-md-*`** has **not** kicked in yet. That is why `col-12 col-md-6` still looks “stacked” there.

**Landscape tip:** turning a phone sideways often jumps you across **`sm`** or **`md`** — always resize in DevTools to see your real breakpoints.

---

## 7. How to verify yourself

1. Open DevTools → toggle device toolbar.
2. Drag width across **768px** (`md`).
3. Watch which column “snaps” from full width to auto/half.

Official docs: [Bootstrap Grid system](https://getbootstrap.com/docs/5.3/layout/grid/) and [Breakpoints](https://getbootstrap.com/docs/5.3/layout/breakpoints/).

If you want this applied to more sections step by step, say which block (e.g. nav, hero, footer); in **Agent mode** I can edit the file for you.
