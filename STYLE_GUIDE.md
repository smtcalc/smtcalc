# SMTCalc.com — Design System & Style Guide

This document is the single source of truth for building and maintaining tools on smtcalc.com.
It is derived from the live HTML source files. When in doubt, cross-check against
`stencil-coach-index.html`, which is the canonical reference implementation.

---

## 1. Site Structure

All tools are self-contained single HTML files. No frameworks, no build tools, no external
dependencies beyond Google Fonts. Each file is published to its own subdirectory on GitHub Pages.

```
smtcalc.com/                              → landing page (index.html)
smtcalc.com/about/                        → about page (about-index.html)
smtcalc.com/stencil-coach/               → Stencil Coach
smtcalc.com/smt-capacity-calculator/     → SMT Capacity Calculator
smtcalc.com/msl-bake-calculator/         → MSL Bake Calculator
smtcalc.com/reflow-throughput-calculator/ → Reflow Throughput Calculator
smtcalc.com/placement-speed-verifier/    → Placement Speed Verifier
smtcalc.com/msl-tracker/                 → MSL Tracker (Excel download)
smtcalc.com/resistor-code-calculator/    → Resistor Code Calculator
smtcalc.com/solder-alloy-reference/      → Solder Alloy Reference
smtcalc.com/flux-designator-decoder/     → Flux Designator Decoder
```

Note: The MSL Tracker page is a download landing page for an Excel workbook, not a
browser-based calculator. It uses the same header, hero, and visual style as other tools,
but the primary action is a file download button rather than an interactive calculator panel.
This is currently the only tool of this type on the site.

Every new tool gets its own subdirectory and its own `index.html`.
The project filename convention is `[tool-slug]-index.html` for storage in the project directory.

### Sitemap

Always deliver an updated `sitemap.xml` alongside any new tool. All existing URLs are included,
today's date is used as `lastmod` for all entries. Standard structure:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://smtcalc.com/</loc>
    <lastmod>YYYY-MM-DD</lastmod>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://smtcalc.com/about/</loc>
    <lastmod>YYYY-MM-DD</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.6</priority>
  </url>
  <!-- one <url> block per tool, priority 0.8, changefreq monthly -->
</urlset>
```

---

## 2. Encoding Rules (Critical for Blogger / GitHub Pages)

All HTML/JS must be fully ASCII-clean. Blogger's HTML editor mangles any character above
codepoint 127. This applies even when the target is GitHub Pages, to keep the workflow consistent.

**Never use:**
- Em dashes (use a comma or period instead)
- Smart quotes / curly quotes
- Checkmark symbols (use ASCII `OK` or `PASS`)
- Currency symbols other than `$`
- Unicode minus signs (use plain hyphen `-`)
- Any character that is not plain 7-bit ASCII

**Alternatives for common symbols:**

| Intended character | Use instead       |
|--------------------|-------------------|
| Em dash `—`        | Comma or period   |
| Right arrow `→`    | `&#8594;`         |
| Checkmark `✓`      | `&#10003;` or OK  |
| Cross `✗`          | `&#10007;` or FAIL|
| Degree `°`         | `&#176;`          |
| Micro `µ`          | `&#181;`          |
| Copyright `©`      | `&copy;`          |

---

## 3. Color System

The site uses a **dark theme**. All colors are defined as CSS custom properties on `:root`.
Use variables throughout -- never hardcode hex values in component styles.

```css
:root {
  --bg:      #10141a;   /* Page background, deepest level */
  --bg2:     #171d26;   /* Hero section background */
  --bg3:     #1e2530;   /* Input fields, stat boxes */
  --bg4:     #262e3c;   /* Highlighted/active panel backgrounds */
  --accent:  #e08318;   /* Orange: primary accent, borders, labels */
  --accent2: #d43e10;   /* Red-orange: destructive / error state (also --red) */
  --green:   #1fa855;   /* Pass / OK verdict */
  --yellow:  #e08318;   /* Warning / marginal verdict (same as accent) */
  --red:     #d43e10;   /* Fail / bad verdict */
  --text:    #dde4ee;   /* Primary text, cool light */
  --muted:   #8fa6be;   /* Secondary text, labels, placeholders */
  --border:  #28323e;   /* Primary border, grid gap color */
  --border2: #303c4a;   /* Secondary border, input borders */
  --mono:    'Share Tech Mono', monospace;  /* Convenience alias */
}
```

