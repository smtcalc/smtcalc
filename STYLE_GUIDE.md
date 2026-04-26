# SMTCalc.com -- Design System & Style Guide

This document is the single source of truth for building and maintaining tools on smtcalc.com.
Cross-check against `stencil-architect/index.html` (canonical tool reference) and `style.css`
(shared stylesheet) when in doubt.

---

## 1. Site Structure

All tools are self-contained HTML files in their own subdirectory. Styles shared across all pages
live in `/style.css` at the project root. Each tool page links to it and adds only its own
page-specific styles in an inline `<style>` block.

```
smtcalc.com/                               -> landing page
smtcalc.com/about/                         -> about page
smtcalc.com/stencil-architect/            -> Stencil Architect (canonical tool reference)
smtcalc.com/smt-capacity-calculator/      -> SMT Capacity Calculator
smtcalc.com/msl-bake-calculator/          -> MSL Bake Calculator
smtcalc.com/reflow-throughput-calculator/ -> Reflow Throughput Calculator
smtcalc.com/placement-speed-verifier/     -> Placement Speed Verifier
smtcalc.com/msl-tracker/                  -> MSL Tracker (Excel download page)
smtcalc.com/resistor-code-calculator/     -> Resistor Code Calculator
smtcalc.com/solder-alloy-reference/       -> Solder Alloy Reference
smtcalc.com/flux-designator-decoder/      -> Flux Designator Decoder
smtcalc.com/ipc-standards-finder/         -> IPC Standards Finder
smtcalc.com/smt-line-capacity-planner/    -> SMT Line Capacity Planner
smtcalc.com/bare-board-bake-calculator/   -> Bare Board Bake Calculator
```

Every new tool gets its own subdirectory and `index.html`.
Project working filename convention: `[tool-slug]-index.html`.

### Sitemap

Always deliver an updated `sitemap.xml` with any new tool. Use today's date as `lastmod`
on all entries. Priority: 1.0 for `/`, 0.6 for `/about/`, 0.8 for all tools.
Changefreq: weekly for `/`, monthly for all others.

---

## 2. Encoding Rules (Critical)

All HTML and JS must be 100% ASCII-clean (7-bit, codepoint 127 max).

Never use:
- Em dashes (use comma or period instead)
- Smart/curly quotes
- Unicode checkmarks or crosses (use `&#10003;` or `&#10007;`)
- Unicode minus signs (use plain hyphen)
- Any character above codepoint 127

Common safe alternatives:

| Intended character | Use instead      |
|--------------------|------------------|
| Em dash            | Comma or period  |
| Right arrow        | `&#8594;`        |
| Checkmark          | `&#10003;`       |
| Cross              | `&#10007;`       |
| Degree             | `&#176;`         |
| Micro              | `&#181;`         |
| Copyright          | `&copy;`         |

---

## 3. Shared Stylesheet (style.css)

`/style.css` at the project root is loaded by every page. It provides:

- CSS custom properties (design tokens)
- Reset and body base styles
- Header, logo, nav, tool-badge
- Tool hero section
- Guide banner
- Main tool grid (.tool-main)
- Panels (.panel, .panel-full, .panel-label, .panel-header)
- Form fields (.field, .input-wrap, .input-unit, .field-note)
- Stat boxes (.stat-box, .stats-grid)
- Verdict colors (.pass, .warn, .fail)
- Alert boxes (.warn-box, .info-box, .results-empty)
- Print bar (.print-bar, .print-btn)
- Accordion (.accordion, .accordion-item, .accordion-header, .accordion-content)
- Footer
- Responsive breakpoints for shared components
- Base `@media print` rule (hiding header, nav, guide-banner, print-bar)

Each tool page adds ONLY its page-specific styles in an inline `<style>` block.
Each tool page keeps its own `@media print` block inline to hide screen-only elements.
No `:root` color override is needed in print -- the site is already light-themed.

Link in every page `<head>` immediately after the favicon:

```html
<link rel="icon" type="image/svg+xml" href="/favicon.svg">
...
<link rel="stylesheet" href="/style.css">
```

