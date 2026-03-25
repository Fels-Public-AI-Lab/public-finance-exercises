# TIF-lesson — CLAUDE.md

## Task
Build `TIF-lesson/index.html` — a fully self-contained, single-file interactive slide presentation and simulation teaching Tax Increment Financing for GAFL 6510 (Public & Nonprofit Finance) at the Fels Institute of Government, University of Pennsylvania.

Read this entire file before writing any code.

---

## What this is (and is not)

This is a **slide-driven simulation**, not a spreadsheet exercise. There is no sidebar, no Excel download, no hint/verify system, no SheetJS. The exercise IS the simulation — students advance through 20 slides, manipulate live sliders, and watch Chart.js charts update in real time.

---

## Single-file architecture

- Output: `TIF-lesson/index.html`
- All CSS, JS, charts, and assets embedded inline — no external files except CDN scripts and Google Fonts
- Open directly in a browser, no build tools

### CDN dependencies
```html
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Source+Serif+4:ital,wght@0,400;0,600;1,400&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/chartjs-plugin-annotation/3.0.1/chartjs-plugin-annotation.min.js"></script>
```

---

## Fels logo

Encode `Fels_Logo_Blue_Background.png` from the repo root as Base64 and embed as a data URI in the top bar. Use Python:
```python
import base64
with open('../Fels_Logo_Blue_Background.png', 'rb') as f:
    b64 = base64.b64encode(f.read()).decode('utf-8')
# <img src="data:image/png;base64,{b64}" style="height:36px;" alt="Fels Institute of Government">
```

---

## Images

Images are in `TIF-lesson/images/`. Encode each as Base64 and embed as `<img>` tags
replacing the `img-slot` placeholder divs. Match by `data-slide` attribute.
Use the correct MIME type per format: `.jpg` → `image/jpeg`, `.png` → `image/png`.

| data-slide | File | Slide | Caption / Attribution |
|---|---|---|---|
| `chicago-success-before` | `images/millennium-park-before.jpg` | 12 | Grant Park rail yard prior to redevelopment |
| `chicago-success-after` | `images/millennium-park-after.jpg` | 12 | Millennium Park, City of Chicago |
| `chicago-failure` | `images/chicago-tif-map.jpg` | 14 | Source: TIF Illumination Project, 2011 |
| `detroit-failure` | `images/detroit-vacancy.png` | 15 | Source: Detroit Blight Removal Task Force / New York Times, 2014 |
| `philly-gallery` | `images/philly-gallery.jpg` | 15 | Source: Macerich/Fashion District Philadelphia developer rendering, c. 2015 |

If an image file is missing, leave the placeholder div — do not error out.

Note: Slide 12 has TWO image slots side by side (before/after). Display them in a
two-column grid, equal width, same height (200px), object-fit: cover.

---

## Penn design system

### Colors
```css
--penn-blue:    #011F5B   /* top bar, nav buttons, headings */
--penn-maroon:  #990000   /* progress bar, section accents */
--emerald:      #2d6a4f   /* success states */
--coral:        #c44536   /* failure, risk, shortfall */
--amber:        #c47a1a   /* warning, caution */
--sky:          #778da9   /* muted labels, counters */
--steel:        #415a77   /* body text */
--cream:        #f5f0e8   /* slide background */
--warm-white:   #faf8f4   /* card backgrounds */
--sand:         #e8e0d0   /* borders */
```

### Typography
- **Slide headings:** `DM Serif Display`, 2rem–3rem, `--penn-blue`
- **Body text:** `Source Serif 4`, 1rem, `--steel`, line-height 1.7
- **Labels / numbers / formulas:** `IBM Plex Mono`, 10–12px, `--sky` or `--steel`
- **Section divider titles:** `DM Serif Display`, 3rem+, white

### Slide backgrounds
- Standard slides: `--cream`
- Section dividers: `linear-gradient(135deg, #990000 0%, #011F5B 60%)` with white radial glow at 5% opacity
- Closing slide: `#011F5B` solid

