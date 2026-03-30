# Topo Finance — Financed Emissions Estimator

## What this project is
A single-file, browser-based estimator that helps treasury professionals estimate their company's **financed emissions** (Scope 3, Category 15) across banking relationships and money market fund holdings. Built as a lead-generation and awareness tool for Topo Finance, ending with an email CTA for deeper engagement.

The tool now uses **real emission factor data** from Topo Finance (per-bank GSIB factors from "Data Analytics (Intensities..) 30.10.2025.xlsx" and MMF/transition risk data from "Topo Finance — Financed Emissions Estimator Data.xlsx").

---

## The file
```
Documents/Claude Projects/Topo Finance/Financed Emissions Calculator/index.html
```
Single self-contained HTML file. No dependencies, no frameworks, no external calls. All CSS and JS are inline. Works by opening directly in any browser.

---

## Deployment
- GitHub repo: `https://github.com/blakehelman/topo-finance-calculator`
- Live site: `https://blakehelman.github.io/topo-finance-calculator`
- Hosted on **GitHub Pages** — no Netlify, no build pipeline
- Any `git push` to main auto-deploys within ~1 minute
- Claude may push to GitHub when asked
- Target custom domain: `calculator.topofinance.org` (DNS CNAME not yet configured)

---

## Branding
| Token | Value |
|---|---|
| Primary blue | `#1A2AEB` |
| Accent yellow-green | `#EDFE53` |
| Background | `#f9fafb` |
| White | `#ffffff` |
| Body font | System sans-serif stack |

- All `--navy` references in the CSS use `#1A2AEB`
- Logo embedded as base64 PNG inside the HTML (`LOGO_B64` constant)
- Logo renders **white** in the header, **blue** on the welcome screen
- Header bar is **hidden on the welcome screen** and appears from Step 1 onwards

---

## User flow & step logic
Steps are dynamically ordered. The `visible()` function builds the step array at runtime.

### Current step sequence
| Step ID | Label | Notes |
|---|---|---|
| `welcome` | Welcome screen | Always shown |
| `gsib-select` | Select Your Banks | 27 Global Banks listed alphabetically |
| `gsib-input` | Bank Deposits | Only if banks selected |
| `mmf` | Money Market Funds | Optional/skippable |
| `review` | Review | Summary of all inputs |
| `scope123` | Company Emissions | Optional/skippable |
| `results` | Your Results | Emissions breakdown + email CTA |

### Temporarily removed steps (code commented out, not deleted)
- **`dsib` (Regional Banking)** — removed per client feedback (agreed with Paul). Code preserved with `// TEMPORARILY REMOVED` comments.

### Key logic
- Banks listed alphabetically (previously grouped by country — old code preserved as comments)
- User-facing text says "Global Banks" (not "G-SIBs"), but internal variable names still use `gsib` prefix — do not rename these
- "Financed Emissions Estimator" (not "Calculator") throughout

---

## Data sources

### Per-bank emission factors (GSIB_FACTORS)
Source: `Data Analytics (Intensities..) 30.10.2025.xlsx`
- 27 banks with individual tCO2e per $1M factors
- Stored in `GSIB_FACTORS` object — values range from ~56 to ~998

### MMF factors (MMF_CURRENCIES)
Source: `Topo Finance — Financed Emissions Estimator Data.xlsx`
| Currency | Factor (tCO2e per $1M) |
|---|---|
| USD | 311 |
| GBP | 313 |
| EUR | 303 |

### Transition risk ratings (TRANSITION_RISK)
Source: `Topo Finance — Financed Emissions Estimator Data.xlsx`
- Per-bank high/medium/low ratings
- Banks with "NA" in spreadsheet defaulted to medium
- **High:** JPMorgan Chase, Wells Fargo, Citigroup, Barclays, HSBC, Royal Bank of Canada
- **Low:** ING, UBS
- **Medium:** all others

---

## Bank list (27 banks)
Commonwealth Bank, ANZ Bank, Royal Bank of Canada, TD Bank, Danske Bank, BNP Paribas, BPCE / Natixis, Crédit Agricole, Société Générale, Deutsche Bank, UniCredit, Mitsubishi UFJ FG, Mizuho Financial Group, Sumitomo Mitsui FG, ING, BBVA, Santander, UBS, Barclays, HSBC, Lloyds, Standard Chartered, Bank of America, Citigroup, Goldman Sachs, JPMorgan Chase, Wells Fargo