No separate Google Fonts `<link>` tags are needed. Fonts are loaded via `@import` inside style.css.

---

## 4. Color System

The site uses a light theme ("Technical Green") with a near-white background and forest
green accent. All tokens are defined in style.css.

```css
:root {
  --bg:           #f7faf6;                  /* Page background, near-white with green tint */
  --bg2:          #f0f4f0;                  /* Section backgrounds, panels */
  --bg3:          #e6ece7;                  /* Input fields, stat boxes */
  --bg4:          #dbe4dc;                  /* Active states, unit badges */
  --bg5:          #cfd9d1;                  /* Hover states, deepest inset */
  --accent:       #186640;                  /* Forest green: primary accent */
  --accent-hover: #0f5533;                  /* Darker green: hover state */
  --accent2:      #b03333;                  /* Red: destructive / error */
  --text:         #1c2a1f;                  /* Primary text, near-black green */
  --muted:        #4a5e50;                  /* Secondary text, labels */
  --border:       rgba(28,42,31,0.12);      /* Primary border */
  --border2:      rgba(28,42,31,0.22);      /* Secondary border */
  --green:        #1a9e55;                  /* PASS / OK verdict */
  --yellow:       #b06a10;                  /* WARNING, amber */
  --red:          #b03333;                  /* FAIL verdict (same as --accent2) */
  --mono:         'Share Tech Mono', monospace;
}
```

**Verdict color logic:**

| State     | Color          | Variable  |
|-----------|----------------|-----------|
| PASS / OK | #1a9e55 green  | --green   |
| MARGINAL  | #b06a10 amber  | --yellow  |
| FAIL      | #b03333 red    | --red     |

**rgba reference (for transparency expressions in page-specific CSS):**

| Token    | Hex     | RGB base              |
|----------|---------|-----------------------|
| --accent | #186640 | rgba(24,102,64,...)   |
| --green  | #1a9e55 | rgba(26,158,85,...)   |
| --yellow | #b06a10 | rgba(176,106,16,...)  |
| --red    | #b03333 | rgba(176,51,51,...)   |

**Navigation header (hardcoded in style.css, not CSS variables):**
The header is dark on all pages regardless of the light page theme.
- Header background: rgba(22,42,28,0.97), border-bottom: rgba(62,207,122,0.15)
- Logo "SMT" text: color #f0f4f0 (hardcoded -- NOT var(--text), which is dark)
- Logo "Calc" span: color #3ecf7a
- Nav links: color #c8d8cc, hover color #3ecf7a
- Tool badge: color #8aad96, border rgba(62,207,122,0.22)

**Print reports use independent hardcoded colors.** The `printReport()` JS builds its own
HTML with these fixed values -- do not change them:
- Background: #ffffff
- Accent / labels: #d4720a
- PASS: #167a40
- FAIL: #c42b08

**Semantic rgba tint usage:**
- Info / accent context: rgba(24,102,64,...) -- green tint
- Warning context: rgba(176,106,16,...) -- amber tint
- Error / fail context: rgba(176,51,51,...) -- red tint

---

## 5. Typography

Fonts are loaded by style.css via `@import`:

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=Manrope:wght@600;700;800&family=Share+Tech+Mono&display=swap');
```

**Font roles:**

| Family          | Weight(s) | Use case                                       |
|-----------------|-----------|------------------------------------------------|
| Manrope         | 800       | Tool hero h1, landing page h1                  |
| Inter           | 800-900   | Section headings                               |
| Inter           | 600-700   | Panel labels, eyebrows, button text, nav       |
| Inter           | 400-500   | Body text, field notes, guide prose            |
| Share Tech Mono | 400       | Input values, units, stat values, formula text |

**Base body rule (from style.css):**

```css
body {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}
```

**Font size reference:**

| Role                         | Size       |
|------------------------------|------------|
| Tool hero h1                 | clamp(2rem, 5vw, 3.25rem) |
| Section h2                   | 1.25-1.5rem |
| Panel label                  | 0.78rem    |
| Hero eyebrow                 | 0.85rem    |
| Field label                  | 0.85rem    |
| Body / guide text            | 0.97-1.1rem |
| Field notes                  | 0.88rem    |
| Input values (mono)          | 1rem       |
| Stat values (mono)           | 1.6rem     |
| Stat labels                  | 0.75rem    |
| Input unit badge (mono)      | 0.85rem    |
| Print button / nav           | 0.9-1rem   |

---

## 6. Header

Fixed, 80px tall. Defined entirely in style.css.

```html
<header>
  <a href="/" class="logo">SMT<span>Calc</span></a>
  <div class="tool-badge">Tool Name Here</div>