**Verdict color logic:**

| State      | Color          | Variable  |
|------------|----------------|-----------|
| PASS / OK  | #1fa855 green  | --green   |
| MARGINAL   | #e08318 orange | --yellow  |
| FAIL / BAD | #d43e10 red    | --red     |

**Hardcoded color reference (for rgba and inline contexts):**

These values appear in rgba() expressions in CSS. Keep them in sync with the token values above.

| Token     | Hex       | rgba base for transparency use          |
|-----------|-----------|-----------------------------------------|
| --accent  | #e08318   | rgba(224,131,24,...)                    |
| --green   | #1fa855   | rgba(31,168,85,...)                     |
| --red     | #d43e10   | rgba(212,62,16,...)                     |

Header background (hardcoded, not a variable): `rgba(16,20,26,0.97)`

**Print reports always use a light theme regardless of the page theme.**
The printReport() JS function builds its own HTML with hardcoded light colors:
- Page background: #ffffff
- Accent / section labels: #c07c0a
- PASS verdict: #167a40
- FAIL verdict: #c42b08

Tools with a @media print block in their CSS use an inline :root override to reset the
dark tokens to light values for printing. Do not remove this print override.

---

## 4. Typography

Google Fonts are loaded in the `<head>`. The import string for all three families together:

```html
<link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@400;600;700;800;900&family=Barlow:wght@300;400;500&family=Share Tech Mono&display=swap" rel="stylesheet">
```

**Font roles:**

| Family              | Weight(s)         | Use case                                         |
|---------------------|-------------------|--------------------------------------------------|
| Barlow Condensed    | 900               | Page h1, tool name, verdict labels               |
| Barlow Condensed    | 700-800           | Panel labels, section eyebrows, button text      |
| Barlow              | 400               | Body text (default `font-weight` on `body`)      |
| Barlow              | 400-500           | Nav links, guide text, field notes               |
| Share Tech Mono     | 400               | Input values, units, stat labels, formula text   |

**Base body rule:**

```css
body {
  background: var(--bg);
  color: var(--text);
  font-family: 'Barlow', sans-serif;
  font-weight: 400;
  min-height: 100vh;
}
```

Note: body font-weight is **400**, not 300. Barlow Light (300) is too thin on a light background.

**Font size scale (UI elements):**

| Role                          | Size      |
|-------------------------------|-----------|
| Input field values            | 1.2rem    |
| Body / guide text             | 0.9rem    |
| Field notes, secondary prose  | 0.85rem   |
| Warn/info box text            | 0.88rem   |
| Toggle buttons, print button  | 0.9rem    |
| Panel labels, eyebrows        | 0.75rem   |
| Field labels, input units     | 0.75rem   |
| Stat labels, mono small text  | 0.7rem    |
| Formula notes, table cells    | 0.7-0.82rem |
| Powder badge (tiny)           | 0.62rem   |

---

## 5. Header

Sticky, 64px tall, blurred semi-transparent background. Contains the site logo (links to `/`)
and a tool badge showing the current tool name.

```html
<header>
  <a href="/" class="logo">SMT<span>Calc</span></a>
  <div class="tool-badge">Tool Name Here</div>
</header>
```

```css
header {
  position: sticky;
  top: 0;
  z-index: 100;
  padding: 0 2rem;
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: rgba(247,245,242,0.95);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border);
}

.logo {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 900;
  font-size: 1.4rem;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  text-decoration: none;
  color: var(--text);
}
.logo span { color: var(--accent); }

.tool-badge {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 700;
  font-size: 0.85rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--muted);
  border: 1px solid var(--border2);
  padding: 0.3rem 0.75rem;
}
```

Logo renders as: **SMT** with the "Calc" portion in orange (`--accent`).

---

## 6. Tool Hero Section

The banner immediately below the header. Light `--bg2` background with a subtle radial orange
glow behind the content (decorative, pointer-events: none). Max content width 900px, centered.

Structure:
1. Eyebrow line (category label in orange, prefixed by a short orange bar)
2. `<h1>` tool name in Barlow Condensed 900, uppercase, very large
3. `<p>` one-sentence description in muted gray