### Callout components
```css
/* Info */      border-left: 4px solid var(--penn-blue);  background: rgba(1,31,91,0.06);
/* Success */   border-left: 4px solid var(--emerald);    background: rgba(45,106,79,0.07);
/* Warning */   border-left: 4px solid var(--amber);      background: rgba(196,122,26,0.07);
/* Failure */   border-left: 4px solid var(--coral);      background: rgba(196,69,54,0.07);
/* Definition box */ background: var(--penn-blue); color: white; border-radius: 6px; padding: 1.2rem 1.5rem;
/* Step card */      background: var(--warm-white); border: 1px solid var(--sand); border-radius: 6px;
                     step number in --penn-blue filled circle, white text
```

### Stat cards
```css
background: var(--warm-white); border: 1px solid var(--sand); border-radius: 6px; padding: 1rem;
.stat-value { font-family: DM Serif Display; font-size: 1.8rem; color: var(--penn-blue); }
.stat-label { font-family: IBM Plex Mono; font-size: 10px; color: var(--sky); text-transform: uppercase; }
/* Colored variants: emerald bg / coral bg — white text on both */
```

---

## Layout

### Top bar (60px, fixed)
- Background: `--penn-blue`
- Left: Fels logo (Base64, height 36px)
- Center: `GAFL 6510 · Public & Nonprofit Finance` (IBM Plex Mono, 11px, white 60% opacity)
- Right: `Slide N of 20` (IBM Plex Mono, 11px, `--sky`)

### Slide area
- Full viewport below top bar
- Padding: `2rem 3rem` on standard slides, `4rem` on section dividers
- Max content width: 900px, centered

### Navigation
- Bottom-right fixed: `←` `→` buttons
- Style: `--penn-blue` border, white bg, hover fills Penn blue, white text
- Keyboard: ArrowLeft / ArrowRight / Space
- Progress bar: 3px, `--penn-maroon`, top of viewport, animates width

### Footer (every slide)
```
Fels Public AI Lab · GAFL 6510 · University of Pennsylvania
```
IBM Plex Mono, 10px, `--sky`, bottom of slide area.

---

## Image slot pattern

```html
<div class="img-slot" data-slide="SLOT-ID">
  <span class="img-slot-label">[ IMAGE: description ]</span>
</div>
```
```css
.img-slot {
  width: 100%; height: 200px;
  background: var(--sand); border: 2px dashed var(--sky); border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
}
.img-slot-label { font-family: IBM Plex Mono; font-size: 11px; color: var(--sky); }
```
When image files are present, replace with:
```html
<img src="data:image/jpeg;base64,{b64}" alt="description"
     style="width:100%; height:200px; object-fit:cover; border-radius:8px;">
```

---

## Slides (20 total)

### Slide 1 — Title
Background: gradient (maroon → Penn blue) + white radial glow overlay at 5% opacity.
- Eyebrow (IBM Plex Mono, white 50%): `GAFL 6510 · Public & Nonprofit Finance`
- H1 (DM Serif Display, white, ~4rem): "Tax Increment Financing"
- Subtitle (Source Serif 4 italic, white 70%): "How cities borrow against the future — and what happens when the future doesn't cooperate."
- Bottom-right meta (IBM Plex Mono, white 30%): "90-minute session · Fels Institute of Government"
- Decorative: oversized "TIF" in DM Serif Display, white at 4% opacity, absolute bottom-right

### Slide 2 — Agenda
- H2: "What we're covering today"
- Agenda rows (5), styled as bordered list rows: Part number | Title | Time
  1. The basic idea — what is a tax increment? | 20 min
  2. How TIF financing works in practice | 20 min
  3. When TIF works — a success story | 15 min
  4. When TIF fails — and why | 20 min
  5. TIF as policy — tradeoffs & reform | 15 min
