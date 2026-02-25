# Topo Finance — Financed Emissions Calculator

## What this project is
A single-file, browser-based calculator that helps treasury professionals estimate their company's **financed emissions** (Scope 3, Category 15) across banking relationships, money market funds, and sovereign bond holdings. Built as a lead-generation and awareness tool for Topo Finance, ending with an email CTA for deeper engagement.

The tool is a **mockup/prototype** — emission factors are illustrative. Real PCAF-based data has not yet been provided by the client.

---

## The file
```
Documents/Claude Projects/topo-finance/topo-finance-calculator.html
```
Single self-contained HTML file. No dependencies, no frameworks, no external calls. All CSS and JS are inline. Works by opening directly in any browser.

---

## Deployment
- Hosted on **Netlify** via manual drag-and-drop deploy
- To update: drag the HTML file onto the Netlify site's Deploys page
- Next step: connect to a GitHub repo for auto-deploy on file change
- Target URL: `calculator.topofinance.org` (subdomain not yet configured)

---

## Branding
| Token | Value |
|---|---|
| Primary blue | `#1A2AEB` |
| Accent yellow-green | `#C4E635` |
| Background | `#f9fafb` |
| White | `#ffffff` |
| Body font | System sans-serif stack |

- All `--navy` references in the CSS use `#1A2AEB`
- Logo file: `Topo-Finance-Logo-Blue.svg` (stored in Downloads, embedded as base64 PNG inside the HTML)
- Logo renders **white** in the header (via color override), **blue** on the welcome screen
- Header bar is **hidden on the welcome screen** and appears from Step 1 onwards

---

## User flow & step logic
Steps are dynamically ordered based on user selections. The `visible()` function builds the step array at runtime.

### Step sequence
| Step ID | Label | Condition |
|---|---|---|
| `welcome` | Welcome screen | Always |
| `geography` | Where you bank (European / US / Both) | Always |
| `primary-region` | Which region is majority? | Only if "Both" |
| `eu-gsibs` | European Global Banks (GSIBs) | European or Both |
| `eu-dsibs` | European Domestic Banks (DSIBs) | European or Both |
| `eu-regional` | European Regional Banks | European or Both |
| `us-gsibs` | US Global Banks (GSIBs) | US or Both |
| `us-regional` | US Regional Banks | US or Both |
| `mmf-eu` | MMF: European Sovereign | Always (skippable) |
| `mmf-us` | MMF: US Sovereign | Always (skippable) |
| `sovereign-gate` | Do you hold sovereign bonds? | Always |
| `eu-sovereign` | European Sovereign Debt | Only if sovereign gate = yes |
| `us-sovereign` | US Treasury Bonds | Only if sovereign gate = yes |
| `scope123` | Reported Scope 1/2/3 emissions | Always (skippable) |
| `results` | Results + email CTA | Always |

### Key logic
- If "Both" → asks which region is primary → does that region first, then the other
- "EU" is referred to as "European" throughout all user-facing text
- Internal variable/function names still use `eu` prefix (e.g. `euGsibs`, `isEU()`) — do not rename these

---

## Emission factors (illustrative — AWAITING REAL DATA)
| Category | Current factor (tCO₂e per $1M) |
|---|---|
| European GSIBs | 130 |
| European DSIBs | 105 |
| European Regional Banks | 85 |
| MMF: European Sovereign | 45 |
| MMF: US Sovereign | 70 |
| European Sovereign Debt | 55 |
| US Sovereign Debt | 80 |
| US GSIBs | 160 |
| US Regional Banks | 115 |

**To update:** The user will provide a spreadsheet or table. Replace values in the `FACTORS` object in the JS. Do not change the key names.

---

## Bank lists
### European GSIBs
BNP Paribas, HSBC, Deutsche Bank, Barclays, UBS, Société Générale, UniCredit, ING

### European DSIBs
ABN AMRO, Commerzbank, Santander, CaixaBank, Erste Group

### US GSIBs
JPMorgan, Bank of America, Citigroup, Wells Fargo, Goldman Sachs, Morgan Stanley

Regional banks (European and US) are entered as a single aggregate figure, not per-bank.

---

## UI decisions made
- Header is `#1A2AEB` (Topo brand blue) throughout
- Welcome page hides the header entirely — title "Financed Emissions Calculator" appears in full blue on one line
- Persistent header title: "Financed Emissions Calculator" centred in the header bar
- Back / Next / Skip buttons are 50% larger than other buttons (targeted via `#site-footer .btn`)
- Progress bar sits below the header, fills as user advances through steps
- Step counter format: "Step X of Y" — Y updates dynamically as selections are made
- Results bar chart is CSS-only, no libraries, bars animate in on load
- Print/PDF button triggers `window.print()` with a clean print stylesheet

---