```html
<div class="tool-hero">
  <div class="tool-hero-inner">
    <div class="tool-eyebrow">Category Label Here</div>
    <h1>Tool Name</h1>
    <p>One sentence describing what the tool does and who it is for.</p>
  </div>
</div>
```

**Eyebrow category labels used across the site:**

| Tool                      | Eyebrow label                            |
|---------------------------|------------------------------------------|
| Stencil Coach             | Stencil / Paste Process                  |
| SMT Capacity Calculator   | Production / Planning                    |
| MSL Bake Calculator       | Component Handling / Moisture Management |
| Reflow Throughput Calc    | Reflow / Thermal Process                 |
| Placement Speed Verifier  | Pick and Place / Throughput              |
| MSL Tracker               | Component Handling / Moisture Management |
| Resistor Code Calculator  | Components / Passive Identification      |
| Solder Alloy Reference    | Materials / Solder Alloys                |
| Flux Designator Decoder   | Materials / Flux Chemistry               |

New tools: follow the same pattern. Two or three words, slash-separated, Title Case.

```css
.tool-hero {
  padding: 3rem 2rem 2rem;
  border-bottom: 1px solid var(--border);
  background: var(--bg2);
  position: relative;
  overflow: hidden;
}

.tool-hero::before {
  content: '';
  position: absolute;
  top: -50%; right: -10%;
  width: 500px; height: 500px;
  background: radial-gradient(circle, rgba(192,124,10,0.08) 0%, transparent 70%);
  pointer-events: none;
}

.tool-hero-inner {
  max-width: 900px;
  margin: 0 auto;
  position: relative;
  z-index: 1;
}

.tool-eyebrow {
  font-size: 0.8rem;
  font-weight: 500;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--accent);
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}
.tool-eyebrow::before {
  content: '';
  display: block;
  width: 20px; height: 2px;
  background: var(--accent);
}

.tool-hero h1 {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 900;
  font-size: clamp(2.5rem, 6vw, 4.5rem);
  text-transform: uppercase;
  line-height: 0.95;
  letter-spacing: -0.01em;
  margin-bottom: 0.75rem;
}

.tool-hero p {
  color: var(--muted);
  font-size: 1rem;
  max-width: 560px;
  line-height: 1.6;
}
```

---

## 7. Guide Banner

Displayed between the hero and the main tool grid, when a guide document exists for the tool.
Left-accented box with descriptive text and a link button.

```html
<div class="guide-banner">
  <div class="guide-inner">
    <p><strong>New to [topic]?</strong> Read the short guide covering [topics].</p>
    <a href="GUIDE_URL" target="_blank" class="guide-btn">Read the Guide &#8594;</a>
  </div>
</div>
```

```css
.guide-banner {
  max-width: 900px;
  margin: 0 auto 2rem;
  padding: 0 2rem;
}

.guide-inner {
  background: var(--bg2);
  border: 1px solid var(--border);
  border-left: 3px solid var(--accent);
  padding: 1rem 1.25rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  flex-wrap: wrap;
}

.guide-inner p {
  font-size: 0.95rem;
  color: var(--muted);
}
.guide-inner strong { color: var(--text); }

.guide-btn {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 700;
  font-size: 0.9rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--accent);
  text-decoration: none;
  border: 1px solid rgba(192,124,10,0.35);
  padding: 0.4rem 1rem;
  white-space: nowrap;
  transition: background 0.2s;
}
.guide-btn:hover { background: rgba(192,124,10,0.10); }
```

If no guide exists yet for a new tool, omit the guide banner entirely. Add it later when the
guide is published.

---

## 8. Main Tool Grid

The calculator content lives in a two-column grid. Panels are separated by 1.5px gaps rendered
as the `--border` color (the gap is the background, panels are opaque over it). Max width 900px.

```html
<div style="max-width:900px; margin:0 auto; padding:0 2rem;">
  <div class="tool-main" style="margin:0 0 1.5rem; padding:0;">

    <div class="panel">
      <!-- left column panel -->
    </div>

    <div class="panel">
      <!-- right column panel -->
    </div>

    <div class="panel-full">
      <!-- full-width panel -->
    </div>

  </div>
</div>
```