</header>
```

Logo renders as: **SMT** in color #f0f4f0 (hardcoded light, visible on dark nav), **Calc** in #3ecf7a.
On the landing page, `<div class="tool-badge">` is replaced by `<nav>`.

Key values from style.css:
- Height: 80px
- Background: rgba(22,42,28,0.97) with backdrop-filter: blur(10px)
- Logo: Inter 900, 1.75rem, letter-spacing -0.02em, color #f0f4f0 (hardcoded)
- Tool badge: Inter 600, 0.78rem, letter-spacing 0.06em, color #8aad96

---

## 7. Tool Hero Section

Banner below the header. `--bg2` background, no decorative glows.
Top padding 120px clears the fixed 80px header with breathing room.

```html
<div class="tool-hero">
  <div class="tool-hero-inner">
    <div class="hero-eyebrow">Category / Subcategory</div>
    <h1>Tool Name</h1>
    <p>One sentence describing what the tool does and who it is for.</p>
  </div>
</div>
```

**Critical rules:**
- The outer element is always `<div class="tool-hero">`. Never use `.page-hero`, `.hero`, or any other class name.
- The inner wrapper is always `<div class="tool-hero-inner">`. Never use `.hero-inner`, `.page-hero-inner`, or similar.
- The eyebrow is always `<div class="hero-eyebrow">`. Never use `.tool-eyebrow`, `.eyebrow`, or similar.
- Do not add any inline CSS that overrides the hero's h1 font, size, color, or text-transform. All styling comes from style.css.
- Do not add decorative pseudo-elements (glows, radial gradients, lines) to the hero.

Key values from style.css:
- Padding: 120px 2rem 60px
- Max-width inner: 960px
- h1: Manrope 800, clamp(2rem, 5vw, 3.25rem), line-height 1.1, letter-spacing -0.02em, color --text
- Eyebrow: Inter 600, 0.85rem, letter-spacing 0.1em, uppercase, color --accent
- Description p: 1.1rem, --muted, max-width 700px, line-height 1.7

**Eyebrow category labels:**

| Tool                      | Eyebrow label                            |
|---------------------------|------------------------------------------|
| Stencil Architect         | Stencil / Paste Process                  |
| SMT Capacity Calculator   | Production / Planning                    |
| MSL Bake Calculator       | Component Handling / Moisture Management |
| Reflow Throughput Calc    | Reflow / Thermal Process                 |
| Placement Speed Verifier  | Pick and Place / Throughput              |
| MSL Tracker               | Component Handling / Moisture Management |
| Resistor Code Calculator  | Components / Passive Identification      |
| Solder Alloy Reference    | Materials / Solder Alloys                |
| Flux Designator Decoder   | Materials / Flux Chemistry               |
| IPC Standards Finder      | Standards / Reference                    |
| Bare Board Bake Calc      | PCB Handling / Moisture                  |
| SMT Line Capacity Planner | Production / Planning                    |

New tools: two or three words, slash-separated, Title Case.

---

## 8. Guide Banner

Shown between hero and tool grid when a guide exists. Defined in style.css.

```html
<div class="guide-banner">
  <span>New to aperture design? Read the guide covering area ratio, aspect ratio, and paste release.</span>
  <a href="GUIDE_URL">Read the Guide &#8594;</a>