- Warning callout at bottom: "TIF sounds like free money — the city pays for development using money that didn't exist before. But is it really free? Who bears the risk? And who decides?"

### Slide 3 — Section divider: Part 1
Gradient background. White text throughout.
- Label (IBM Plex Mono): "Part 1 of 5"
- H2 (DM Serif Display, ~3.5rem): "The basic idea: What is a tax increment?"
- Subtitle: "Before we get to financing, we need to understand the tax system TIF is built on."

### Slide 4 — Property tax basics
- H2: "Start here: how property taxes work"
- Lead paragraph: "Every property in a jurisdiction has an assessed value. The government applies a tax rate to that value and collects revenue."
- Formula card (IBM Plex Mono, left border Penn blue): `Tax owed = Assessed Value × Tax Rate (mills)` — sub-note: "Example: $200,000 home × 30 mills (0.030) = $6,000/year"
- Two callouts side by side: Info "Who collects it" (city, county, school district) / Success "What it pays for" (schools, police, fire, parks, roads)
- Definition box (Penn blue bg, white): "When a property's assessed value rises, it generates more tax revenue without a rate increase. That growth — the difference between the old revenue and the new — is the **increment.**"

### Slide 5 — What is the increment?
- H2: "What exactly is the 'increment'?"
- Definition box: "The increment is the increase in property tax revenue above a frozen baseline — the extra money generated when development raises assessed values in a district."
- Two-column mechanism panel (styled divs):
  - Left (light blue tint, `rgba(1,31,91,0.08)`): "Base value (frozen)" — revenue continues to flow to schools, city, county. Tag: "→ taxing districts keep this"
  - Right (light green tint, `rgba(45,106,79,0.08)`): "Increment (new growth)" — additional revenue above base, captured by TIF authority. Tag: "→ TIF authority captures this"
- Body: "The frozen base is what the neighborhood was worth before. The increment is what it's worth because of the investment. TIF redirects that new value toward paying off the investment that created it."

### Slide 6 — Increment chart (INTERACTIVE — Simulation 1)
See **Simulation 1** spec below.

### Slide 7 — Section divider: Part 2
Gradient background.
- "Part 2 of 5"
- "How TIF financing works in practice"
- Subtitle: "The increment is the revenue stream. Financing is how you borrow against it before the money exists."

### Slide 8 — Six-step process
- H2: "The six-step TIF process"
- Six step cards (Penn blue numbered circles):
  1. **Designate the district** — City declares TIF district; typically requires finding of "blight" or "but for"
  2. **Freeze the base** — Assessed value at designation becomes frozen baseline; taxing districts keep revenue on base only
  3. **Issue bonds** — Redevelopment authority borrows money, pledging future increment as repayment
  4. **Fund the project** — Bond proceeds pay for infrastructure, cleanup, or developer subsidies
  5. **Capture the increment** — As values rise, increment services bond debt
  6. **Expiration & release** — TIF ends; full assessed value flows to all taxing districts

### Slide 9 — The "but for" test
- H2: "The 'but for' test — the hinge of the whole argument"
- Warning callout: "'But for' requirement: development would NOT occur without the TIF subsidy."
- Two columns:
  - Success callout: "When it works — Contaminated brownfield. No private capital. TIF covers cleanup → development → increment pays bonds → schools get full value at expiration."
  - Failure callout: "When it doesn't — Hot downtown neighborhood. Developer would have built anyway. TIF created, increment captured — schools lose a decade of revenue."
- Failure callout full-width at bottom: "The hard truth: The 'but for' assumption is almost impossible to verify in advance. Developers have every incentive to argue they need it. This information asymmetry is where TIF breaks down."

### Slide 10 — Debt service risk (INTERACTIVE — Simulation 2)
See **Simulation 2** spec below.

