# Australian Federal Budget Simulator — v13 Snapshot

**Snapshot date:** 25 May 2026
**File:** `AusBudgetSim_v13-funds-presets-mobile_2026-05-25.jsx`
**Size:** 445 KB · 6,532 lines · 978a19eac036a9e1a672022cec4cb965 (md5)
**Status:** TypeScript-validated, all JSX components defined, all dependency arrays complete

This is a complete, working snapshot before continuing further development. The file is self-contained — drop it into any React/JSX runtime that supports Recharts and it will execute. No external state required.

## Baseline anchors

All figures from the 2026-27 Commonwealth Budget Papers (12 May 2026):

| Metric | Value | Source |
|---|---|---|
| Nominal GDP | $3,094 B | BP1 Supplementary Data |
| Underlying Cash Balance | −$31.5 B (−1.0% GDP) | BP1 Table 3.1 |
| Total receipts | $798.1 B | BP1 Table 5.7 |
| Total payments | $829.6 B | BP1 Table 6.1 |
| Company tax | $154 B | BP1 Table 5.7 |
| Public debt interest | $31.9 B (→ $46.9 B by 2029-30) | BP1 6.17 |
| State payments | $207.8 B | BP3 Table 1.1 |
| Gross debt | 34.0% GDP (peak 35.8% in 2028-29) | BP1 Statement 3 |

## Feature inventory

### 31 toggleable sections

**Revenue (17):** Progressive Income Tax · Company Tax · GST · Superannuation · Capital Gains Tax · Negative Gearing · Fuel Excise · Tobacco & Alcohol · Resource & Mining · FBT · New Taxes · Other Taxes · Trust Taxation · Environmental & Health Excises · Digital & Monopoly · Nationalisation · Tax Expenditures

**Spending (15):** Age Pension · Welfare/Family/Childcare · Health & Medicare · NDIS · Aged Care · Education · Defence · Infrastructure · APS · Climate · Other Spending · Higher Education · State & Territory commitments · Commonwealth GBEs · **Commonwealth Investment Funds** (new in Phase 13)

### 11 preset policy stances

| Preset | Tradition | Top income rate | Top company rate | Distinctive feature |
|---|---|---|---|---|
| Progressive | Social-democratic | 60% | 40% | Existing — restoration of pre-Hawke consensus |
| Fiscally Conservative | Coalition Treasury | 45% | 25% flat | Howard-Costello-Frydenberg lineage |
| Centrist | Treasury orthodoxy | 47% | 28% top | Henry Tax Review actual recommendations |
| Maximalist Progressive | Curtin + Piketty | 75% | 50% | WWII-era restoration with wealth taxation |
| Libertarian | LDP / Friedman | 25% flat | 22% flat | Comprehensive fund dissolution |
| Georgist | Henry George single-tax | 35% | 15% flat | LVT at 2%/yr as primary revenue |
| Climate Emergency | Green New Deal | 50% | 38% | Carbon at $200/t, CEFC tripled |
| Protectionist | Deakinite Australian Settlement | 48% | 38% | Customs +50%, NRFC tripled |
| Sovereignty | Post-AUKUS realism | 47% | 32% | Defence 3.5% GDP, full port re-acquisition |
| Demographic | Boomer-coalition realism | 45% | 30% | Div 296 repealed, in-home aged care doubled |
| Distributist | Catholic social teaching | 48% | 42% | Monopoly levy 15%, steep company graduation |

### Commonwealth Investment Funds (Phase 13)

Nine real funds with full lever control:

| Fund | AUM (31 Dec 2025) | Enabling Act |
|---|---|---|
| Future Fund | $267.4 B | Future Fund Act 2006 |
| Medical Research Future Fund | $24.2 B | MRFF Act 2015 |
| DisabilityCare Australia Fund | $17.8 B | DCAF Act 2013 |
| ATSI Land and Sea Future Fund | $2.6 B | (FFBoG-managed) |
| Future Drought Fund | $5.2 B | Future Drought Fund Act 2019 |
| Disaster Ready Fund | $4.7 B | (was ERF 2019, repurposed 2023) |
| Housing Australia Future Fund | $11.5 B | HAFF Act 2023 |
| Clean Energy Finance Corporation | $30.5 B | CEFC Act 2012 |
| National Reconstruction Fund Corporation | $15.0 B | NRFC Act 2023 |
| **Total** | **$378.9 B** | |

Plus 4 new proposed funds (off by default): Sovereign Wealth Fund (Norwegian model), Future Generations Fund, National Infrastructure Investment Fund, Defence Industry Investment Fund (post-AUKUS).

Each existing fund has up to 5 levers: contribution, target return, disbursement adjustment, partial sale %, full dissolution.

### State & Territory commitments (22 levers)

NSW (WSA rail · HumeLink · M12) · VIC (SRL East · Geelong Fast Rail · Yarra recovery) · QLD (Bruce Hwy · Brisbane Olympics · CopperString) · WA (Westport · METRONET · Pilbara energy) · SA (Whyalla · HMRB · Northern Water) · TAS (Macquarie Pt · Bridgewater Bridge · Freight Equalisation) · NT (Middle Arm · Defence dual-use) · ACT (Light Rail 2A · National Institutions)