</div>
```

Background: rgba(24,102,64,0.07), bottom border: rgba(24,102,64,0.18).
Omit the guide banner entirely if no guide exists yet.

---

## 9. Main Tool Grid

Two-column grid. Panels are flat tiles inside a framed container -- NOT floating rounded
cards. The style.css defaults for `.tool-main` and `.panel` do not match the canonical
look. **Every tool page must override these in its inline `<style>` block.**

```html
<div class="tool-main">

  <div class="panel">
    <div class="panel-label">Panel Title</div>
    <!-- content -->
  </div>

  <div class="panel">
    <div class="panel-label">Panel Title</div>
    <!-- content -->
  </div>

  <div class="panel panel-full">
    <div class="panel-label">Full Width Panel</div>
    <!-- content -->
  </div>

</div>
```

**Required page-specific CSS overrides (copy into every tool's inline `<style>`):**

```css
.tool-main {
  max-width: 960px;
  margin: 2rem auto;
  padding: 2rem;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5px;
  background: var(--border);
  border: 1px solid var(--border);
}
@media (max-width: 680px) {
  .tool-main { grid-template-columns: 1fr; margin: 1rem; padding: 0; }
}

.panel {
  background: var(--bg2);
  padding: 1.75rem;
}

.panel-full {
  grid-column: 1 / -1;
  background: var(--bg2);
  padding: 1.75rem;
}