```css
.tool-main {
  max-width: 900px;
  margin: 0 auto;
  padding: 2rem;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5px;
  background: var(--border);   /* gap color trick */
  border: 1px solid var(--border);
  margin-top: 2rem;
  margin-bottom: 2rem;
}

@media (max-width: 680px) {
  .tool-main { grid-template-columns: 1fr; margin: 1rem; padding: 0; }
}

.panel {
  background: var(--bg);
  padding: 1.75rem;
}

.panel-full {
  grid-column: 1 / -1;
  background: var(--bg);
  padding: 1.75rem;
}
```

### Panel Label

Every panel starts with a label. Orange short bar + uppercase small-caps style text.

```html
<div class="panel-label">Panel Title</div>
```

```css
.panel-label {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 700;
  font-size: 0.75rem;
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
  width: 16px; height: 2px;
  background: var(--accent);
}
```

---

## 9. Form Fields

### Number Input with Unit

```html
<div class="field">
  <label>Field Name</label>
  <div class="input-wrap">
    <input type="number" id="fieldId" min="0" step="0.01" placeholder="0.00" oninput="calculate()">
    <span class="input-unit">mm</span>
  </div>
  <div class="field-note">Optional helper text explaining valid values or typical ranges.</div>
</div>
```

```css
.field { margin-bottom: 1.1rem; }

.field label {
  display: block;
  font-family: var(--mono);
  font-size: 0.75rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--muted);
  margin-bottom: 0.4rem;
}

.input-wrap { position: relative; }

.input-wrap input[type=number] {
  width: 100%;
  background: var(--bg3);
  border: 1px solid var(--border2);
  color: var(--text);
  font-family: var(--mono);
  font-size: 1.2rem;
  padding: 0.65rem 2.5rem 0.65rem 0.9rem;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
  -moz-appearance: textfield;
  appearance: textfield;
}
.input-wrap input[type=number]::-webkit-outer-spin-button,
.input-wrap input[type=number]::-webkit-inner-spin-button { -webkit-appearance: none; }
.input-wrap input[type=number]:focus {
  border-color: var(--accent);
  box-shadow: 0 0 0 2px rgba(192,124,10,0.22);
}

.input-unit {
  position: absolute;
  right: 0.75rem;
  top: 50%;
  transform: translateY(-50%);
  font-family: var(--mono);
  font-size: 0.75rem;
  color: var(--muted);
  pointer-events: none;
  text-transform: uppercase;
}

.field-note {
  font-size: 0.85rem;
  color: #7a7570;
  line-height: 1.5;
  margin-top: 0.4rem;
}
```

Spinner arrows are always hidden. Focus state shows orange border and a faint orange glow.

### Select Dropdown

Use sparingly. Styled to match the input field appearance:

```css
select {
  width: 100%;
  background: var(--bg3);
  border: 1px solid var(--border2);
  color: var(--text);
  font-family: var(--mono);
  font-size: 1.05rem;
  padding: 0.65rem 0.9rem;
  outline: none;
  cursor: pointer;
  appearance: none;
}
select:focus { border-color: var(--accent); }
```

### Toggle Button Group

Used for mutually exclusive options (e.g. aperture shape: Rectangular / Circular).

```html
<div class="shape-toggle">
  <button id="btn-a" class="active" onclick="setOption('a')">Option A</button>
  <button id="btn-b" onclick="setOption('b')">Option B</button>
</div>
```

```css
.shape-toggle {
  display: flex;
  border: 1px solid var(--border2);
  overflow: hidden;
  margin-bottom: 1.25rem;
}

.shape-toggle button {
  flex: 1;
  background: var(--bg3);
  border: none;
  color: var(--muted);
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 700;
  font-size: 0.9rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  padding: 0.6rem;
  cursor: pointer;
  transition: background 0.15s, color 0.15s;
  border-right: 1px solid var(--border2);
}
.shape-toggle button:last-child { border-right: none; }
.shape-toggle button.active {
  background: var(--accent);
  color: #1a1815;
}
```

---

## 10. Feedback / Alert Boxes

### Warning Box (error, caution)

Red left accent. Used for process warnings, borderline conditions, important caveats.

```html
<div class="warn-box">
  <strong>Warning label</strong> Explanatory text.
</div>
```