---

## Welcome screen accordions
| Heading | Icon |
|---|---|
| What it does | 📐 |
| What it is not | ⚠️ |
| Data Privacy | 🔒 |
| Want more information? | 📤 |
| ~~Data limitations~~ | ~~🚧~~ (commented out — replaced by footer disclaimer) |

Footer disclaimer at bottom of welcome page with "Full disclaimer" link (URL TBD).

---

## UI decisions made
- Header is `#1A2AEB` (Topo brand blue) throughout
- Welcome page hides the header entirely — title "Financed Emissions Estimator" appears in full blue
- Persistent header title: "Financed Emissions Estimator" centred in the header bar
- Back / Next / Skip buttons are 50% larger than other buttons (targeted via `#site-footer .btn`)
- Progress bar sits below the header, fills as user advances through steps
- Step counter format: "Step X of Y" — Y updates dynamically
- Results bar chart is CSS-only, no libraries, bars animate in on load
- Print/PDF button triggers `window.print()` with a clean print stylesheet
- Banks sorted alphabetically (not by country)

---

## What still needs to be done
- [ ] Wire up email CTA to actual CRM / Mailchimp / Formspree
- [ ] Configure `calculator.topofinance.org` subdomain
- [ ] Add URL for "Full disclaimer" link on welcome page footer
- [ ] Client to confirm if regional bank step should be restored in future

---

## What NOT to change without checking
- Internal JS variable names (`selectedGsibs`, `GSIB_FACTORS`, `gsibDeposits`, etc.) — user-facing copy uses "Global Banks" but code uses `gsib`
- The `LOGO_B64` constant — base64 PNG of the Topo Finance logo
- The `logoSVG(uid, w, h, color)` function — generates the logo at any size/color
- Step IDs (`gsib-select`, `gsib-input`, etc.) — internal identifiers, not displayed to users

---

## Session log

### Session 1 — Initial build
Built the entire tool from scratch as a single self-contained HTML file. Vanilla JS with a `render()` function, dynamic step list via `visible()`, flat `data` object for all user inputs. Originally had geography-based flow (European/US/Both) with per-region bank steps, sovereign bonds, and DSIBs.

### Session 2 — Context reload
Reloaded project context. Established CLAUDE.md as persistent session reference.

### Session 3 — Welcome page layout + responsive fixes
Two-column welcome layout (hero left, accordions right). CSS grid with intrinsic responsiveness. Fixed vertical centering, mobile viewport issues (`100dvh` vs `100vh`), accordion animation.

### Session 4 — Design polish, mobile fixes, UX tweaks
Enterprise design refresh: sharper corners, squared-off buttons (except major CTAs), increased step-wrap width to 900px. Mobile fixes for iOS Safari. Results page scope comparison redesign.

### Session 5 — Client feedback & data integration
Major changes based on client review:
- **Renamed** "Calculator" → "Estimator" throughout
- **Renamed** "G-SIB" → "Global Banks" in all user-facing text
- **Removed** regional bank step (dsib) per client agreement with Paul — code commented out
- **Removed** "Data limitations" accordion — replaced by footer disclaimer
- **Removed** "About these emission factors" accordion from bank deposits page
- **Removed** "How these figures are calculated" from results page
- **Removed** review page notice box
- **Added** footer disclaimer on welcome page with placeholder "Full disclaimer" link
- **Sorted** banks alphabetically instead of by country
- **Applied** revised copy from "Topo Finance — Site Copy.xlsx" spreadsheet across all screens (welcome subtitle, accordion texts, step titles/subtitles, scope tab labels, review page copy)
- **Updated** MMF factors to real data (USD: 311, GBP: 313, EUR: 303)
- **Updated** transition risk ratings from final data spreadsheet (H/M/L per bank, NA → medium)
- **Updated** CTA copy on results page per client
- Per-bank emission factors retained from earlier "Data Analytics (Intensities..)" file
- Session changes take priority over spreadsheet where conflicts exist (e.g. "Estimator" not "Lens")