.panel-label {
  font-family: 'Inter', sans-serif;
  font-weight: 700;
  font-size: 0.78rem;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: var(--accent);
  margin-bottom: 1.25rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
.panel-label::before {
  content: '';
  display: block;
  width: 16px;
  height: 2px;
  background: var(--accent);
}
```

**Key rules:**
- `gap: 1.5px` with `background: var(--border)` creates hairline separators between panels.
  The outer `border: 1px solid var(--border)` frames the entire grid.
- Panels have no individual border and no border-radius. Flat tiles only.
- `panel-label` uses a `<div>`, not a `<span>`. It renders with a `::before` green bar
  instead of a bottom border. Do not add a border-bottom to panel-label.
- Mobile breakpoint for this pattern is **680px**, not 768px.
- The style.css `.panel` (border-radius 8px, individual border) is intentionally NOT used
  for tool panels. It exists for other contexts only.

---

## 10. Form Fields

### Number / Text Input with Unit

```html
<div class="field">
  <label for="fieldId">Field Name</label>
  <div class="input-wrap">
    <input type="number" id="fieldId" min="0" step="0.01" placeholder="0.00" oninput="calculate()">
    <span class="input-unit">mm</span>
  </div>
  <div class="field-note">Helper text with valid ranges or typical values.</div>
</div>
```

Key values from style.css:
- Label: Inter 600, 0.85rem, --muted
- input-wrap: flex row, background --bg3, border 1px --border, border-radius 4px
- input-wrap:focus-within: border-color --accent
- Input: font-family --mono, 1rem, background transparent
- Unit badge: --mono 0.85rem, --muted, background --bg4, left border 1px --border

### Select Dropdown

```html
<div class="field">
  <label for="selectId">Label</label>
  <div class="input-wrap">
    <select id="selectId" onchange="calculate()">
      <option value="a">Option A</option>
    </select>
  </div>
</div>
```

Select uses Inter (not mono) per style.css: `font-family: 'Inter', sans-serif`.

### Toggle Button Group

Used for mutually exclusive options. Page-specific style (not in style.css).

```html
<div class="shape-toggle">
  <button id="btn-a" class="active" onclick="setOption('a')">Option A</button>
  <button id="btn-b" onclick="setOption('b')">Option B</button>
</div>
```

```css
.shape-toggle {
  display: flex;
  border: 1px solid var(--border);
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 1.25rem;
}
.shape-toggle button {
  flex: 1;
  background: var(--bg3);
  border: none;
  border-right: 1px solid var(--border);
  color: var(--muted);
  font-family: 'Inter', sans-serif;
  font-weight: 600;
  font-size: 0.9rem;
  padding: 0.6rem;
  cursor: pointer;
  transition: background 0.15s, color 0.15s;
}
.shape-toggle button:last-child { border-right: none; }
.shape-toggle button.active {
  background: var(--accent);
  color: #fff;
}
```

---

## 11. Feedback / Alert Boxes

Defined in style.css. Use CSS variables, not hardcoded rgba.

### Warning Box

```html
<div class="warn-box">
  <strong>Warning:</strong> Explanatory text about the issue.
</div>
```

Background: rgba(176,51,51,0.08), border: 1px rgba(176,51,51,0.28), border-radius 6px.

### Info Box

```html
<div class="info-box">
  Informational or guidance text.
</div>
```

Background: rgba(24,102,64,0.08), border: 1px rgba(24,102,64,0.22), border-radius 6px.

### Empty State

```html
<div class="results-empty">Enter values above to calculate.</div>
```

---

## 12. Results Area

### Stat Boxes

```html
<div class="stats-grid">
  <div class="stat-box">
    <div class="stat-label">Area Ratio</div>
    <div class="stat-value">0.74<span class="stat-unit"> --</span></div>
  </div>
</div>
```

Key values from style.css:
- stats-grid: auto-fit, minmax(150px, 1fr), gap 1rem
- stat-box: background --bg3, border 1px --border, border-radius 6px, padding 1.25rem 1.5rem
- stat-label: Inter 700, 0.75rem, uppercase, letter-spacing 0.1em, --muted
- stat-value: Share Tech Mono, 1.6rem, --text
- stat-unit: Share Tech Mono, 0.85rem, --muted

### Verdict Colors

Applied as classes on result elements. Defined in style.css with `!important`.

```html
<div class="stat-value pass">0.74</div>   <!-- green -->
<div class="stat-value warn">0.63</div>   <!-- orange -->
<div class="stat-value fail">0.48</div>   <!-- red -->
```

---

## 13. Print Report

### Print Bar

Shown below the main tool grid. Defined in style.css.

```html
<div class="print-bar">
  <label>Report Title</label>
  <input type="text" id="reportTitle" placeholder="e.g. Product X evaluation">
  <button class="print-btn" onclick="printReport()">&#9113; Print Report</button>
</div>
```

Key values from style.css:
- print-btn: background --accent (#186640), color #fff, Inter 600, border-radius 4px
- Hover: background --accent-hover (#0f5533)

### printReport() function

The function builds a new window with a light-theme print layout. These hardcoded colors
in the JS must not be changed -- print is always light theme:
- Background: #ffffff
- Accent / section labels: #d4720a
- PASS verdict: #167a40
- FAIL verdict: #c42b08

Fonts in the print window: loaded fresh from Google Fonts (Barlow Condensed, Barlow,
Share Tech Mono). These are print-only and separate from the site fonts.

### Page print hide rules

Each tool page keeps its own `@media print` block inline (not in style.css).
The site is already light-themed, so no `:root` color override is needed for print.
The block only needs to hide screen-only elements:

```css
@media print {
  header, .print-bar, .guide-toggle-btn, .guide-content { display: none !important; }
  /* Add any other page-specific screen-only elements here */
}
```

style.css also hides header, .guide-banner, .print-bar, and nav globally in print.

---

## 14. Accordion (Guide Sections)

Used for inline collapsible guide content on tool pages. Defined in style.css.

```html
<div class="accordion">
  <div class="accordion-item">
    <button class="accordion-header" onclick="toggleAccordion(this)">
      Section Title
      <svg class="accordion-chevron" width="16" height="16" viewBox="0 0 24 24"
        fill="none" stroke="currentColor" stroke-width="2">
        <polyline points="6 9 12 15 18 9"/>
      </svg>
    </button>
    <div class="accordion-content">
      <p>Guide content here.</p>
    </div>
  </div>
</div>
```

```javascript
function toggleAccordion(btn) {
  var item = btn.parentElement;
  var content = item.querySelector('.accordion-content');
  var isOpen = item.classList.contains('open');
  item.classList.toggle('open', !isOpen);
  content.classList.toggle('open', !isOpen);
}
```

---

## 15. Footer

Defined in style.css. Every page uses the same markup with JS-obfuscated contact email.

```html
<footer>
  <div class="footer-inner">
    <div class="footer-copy">
      &copy; 2026 <a href="/">SMTCalc.com</a> -- Free tools for electronics manufacturing professionals.
      <span class="footer-sep">|</span>
      <a id="contact-link" href="#" onclick="showContact(); return false;">Contact the author</a>
    </div>
  </div>
</footer>

<script>
function showContact() {
  var u = 'contact';
  var d = 'smtcalc.com';
  var a = document.getElementById('contact-link');
  a.href = 'mailto:' + u + '@' + d;
  a.textContent = u + '@' + d;
  a.onclick = null;
}
</script>
```

---

## 16. Meta Tags (per-tool template)

```html
<head>
  <link rel="icon" type="image/svg+xml" href="/favicon.svg">
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Tool Name] - SMTCalc</title>
  <meta name="description" content="[One sentence. Free SMT tool. What it calculates. Standard reference if applicable.]">
  <meta name="robots" content="index, follow">
  <link rel="canonical" href="https://smtcalc.com/[tool-slug]/">
  <link rel="stylesheet" href="/style.css">
  <style>
    /* Page-specific styles only */
  </style>