```css
.warn-box {
  background: rgba(190,46,10,0.08);
  border: 1px solid rgba(190,46,10,0.22);
  border-left: 3px solid var(--red);
  padding: 0.75rem 1rem;
  font-size: 0.88rem;
  color: #4a4540;
  line-height: 1.55;
  margin-bottom: 1rem;
}
.warn-box strong { color: var(--red); }
```

### Info Box (neutral / guidance)

Orange left accent. Used for usage tips, methodology notes.

```css
.info-box {
  background: rgba(192,124,10,0.08);
  border: 1px solid rgba(192,124,10,0.22);
  border-left: 3px solid var(--accent);
  padding: 0.75rem 1rem;
  font-size: 0.88rem;
  color: #5a5550;
  line-height: 1.55;
  margin-bottom: 1rem;
}
```

---

## 11. Results Area

### Empty State

Shown before the user has entered valid inputs.

```html
<div class="results-empty">Enter values above to calculate.</div>
```

```css
.results-empty {
  padding: 3rem 1.75rem;
  text-align: center;
  color: #6a6560;
  font-family: var(--mono);
  font-size: 0.9rem;
  letter-spacing: 0.05em;
  text-transform: uppercase;
}
```

### Stats Grid

A 4-column (2-column on mobile) grid of stat boxes. Each box has a monospace label and a large
condensed value with an optional unit suffix.

```html
<div class="stats-grid">
  <div class="stat-box">
    <div class="stat-label">Label</div>
    <div class="stat-value">42.3<span class="stat-unit"> unit</span></div>
  </div>
  <!-- repeat -->
</div>
```

```css
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1.5px;
  background: var(--border);
  border: 1px solid var(--border);
  margin-bottom: 1.5rem;
}

@media (max-width: 500px) {
  .stats-grid { grid-template-columns: repeat(2, 1fr); }
}

.stat-box {
  background: var(--bg3);
  padding: 0.9rem 1rem;
}

.stat-label {
  font-family: var(--mono);
  font-size: 0.7rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--muted);
  margin-bottom: 0.3rem;
}

.stat-value {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 800;
  font-size: 1.5rem;
  line-height: 1;
}

.stat-unit {
  font-size: 0.8rem;
  font-weight: 400;
  color: var(--muted);
  font-family: var(--mono);
}
```

### Verdict Display (large number + label)

Used when a single calculated value has a clear pass/fail threshold (e.g. area ratio).

```html
<div class="ar-display">
  <div class="ar-big ok">0.742</div>
  <div class="ar-verdict">
    <div class="verdict-label ok">GOOD</div>
    <div class="verdict-desc">Explanation of the verdict in plain language.</div>
  </div>
</div>
```

Color classes on both `.ar-big` and `.verdict-label`: `ok` (green), `warn` (yellow), `bad` (red).

### Progress Bar

Thin horizontal bar showing a value on a 0-to-max scale, with an optional threshold marker.

```html
<div class="bar-labels">
  <span>0</span>
  <span style="color:var(--yellow)">&#9650; 0.66 threshold</span>
  <span>1.0</span>
</div>
<div class="bar-track">
  <div id="bar-fill" class="bar-fill" style="width:0%; background:var(--green)"></div>
  <div class="bar-marker"></div>  <!-- optional threshold line -->
</div>
```

Animate the fill width via `setTimeout` after rendering (50ms delay) to trigger CSS transition.

---

## 12. Print Report

Every tool includes a print report function. The report opens in a new window with a light-theme
print-optimized layout.

**Print bar (below the main tool grid):**

```html
<div class="print-bar">
  <label>Report Title</label>
  <input type="text" id="reportTitle" placeholder="e.g. Product X evaluation">
  <button class="print-btn" onclick="printReport()">&#9113; Print Report</button>
</div>
```

```css
.print-bar {
  max-width: 900px;
  margin: 0 auto 3rem;
  padding: 0 2rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.print-bar label {
  font-family: var(--mono);
  font-size: 0.75rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--muted);
  white-space: nowrap;
}

.print-bar input[type=text] {
  flex: 1;
  min-width: 200px;
  background: var(--bg3);
  border: 1px solid var(--border2);
  color: var(--text);
  font-family: var(--mono);
  font-size: 0.95rem;
  padding: 0.55rem 0.9rem;
  outline: none;
}
.print-bar input[type=text]:focus { border-color: var(--accent); }

.print-btn {
  background: transparent;
  border: 1px solid var(--border2);
  color: var(--muted);
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 700;
  font-size: 0.9rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  padding: 0.55rem 1.25rem;
  cursor: pointer;
  transition: border-color 0.2s, color 0.2s;
  white-space: nowrap;
}
.print-btn:hover { border-color: var(--accent); color: var(--accent); }
```

