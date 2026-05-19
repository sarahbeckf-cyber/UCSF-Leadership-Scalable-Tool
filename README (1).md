# UCSF Health — Ambulatory Physician Leadership Tool

A single-file, static web app that visualizes the ambulatory leadership cascade (SVP → CMO → AEMD → Medical Director → Cost Center) and provides a standardized **role-sizing rubric** for the Ambulatory Physician Leadership Harmonization workgroup.

## Features

- **Leadership Cascade** — interactive tree from SVP down to individual cost centers, with admin-director (dyad) annotations
- **Role Sizing Rubric** — 8 metrics across two domains (Clinical Scope, Structural Complexity), weighted scoring, recommended cFTE band
- **Save & Export** — saved scores persist in browser localStorage; CSV export for sharing with the workgroup
- **No backend** — pure HTML / CSS / vanilla JS; runs offline, deploys to GitHub Pages with zero config

## Quick start

1. Clone or download this repo.
2. Open `index.html` in a browser (double-click works).
3. To deploy to GitHub Pages:
   - Push to a GitHub repo
   - Settings → Pages → Source: `main` branch / `/root`
   - Live at `https://<username>.github.io/<repo>/`

## Loading your data

The tool ships with placeholder data. To load the real UCSF roster, edit the `LEADERSHIP_DATA` object near the bottom of `index.html`. See the **Data Schema** tab inside the app for the full structure — or this short summary:

```javascript
const LEADERSHIP_DATA = {
  svp:   { name: "Inga Lennes",   ccCount: 655 },
  cmo:   { name: "John Fangman",  ccCount: 343, aemdCount: 8 },
  aemds: [
    {
      name: "Jen Perkins",
      vacant: false,
      totalCCs: 48,
      medicalDirectors: [
        {
          name: "Chienying Liu",
          dyads: ["Marino, Mark"],
          metrics: null,         // or { visits:3, clinicians:2, ... } to pre-score
          costCenters: [
            { id: "707069", name: "ADT Lipids PARN", division: "Endocrinology",
              adminDir: "Marino, Mark", manager: "Zhang, Vicky Zhang X." }
          ]
        }
      ]
    }
  ]
};
```

### Converting from Excel

The source-of-truth file is `Physician_Leader_Cost_Center_Map_Master_16.xlsx`. Fastest conversion:

```bash
# Python one-liner with pandas
python -c "
import pandas as pd, json
df = pd.read_excel('Physician_Leader_Cost_Center_Map_Master_16.xlsx')
print(json.dumps(df.to_dict(orient='records'), indent=2))
"
```

Then group by AEMD → Medical Director → Cost Center and paste into `LEADERSHIP_DATA.aemds`.

## Rubric configuration

The 8 metrics, weights, tier thresholds, and cFTE bands are all editable. Look for `RUBRIC_CONFIG` and the `data-weight` attributes on the `<select>` elements in `index.html`.

**Primary drivers** (weighted ×1.5):
- Visit volume / yr
- Clinicians supervised

**Standard drivers** (weighted ×1.0):
- Subspecialty complexity
- Patient complexity
- Number of sites
- Practice setting
- Adult / Peds mix
- Strategic growth priority

**Default tier bands:**

| Score | Tier | Suggested cFTE |
|------:|------|---------------:|
| 0–12 | Tier 4 · Focused MD | 0.10–0.20 |
| 13–18 | Tier 3 · Medical Director | 0.20–0.40 |
| 19–24 | Tier 2 · AEMD | 0.40–0.60 |
| 25+ | Tier 1 · Exec/CMO scope | 0.60–1.00 |

Adjust these in `RUBRIC_CONFIG.tierThresholds` to match what the workgroup lands on.

## Privacy

Everything runs client-side. Saved scores live in browser localStorage and never leave the user's device unless explicitly exported via CSV.

## File structure

```
/
├── index.html      ← the entire app (HTML + CSS + JS + data)
├── README.md       ← this file
└── .gitignore
```

## License

Internal UCSF Health tool. Not for redistribution.