</head>
```

Title format: `[Tool Name] - SMTCalc` (plain hyphen, no special characters).
No separate Google Fonts link needed -- style.css imports Inter and Share Tech Mono.

---

## 17. New Tool Checklist

Deliver three files for any new tool:
1. `[tool-slug]-index.html` -- the tool itself
2. Updated `index.html` -- new card on the landing page
3. Updated `sitemap.xml` -- new URL, today's date as lastmod on all entries

Tool file checklist:
- [ ] Title format: `[Tool Name] - SMTCalc`
- [ ] Correct meta description (one sentence, plain language)
- [ ] Favicon link: `/favicon.svg`
- [ ] `<link rel="stylesheet" href="/style.css">` -- no separate font links
- [ ] No inline `:root` block (tokens come from style.css)
- [ ] Header with logo linking to `/` and `.tool-badge`
- [ ] Tool hero: `.tool-hero` > `.tool-hero-inner` > `.hero-eyebrow` + `h1` + `p`
- [ ] Guide banner if a guide URL is available
- [ ] `.tool-main`, `.panel`, `.panel-full`, `.panel-label` overridden in inline `<style>` per Section 9 (flat tile pattern, NOT style.css defaults)
- [ ] Print bar with report title input and `.print-btn`
- [ ] `printReport()` function using hardcoded colors (#d4720a accent, #167a40 pass, #c42b08 fail)
- [ ] `@media print` block inline (not in style.css) hiding header, print-bar, guide toggles
- [ ] Footer with JS-obfuscated contact email
- [ ] No characters above ASCII codepoint 127 anywhere in the file
- [ ] body font-weight 400 (set in style.css, verify not overridden)

---

## 18. Inline SVG / Canvas Charts

Some tools contain inline SVG diagrams or canvas-based charts. CSS variables do not apply
inside canvas 2D drawing calls or SVG fill/stroke attributes -- use hex values directly.

**Color mapping for chart elements:**

| Element              | Token      | Hex / value             |
|----------------------|------------|-------------------------|
| Chart background     | --bg2      | #f0f4f0                 |
| Shaded region        | --bg3      | #e6ece7                 |
| Grid lines / axes    | --border   | rgba(28,42,31,0.14)     |
| Chart border         | --border2  | rgba(28,42,31,0.22)     |
| Axis labels / text   | --muted    | #4a5e50                 |
| Primary curve        | --accent   | #186640                 |
| Dimmed / secondary   | n/a        | #7ab090                 |
| Pass / OK indicator  | --green    | #1a9e55                 |
| Warning indicator    | --yellow   | #b06a10                 |
| Fail indicator       | --red      | #b03333                 |

**SVG document icons** (guide toggle buttons) use `stroke="#186640"`. Never hardcode the
old orange `#d4720a` for SVG icons in page content -- that color is print-report only.

---

## 19. Guide Documents

Guides are inline collapsible accordion sections on tool pages. Write for the operator
on the floor, not the engineer. Plain language. Explain the physics, not just the formula.

Guide structure:
1. What is this tool?
2. How to use it (3-4 steps max)
3. How it works (underlying calculation or standard)
4. Additional concept pages as needed

Footer line on all guide pages: `smtcalc.com -- Free tools for electronics manufacturing professionals`