**Print report window conventions:**

- White background, black text, light borders
- Orange (`#c07c0a`) used as accent for headers, section labels, key values
- Fonts: Barlow Condensed + Barlow + Share Tech Mono (loaded from Google Fonts in the print window)
- Header block: tool name (small caps), optional user-entered title (large), timestamp (mono)
- Orange bottom border on the header block (3px solid #c07c0a)
- Sections separated by orange monospace section labels
- Verdict colors in print JS: PASS `#167a40`, MARGINAL `#d4720a`, FAIL `#c42b08`
- Input summary shown first, then result, then stat grid, then any recommendations
- `@media print` rule: `@page { size: A4 portrait; margin: 0; }` with `-webkit-print-color-adjust: exact`
- Print window is opened via `window.open()`, written via `document.write()`, then `win.print()` on `onload`

**Hide from print on the tool page itself:**

```css
@media print {
  header, .guide-banner, .print-bar { display: none !important; }
}
```

---

## 13. Section Labels (sub-headers within panels)

Used inside result panels and print reports to separate logical groups.

```html
<div class="section-label">Calculated Values</div>
```

Pattern: orange short bar (via `::before`) + uppercase mono-style text. Same visual grammar
as panel labels and eyebrows throughout the site.

---

## 14. Global Reset and Utility

Applied at the top of every `<style>` block:

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
```

---

## 15. Meta Tags (per-tool template)

```html
<head>
  <link rel="icon" type="image/svg+xml" href="/favicon.svg">
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Tool Name] - SMTCalc</title>
  <meta name="description" content="[One sentence. Free SMT [topic] calculator. [What it does]. [Standard reference if applicable].]">
  <link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@400;600;700;800;900&family=Barlow:wght@300;400;500&family=Share+Tech+Mono&display=swap" rel="stylesheet">
</head>
```

Title format: `[Tool Name] - SMTCalc` (plain hyphen to avoid encoding issues).

---

## 16. New Tool Checklist

When building a new tool, deliver all of the following:

- [ ] `[tool-slug]-index.html` — the tool itself (self-contained, ASCII-clean)
- [ ] Updated `index.html` — new tool card added to the landing page
- [ ] Updated `sitemap.xml` — new URL added, today's date as `lastmod` on all entries

Tool file checklist:
- [ ] Correct `<title>` format
- [ ] Correct `<meta name="description">`
- [ ] Favicon link to `/favicon.svg`
- [ ] All three Google Font families imported
- [ ] All CSS variables match the design system (light theme)
- [ ] Header with logo linking to `/` and tool badge
- [ ] Tool hero with eyebrow, h1, description paragraph
- [ ] Guide banner (if guide URL is available)
- [ ] Main grid with `.tool-main`, `.panel`, `.panel-full`
- [ ] Print bar with report title input and print button
- [ ] `printReport()` function using correct light-theme accent colors (#d4720a, #167a40, #c42b08)
- [ ] `@media print` hiding header, guide banner, print bar
- [ ] No characters above ASCII codepoint 127 anywhere in the file
- [ ] body font-weight is 400, not 300

---

## 17. Guide Documents

Guides are user-facing PDF documents published to Canva and linked from the tool's guide banner.
They are not design references for Claude. The guide structure follows a consistent pattern:

**Slide / page sequence:**

1. Cover: category eyebrow (e.g. "STENCIL DESIGN / PASTE PRINTING"), tool name, one-line tagline.
   Dark background, orange accents, same typographic style as the tool hero.
2. "What is this tool?" — brief explainer in plain language
3. "How to use it" — step-by-step, 3-4 steps max
4. "How it works" — the underlying calculation or standard, explained for non-engineers
5. Additional concept pages as needed (one concept per page)
6. Footer on all pages: `smtcalc.com -- Free tools for electronics manufacturing professionals`

Guide content is written for the operator or technician on the floor, not the engineer.
Use plain language. Explain the physics, not just the formula.
