# public-finance-exercises

## Project Overview
A collection of interactive single-page HTML tutorials for GAFL 6510 (Public & Nonprofit Finance) at the Fels Institute of Government, University of Pennsylvania. Students work hands-on in Excel, building formulas themselves, then use the tutorial to verify their work, get progressive hints, and explore scenarios. Each exercise is a fully self-contained `index.html` file — no build tools, no server required.

## Repo Structure
```
public-finance-exercises/
├── sales-tax-revenue-forecasting/   # Exercise 1 — inflation adjustment, YoY %, moving average
├── budget-trend-analysis/           # Exercise 2 — (see folder CLAUDE.md)
├── personnel-budget/                # Exercise 3 — (see folder CLAUDE.md)
└── nonprofit-cash-flow/             # Exercise 4 — cash flow, cumulative position, liquidity ratio
```

## Shared Design System
All exercises use the same visual language. Do not deviate from these without a deliberate reason.

### Colors (CSS variables)
```css
--penn-blue: #011F5B      /* sidebar background, primary navy */
--penn-maroon: #990000    /* accent color (--amber), active nav indicator */
--emerald: #2d6a4f        /* success states, correct answers, column highlights */
--emerald-light: #40916c  /* download button gradient end */
--coral: #c44536          /* error states, negative values */
--sky: #778da9            /* muted text, secondary labels */
--steel: #415a77          /* body text, hint labels */
--cream: #f5f0e8          /* page background */
--warm-white: #faf8f4     /* card backgrounds */
--sand: #e8e0d0           /* borders */
```

### Typography
- **Body:** `Source Serif 4` (Google Fonts)
- **Monospace / labels / code:** `IBM Plex Mono` (Google Fonts)
- **Headings:** `Perpetua` (system fallback: Georgia)
- **Display:** `DM Serif Display` (Google Fonts)

### Key UI Patterns
- **Sidebar:** Fixed left nav (280px), Penn blue background, maroon active-border indicator, progress bar at bottom
- **Hero:** `linear-gradient(135deg, --penn-maroon 0%, --penn-blue 60%)` with radial glow overlay
- **Download button:** `.btn-download` — green gradient (`--emerald` → `--emerald-light`), lift+shadow on hover
- **Formula cards:** Left border `4px solid --amber`, monospace formula, green Excel snippet badge
- **Verify panels:** Dark navy header, tabbed Quick Check / Upload, tolerance-based validation
- **Hints:** 3 levels per task, sequentially unlocked (conceptual → specific → exact formula)
- **Discussion cards:** Collapsible accordion with numbered questions and model response frameworks
- **Answer key:** Hidden by default, reveal-toggle

### Fels Logo
The Fels Institute logo (`Fels_Logo_Blue_Background.png`) must be embedded as a Base64 data URI in the sidebar. Use Python to encode:
```python
import base64
with open('Fels_Logo_Blue_Background.png', 'rb') as f:
    b64 = base64.b64encode(f.read()).decode('utf-8')
# Insert as: <img src="data:image/png;base64,{b64}" style="max-width:200px;..." alt="Fels Institute of Government">
```
Insert **once** inside `.sidebar-brand`, before the `<h1>` tag. Never use a relative file path — the file must be self-contained.

## Architecture Rules
- **Single-file only:** All CSS, JS, data, and assets embedded in `index.html`. No external files except CDN scripts.
- **CDN dependencies:**
  - Chart.js: `https://cdn.jsdelivr.net/npm/chart.js`
  - SheetJS: `https://cdn.sheetjs.com/xlsx-0.20.3/package/dist/xlsx.full.min.js`
  - Google Fonts: preconnect + stylesheet link
- **No build tools.** Open directly in a browser.
- **Responsive:** Mobile sidebar toggles via `.menu-toggle` button; `.main` margin-left collapses at 900px.
- **Accessibility:** `skip-link`, `aria-label` on nav, `focus-visible` outlines, `prefers-reduced-motion` support.

## Verification Logic
- **Quick Check:** Student pastes specific cell values; validated with tolerance (`±$50` for dollars, `±0.15` for ratios/months, `±0.1%` for percentages). Show ✅/❌ per cell plus a plain-English correction message.
- **File Upload:** Parse with SheetJS, compare column values row-by-row against precomputed answer arrays. Produce a scorecard (rows checked / correct / errors / score %). Mark task complete at ≥90%.
- **Precompute answers in JS** at page load from the raw data array — never hardcode expected values inline.
- **`markTask(idx)`** updates badge (Pending → Verified), nav checkmark, and progress bar.

## Excel Starter Download
- Generated client-side with SheetJS (`XLSX.utils.book_new()` etc.)
- Include a descriptive title row and column headers
- Pre-fill data columns; leave student work columns blank with formula hints in the header row
- Apply currency format (`$#,##0`) to dollar columns
- Filename convention: `GAFL6510_Ex{N}_{ShortName}_Starter.xlsx`
- Always include a CSV fallback (`try/catch`) in case SheetJS fails

## Adding a New Exercise
1. Copy an existing exercise folder as a starting point
2. Replace the `R` data array and precomputed answer arrays (`ansD`, `ansE`, etc.)
3. Update task sections: formula cards, step boxes, hint content, verify panel cell references
4. Update sidebar nav links and section IDs
5. Re-embed the Fels logo (don't copy the Base64 string — re-encode from source to avoid corruption)
6. Update the footer course/semester line
7. Add the exercise to this README and to the repo README.md

## Exercises

| # | Folder | Concept | Status |
|---|--------|---------|--------|
| 1 | `sales-tax-revenue-forecasting/` | Inflation adjustment, YoY % change, 12-mo moving average, forecasting | ✅ Live |
| 2 | `budget-trend-analysis/` | — | ✅ Live |
| 3 | `personnel-budget/` | — | ✅ Live |
| 4 | `nonprofit-cash-flow/` | Net cash flow, cumulative position, months of liquidity, scenario analysis | ✅ Live |

## Formulas Reference

### Exercise 1 — Sales Tax Revenue Forecasting
- **Real Dollars:** `=($C$248/C4)*B4` — adjust nominal to current-dollar terms using CPI ratio
- **YoY % Change:** `=((D16-D4)/D4)*100` — same month prior year, starts row 16
- **Moving Average:** `=AVERAGE(E16:E27)` — 12-month rolling window, starts row 27

### Exercise 4 — Nonprofit Cash Flow
- **Net Cash Flow:** `=B4-C4` — revenue received minus operating expenses
- **Cumulative Cash Position:** `=48500+D4` (row 4 only); `=E4+D5` (rows 5–27)
- **Months of Liquidity:** `=E4/AVERAGE($C$4:$C$27)` — absolute reference locks the denominator

## Attribution
These exercises were adapted from original work by Michael Overton ([mro0001](https://github.com/mro0001)), University of Idaho. The original platform and reference assignments are available at [mro0001/mpa-budgeting-lms](https://github.com/mro0001/mpa-budgeting-lms). Exercises have been modified for use in MPA coursework at the Fels Institute of Government, University of Pennsylvania.

### Footer
Every exercise must include this footer at the bottom of the page:
```html
<div class="footer">Adapted from exercises created by Michael Overton, PhD, University of Idaho.</div>
```
Note: use **Michael** (not "Micheal") — correct the spelling if encountered in existing files.