### Slide 11 — Section divider: Part 3
Gradient background, slightly green tint: `linear-gradient(135deg, #1d4a36 0%, #011F5B 60%)`
- "Part 3 of 5"
- "When TIF works"
- Subtitle: "A successful TIF genuinely adds development that would not have occurred — and its increment covers the bond."

### Slide 12 — Chicago Millennium Park (SUCCESS)
- H2: "Case study: Millennium Park, Chicago"
- Image slot: `data-slide="chicago-success"` label: "[ IMAGE: Millennium Park aerial — before/after ]"
- Case label (IBM Plex Mono): "Chicago, Illinois · 1999–2024" + success tag (emerald pill)
- H3: "A contaminated rail yard becomes a $2.3 billion civic asset"
- Body: "The area north of the Loop was abandoned rail infrastructure on public land. No private developer was touching it. The city used TIF to fund infrastructure that made the site developable, then leveraged private philanthropy for the park itself. Surrounding property values exploded."
- Two columns:
  - Left: 3 success step cards (emerald check circles): "Genuine 'but for'" / "Increment materialized" / "Public asset created"
  - Right: 2 stat cards (emerald bg, white): $270M TIF financing / $2.3B estimated economic impact + success callout: "TIF worked here because the 'but for' was real, the project was a public good, and the increment was genuine new growth — not diverted appreciation."

### Slide 13 — Section divider: Part 4
Gradient, dark red tint: `linear-gradient(135deg, #7a1a1a 0%, #011F5B 60%)`
- "Part 4 of 5"
- "When TIF fails — and why"
- Subtitle: "The same tool that revitalizes a brownfield can become a slush fund, a school defunding mechanism, or a debt trap."

### Slide 14 — Chicago shadow budget (FAILURE)
- H2: "Chicago: TIF as a shadow budget"
- Image slot: `data-slide="chicago-failure"` label: "[ IMAGE: Map of Chicago TIF districts ]"
- 3 stat cards (coral bg, white): "136 active districts (peak)" / "⅓ of city land area in TIFs" / "$841M annual TIF capture (2018)"
- Two columns, failure callouts:
  - "'But for' ignored" — districts created in prosperous neighborhoods; increment was diverted appreciation, not new growth
  - "Schools starved" — CPS receives ~55% of property tax; every TIF dollar was effectively taken from schools; CTU called it a "$1 billion slush fund"
  - "Opacity & patronage" — spending controlled by mayor's office, limited council oversight
- Pull quote (Source Serif 4 italic): *"For too long, the City's TIF spending decisions have occurred in the shadows."* — Mayor Lori Lightfoot, 2019

### Slide 15 — Detroit & Philadelphia (FAILURE)
- H2: "Detroit: when the increment never materializes"
- Image slot: `data-slide="detroit-failure"` label: "[ IMAGE: Detroit vacancy map or bankruptcy headline ]"
- Lead: "Detroit illustrates a different failure mode: TIF districts in deeply distressed areas where population loss meant the increment simply never grew."
- Two failure callouts: "The structural problem" (values falling, no increment) / "Debt without revenue" (bonds came due, Chapter 9 bankruptcy 2013)
- Key concept box (coral bg, white): "TIF is procyclical in distressed markets. It works best when development momentum already exists. When used as a substitute for economic fundamentals that aren't there, the debt comes due and the city pays."
- Horizontal rule, then Philadelphia sub-case:
  - Image slot: `data-slide="philly-gallery"` label: "[ IMAGE: The Gallery / Fashion District Philadelphia ]"
  - Case label: "Philadelphia · Market East / Fashion District · 2015" + mixed/amber tag
  - H3: "The city controller challenged the 'but for' before the ink dried"
  - Body: "$55M TIF proposed for The Gallery renovation. Philadelphia's own Controller questioned whether Macerich — a major S&P 500 REIT — actually needed a public subsidy in an improving retail market. The project proceeded. The 'but for' was never independently verified."