### Commonwealth Government Enterprises

8 existing GBEs (dividend uplift), 4 re-acquisitions (Telstra · airports · ports · CBA), and 6 new proposed corporations (CHFC · ASMC · ACMIC · CDB · RAC · FNWF).

### Utility features

- **Reset All** — restores everything to baseline
- **Copy Inputs** — clipboard text dump with embedded re-import JSON block
- **Paste Inputs** — apply a previously-copied scenario from clipboard
- **Export / Import JSON or TXT** — file-based round-trip
- **Expand All / Collapse All** — global section state
- **Analyse Budget** (Claude API) — AI fiscal analysis of current scenario
- **Auto-save** — persists state between sessions via artifact storage API
- **Forward Estimates panel** — 5-year projection with mobile card-stacked layout

### Mobile-responsive design

Three breakpoints: desktop (>820px), tablet (520-820px), mobile (<520px).

Mobile-specific behaviour:
- Two-column layouts collapse to single column
- Forward Estimates table switches to card-stacked layout (one card per year)
- Chart height grows to 260px (from 210px)
- Bracket grid uses auto-fill minmax(155px,1fr)
- Range sliders enlarge to 32px touch targets
- Button row wraps with flex-wrap

## Architecture notes

### State management

212 useState variables total; 204 are persisted (8 are UI-only: aiText, aiLoading, adding, addingCompany, thresh, companyThresh, calcInc, globalExpand). All persisted state flows through:

1. `buildScenario()` — single source of truth for serialised state
2. `exportSettings()` — file download
3. `importSettings()` — file or pasted-text restoration
4. `copyInputs()` — clipboard text with embedded JSON block
5. `resetAllDefaults()` — baseline restoration
6. Autosave useEffect — debounced persistence to artifact storage

### Tax models

- **Income tax pool model:** 15 income brackets with calibrated taxable income pools summing to $1,450B aggregate base. Default rates reproduce ~$425.9B baseline.
- **Company tax pool model:** 6 brackets with $560B aggregate corporate taxable profit base. Default rates (25/25/25/25/30/30) reproduce $154B baseline.

### Mobile breakpoints

- `@media (max-width: 820px)` — tablet layout (collapsed columns, smaller fonts)
- `@media (max-width: 520px)` — mobile layout (card-stacked tables, full single-column)

### Forward estimates engine

5-year projection (2026-27 to 2030-31) with:
- Real-terms policy persistence (delta scales with nominal GDP)
- Debt-interest cascade at 4.0% CGS yield
- Structural balance line (cyclical oil-price shock dissipating by 2027-28)
- Compound effect on gross debt as percentage of GDP

## What's NOT in this snapshot

Items deferred for future workshopping (mentioned in earlier conversations):

- PNG/SVG card export
- Scenario comparison side-by-side UI
- Distributional impact panel by income decile
- URL-based scenario sharing
- Light/dark theme toggle
- Search/filter levers
- UBI as standalone lever (currently approximated via uniform welfare rates in Georgist preset)
- Pure-function refactor of fiscal formulas
- Metadata-driven UI for lever definitions
- Multiple-file split (model vs UI)

## Conversation history

The development trajectory of this snapshot spans ~13 phases:

- **Phase 1-4** — Initial structure, income/company tax models, GST, super, CGT
- **Phase 5** — Budget measures (WATO, neg-gearing reform, trust min tax, etc.)
- **Phase 6-7** — Expanded revenue (FBT, trust taxation, environmental excises)
- **Phase 8** — Spending expansion (universal dental/vision, mental health, splits)
- **Phase 9** — Nationalisation, digital/monopoly levies
- **Phase 10** — Tax expenditures (FTC, MLS, WHT, Div 293, R&D, thin cap)
- **Phase 11** — GBE expansion (8 existing + 4 re-acquisitions + 6 new corps)
- **Phase 12** — CollapseCtx, state-specific section, Phase 5 distribution, Copy Inputs
- **Phase 12.5** — Progressive Preset social-democratic redesign
- **Phase 13a** — 6 additional presets (Conservative, Centrist, MaxProg, Libertarian, Georgist, Climate)
- **Phase 13b** — 4 more presets (Protectionist, Sovereignty, Demographic, Distributist)
- **Phase 13c** — Commonwealth Investment Funds section
- **Phase 13d** — Toggle component fix (runtime error resolved)
- **Phase 13e** — Mobile-responsive forward estimates

## Restoring from this snapshot

1. Open Claude.ai
2. Create a new conversation (or continue an existing one)
3. Attach `AusBudgetSim_v13-funds-presets-mobile_2026-05-25.jsx` as an upload
4. Ask Claude to deploy it as an artifact, or copy the file contents into a new artifact

The file is a complete single-artifact React component. Drop it into any React 18+ environment with the Recharts library available, and it will run without modification.

If continuing development in Claude: paste the file contents into a new conversation with the message "This is the current state of the budget simulator, please load it as an artifact and continue from here" — Claude can then resume editing from this exact state.