## What still needs to be done
- [ ] Receive and plug in real PCAF-based emission factors from client
- [ ] Confirm/update bank lists with client
- [ ] Wire up email CTA to actual CRM / Mailchimp / Formspree
- [ ] Set up GitHub repo → connect to Netlify for auto-deploy
- [ ] Configure `calculator.topofinance.org` subdomain (DNS CNAME → Netlify)
- [ ] Decide on white vs blue logo in header (currently white — same colour as background makes it invisible if header bg ever changes)
- [ ] Client review of methodology disclaimer copy

---

## What NOT to change without checking
- Internal JS variable names (`euGsibs`, `usGsibs`, `isEU`, etc.) — user-facing copy uses "European" but code uses `eu`
- The `LOGO_B64` constant — this is the base64 PNG extracted from `Topo-Finance-Logo-Blue.svg`
- The `logoSVG(uid, w, h, color)` function — generates the logo at any size/color, called twice (header + welcome)
- Step IDs (`eu-gsibs`, `eu-dsibs`, etc.) — these are internal identifiers, not displayed to users

---

## Session log

### Session 1 — Initial build
**What was built:**
The entire tool was built from scratch in a single session as a self-contained HTML file. No framework, no external dependencies — intentional decision so it can be deployed by dragging a single file onto Netlify with zero build steps.

**Core architecture decisions:**
- **Single HTML file:** Chosen for simplicity of deployment and sharing. Client (Topo Finance) can host it anywhere, email it, or drop it on Netlify without a build pipeline.
- **No JS framework:** Vanilla JS with a `render()` function that re-renders the current step into a `#app` div. Simple enough that a framework would add overhead with no benefit.
- **Dynamic step list (`visible()` function):** Steps are not hardcoded in order. The `visible()` function builds the array at runtime based on user selections (geography, sovereign gate). This means step count and order change live as the user answers — the "Step X of Y" counter updates accordingly.
- **Data object (`data`):** All user inputs live in a single flat `data` object. Resetting the form is just `data = freshData()`.

**Geography / region logic:**
- User picks European, US, or Both
- If Both → asked which is the primary region → that region's bank steps appear first
- This was a deliberate UX choice: walk through the bigger/more relevant chunk first, then circle back
- Internally `eu` prefix is used throughout JS (e.g. `euGsibs`, `isEU()`). User-facing copy always says "European." Decision made to not rename internals to avoid breaking things.

**Bank input design:**
- GSIBs and DSIBs: per-bank input rows (named banks, individual fields)
- Regional banks: single aggregate input (not per-bank) — client confirmed regional banks don't need itemisation
- Running total shown below each bank group — updates live as user types

**Emission factors:**
- All factors are illustrative placeholders based on publicly available PCAF methodology
- Stored in a `FACTORS` object with stable key names — when client provides real data, only the values need updating, not the keys or any logic
- Factors are also printed verbatim in the methodology note on the results page

**Results page:**
- CSS-only animated bar chart — no charting library. Bars animate in using a double `requestAnimationFrame` trick to trigger the CSS transition after render.
- Largest bar gets the accent colour (`#C4E635`), others get navy
- Total shown as a large number with smart formatting (`fmtNum`: shows "1.2M" instead of "1,200,000")
- Optional: if user entered Scope 1/2/3 total, shows financed emissions as a % of that

**Logo:**
- Logo file (`Topo-Finance-Logo-Blue.svg`) was converted to a base64 PNG and embedded as a constant `LOGO_B64`
- A `logoSVG()` function wraps it in an SVG with a colour mask — allows rendering at any size and in any colour (white in header, blue on welcome screen) from one source image
- Header is hidden entirely on the welcome screen — welcome screen has its own full logo lockup

**Branding decisions:**
- Header: `#1A2AEB` (Topo primary blue) with white text and white logo
- Welcome screen: no header — title and logo in blue on white background, feels like a landing page not a form
- Progress bar: 3px accent-coloured bar under the header, hidden on welcome screen
- Back/Next/Skip buttons: 50% larger than standard buttons (targeting `#site-footer .btn`) — felt cramped at the default size

**Deployment:**
- First deployed to Netlify via manual drag-and-drop
- Goal is to move to GitHub repo → Netlify auto-deploy so updates go live on push
- Target subdomain: `calculator.topofinance.org` (DNS CNAME to Netlify not yet configured)

**Copy decisions:**
- Welcome screen cards explain scope (what it covers, what it isn't, privacy, CTA)
- Each step has a subtitle explaining what to enter
- Info-expand sections (the `+` accordions) explain GSIB/DSIB terminology inline — avoids intimidating users who don't know the jargon
- Methodology note on results is intentionally visible and prominent — tool is a lead-gen piece, not a regulatory instrument, and the disclaimer reinforces that Topo Finance can provide the real analysis

### Session 2 — Context reload (this session)
- Reloaded full project context into a new tab from CLAUDE.md and HTML file
- User requested CLAUDE.md be kept as a full session reference log going forward (to substitute for not being able to share conversation history across tabs)
- Updated MEMORY.md to enforce this pattern on all future projects