### Slide 16 — Why TIF fails
- H2: "Why TIF fails: the structural reasons"
- Two columns, 3 failure callouts each:
  - Left: "Optimistic projections" (Lincoln Institute: most TIFs fail to meet projections) / "Displacement, not creation" (moves development, doesn't create it) / "Extended terms" (20–35 years, extensions common)
  - Right: "No seat at the table" (school districts have no formal vote on TIF creation) / "Opacity" (outside normal budget processes) / pull quote: *"In most cases, TIF has not accomplished the goal of promoting economic development."* — David Merriman, Lincoln Institute (2018)

### Slide 17 — Section divider: Part 5
Gradient, amber tint: `linear-gradient(135deg, #7a4d0a 0%, #011F5B 60%)`
- "Part 5 of 5"
- "TIF as policy: tradeoffs & reform"
- Subtitle: "TIF is authorized in 49 states. It's not going away. The question is how to use it responsibly."

### Slide 18 — Honest balance sheet
- H2: "The honest balance sheet"
- Pro/con two-column layout:
  - Green panel "Arguments for TIF": catalyzes genuinely distressed areas / revenue-neutral in theory / aligns incentives / flexible / off-balance-sheet
  - Red panel "Arguments against TIF": diverts school revenue 20–35 years / "but for" unverifiable / often used in prosperous areas / optimistic projections create liability / opaque / shifts not creates development
- Amber callout: "TIF is a tool. Your job as a public administrator is to ask whether this specific use meets the test — right problem, right safeguards, right transparency."

### Slide 19 — Reform checklist
- H2: "A five-point checklist for responsible TIF use"
- Five step cards (amber step-number circles):
  1. **Rigorous "but for" analysis** — Independent third-party review, not the developer's consultant
  2. **Conservative increment projections** — Stress-test downside; can debt service be covered at 50% of projected increment?
  3. **Affected taxing bodies must have a voice** — School districts need formal input before TIF creation
  4. **Annual public reporting** — Actual vs. projected increment, debt service coverage, projects funded — accessible, not buried in a CAFR
  5. **Sunset discipline** — Extensions require a new public finding; no automatic renewals

### Slide 20 — Closing
Background: `#011F5B` solid.
- H2 (DM Serif Display, white): "The bottom line"
- Three dark-themed callouts:
  - Neutral (blue border): "TIF is a bet. The city bets that development will happen, values will rise, and increment will cover the debt. Sometimes it pays off. Often it doesn't."
  - Success (emerald border): "When it works: contaminated land becomes a park, a brownfield becomes a neighborhood, a genuine 'but for' triggers real new growth."
  - Failure (coral border): "When it fails: schools lose a generation of revenue, bonds come due without increment to cover them, and taxpayers pick up the tab."
- Pull quote (DM Serif Display italic, white 65%): "Your job isn't to be for or against TIF. It's to ask the right questions before the city bets with public money."
- Decorative "END" (DM Serif Display, white 3% opacity, large, bottom-right)

---

## Simulation 1 — Increment area chart (Slide 6)

### Sliders
| Label | Min | Max | Default | Step |
|---|---|---|---|---|
| Base assessed value | $1M | $20M | $5M | $1M |
| Annual growth rate | 1% | 10% | 4% | 1% |
| Tax rate (mills) | 10 | 60 | 30 | 1 |

TIF duration is fixed at 10 years; chart shows years 0–20.

### Metric cards (update live)
- **Year 10 increment value** — `baseAV × ((1+growth)^10 - 1)` formatted as $XM
- **Total TIF revenue (yrs 1–10)** — sum of `increment[y] × (mills/1000)` for y=1..10
- **Taxing district frozen share** — `baseAV × (mills/1000)` per year

### Chart
- Type: `line` with `fill`
- Dataset 1 (Penn blue, fill `true`): flat `baseAV` every year — "Base assessment"
- Dataset 2 (emerald, `fill: '-1'`): `baseAV + increment[y]` for y=0..10, `null` for y=11..20 — "TIF increment"
- Dataset 3 (purple `#534AB7`, `fill: '-2'`): `baseAV + increment[y]` for y=10..20, `null` for y=0..9 — "Districts recapture"
- Annotation: dashed vertical line at x=10, coral color, label "TIF expires"
- All point radii: 0. Tension: 0.05. `spanGaps: false`.
- Y-axis tick format: `$XM`

### Legend (custom HTML, above chart)
Penn blue square / emerald square / purple square — IBM Plex Mono 11px labels

---

## Simulation 2 — Debt service coverage chart (Slide 10)

### Sliders
| Label | Min | Max | Default | Step |
|---|---|---|---|---|
| Bond amount | $1M | $10M | $4M | $1M |
| Bond interest rate | 3% | 9% | 5% | 1% |
| Projected increment growth | 1% | 10% | 6% | 1% |
| Actual increment growth | 0% | 10% | 2% | 1% |

### Scenario preset buttons
Three pill buttons above the sliders. Clicking sets all four sliders and updates charts immediately. Active button stays visually selected.

| Button | Bond | Rate | Proj growth | Actual growth | Style |
|---|---|---|---|---|---|
| Optimistic | $3M | 4% | 7% | 6% | Emerald bg, white text |
| Realistic | $4M | 5% | 5% | 3% | Amber bg, white text |
| Stressed | $6M | 6% | 6% | 1% | Coral bg, white text |

### Derived values
```js
const annualDS = bondAmt * (rate * Math.pow(1+rate, 10)) / (Math.pow(1+rate, 10) - 1);
const baseInc = 0.3; // $300K starting increment — fixed
const projInc = year => baseInc * Math.pow(1 + projGrowth, year);
const actInc  = year => baseInc * Math.pow(1 + actualGrowth, year);
const shortfall = year => Math.max(0, annualDS - actInc(year));
```

### Metric cards (update live)
- **Annual debt service** — `annualDS` formatted $XM or $XXXk
- **Cumulative shortfall (10 yr)** — sum of shortfall[1..10]; red text when > $0, green when $0
- **First year increment covers debt** — first year where `actInc(y) >= annualDS`; green "Year N" or red "Never in TIF period"

### Chart (mixed bar + line)
- Dataset 1 (gray bars, `rgba(180,178,169,0.5)`): projected increment per year
- Dataset 2 (emerald bars, `rgba(45,106,79,0.6)`): actual increment per year
- Dataset 3 (coral bars, `rgba(196,69,54,0.7)`): shortfall per year
- Dataset 4 (coral line, no fill, `borderDash: []`): flat `annualDS` across all years — "Debt service"
- X-axis labels: Yr 1 … Yr 10
- Y-axis tick format: `$XM` for ≥1M, `$XXXk` for <1M
- Hide Chart.js default legend; build custom HTML legend above chart

### Legend
Gray square "Projected increment" / Emerald square "Actual increment" / Coral square "Shortfall" / Coral line "Debt service"

---

## Number formatting (apply everywhere)
```js
function fmt(n) {
  const abs = Math.abs(n);
  const sign = n < 0 ? '-' : '';
  if (abs >= 1) return sign + '$' + abs.toFixed(1) + 'M';
  return sign + '$' + Math.round(abs * 1000) + 'k';
}
// All numbers displayed on screen go through fmt() or toFixed(). No raw floats.
// Negative: -$2.3M (sign before dollar sign, not $-2.3M)
```

---

## Accessibility
- `lang="en"` on `<html>`
- `aria-label="Previous slide"` / `"Next slide"` on nav buttons
- `aria-label` on all slider inputs
- `role="main"` on slide container
- `prefers-reduced-motion`: skip opacity transitions, keep content visible

---

## Do not build
- Sidebar
- SheetJS or Excel download
- Hint/verify system or task badges
- Progress bar in a sidebar
- Answer key
- Michael Overton attribution footer