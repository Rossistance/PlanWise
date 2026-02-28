# PlanWise — Construction Project Management

A single-file, client-side construction project management application built for **1910 Legacy Enterprises**. No server, no database, no subscriptions — everything runs in your browser and saves locally.

## Live Demo

Deploy to GitHub Pages: `https://<your-username>.github.io/<repo-name>/`

---

## Features

| Module | Description |
|--------|-------------|
| **Project Overview** | Job info, contract amounts, billing progress fields |
| **Cost Breakdown** | Budget / Committed / Actual / Forecast by cost type |
| **Change Orders** | Full CO log with approved / pending / credit tracking |
| **Purchase Orders** | PO log with invoice sub-ledger per PO |
| **Subcontractors** | Scope tracker — bid, award, contact, PO links |
| **RFI Log** | Open/Closed RFIs with drawing & submittal cross-links |
| **Submittals** | Submittal register with spec section and review status |
| **Spec Exceptions** | Variance request tracker with approval status |
| **Drawings** | Drawing register with category and RFI links |
| **Schedule / Gantt** | Interactive Gantt chart with critical path display |
| **2-Week Look-Ahead** | 14-day crew task planner |
| **Dashboard** | RAG status cards, donut charts, KPI grid, activity log |
| **Portfolio** | Multi-project portfolio view (cross-project summary) |

### Import Engine (Viewpoint Vista XLS)

Drag-and-drop import for all 6 standard Viewpoint export types:

| Report | What Gets Imported |
|--------|--------------------|
| Job Cost Detail Main Page | Cost breakdown + project info + billing totals |
| Job Cost Detail | Detailed cost actuals by cost type |
| Contract Control Sheet | Change orders (multi-phase CO support) |
| Open Commitments | Purchase orders + committed amounts |
| AR Aging | Pay applications + billed/collected/retainage totals |
| AP Unapproved Invoices | Unapproved AP invoice count + total |

Also supports: XLSX / CSV generic imports, MS Project XML, PDF purchase orders (Vista / Axis Energy format).

### Other Capabilities

- **Mobile layout** — responsive bottom-nav on phones/tablets
- **Team File sync** — share a JSON file via any cloud drive (File System Access API)
- **Undo Reset** — 30-second undo window after data reset
- **Export** — Save as standalone HTML, Excel export, HTML report
- **Activity Log** — Auto-logged audit trail for every module action
- **Portfolio Dashboard** — Aggregate KPIs across all loaded projects

---

## Quick Start

### Option A — GitHub Pages (recommended)

1. Fork or clone this repo
2. Go to **Settings → Pages → Source: main branch / root**
3. Visit `https://<username>.github.io/<repo>/`

### Option B — Run locally

```bash
# No build step needed — just open the file
open index.html
# or
python3 -m http.server 8080
```

---

## Data Storage

| Mode | How |
|------|-----|
| **Local** | `localStorage` in your browser |
| **Embedded** | Data baked into the HTML on "Save File" export |
| **Team File** | Shared `.json` file via File System Access API |

Data is **never sent to any server**. Everything is local-first.

---

## File Structure

```
planwise/
├── index.html          # Entire application (single file)
├── README.md           # This file
├── .github/
│   └── workflows/
│       └── pages.yml   # GitHub Pages auto-deploy
├── .gitignore
└── docs/
    ├── CHANGELOG.md    # Version history
    ├── IMPORT_GUIDE.md # How to export from Viewpoint & import
    └── DATA_MODEL.md   # State shape reference
```

---

## Versioning

| Version | Summary |
|---------|---------|
| v23 | Correct base (index.html v21+); fixed vpImportContract multi-phase CO; vpImportARaging billed/collected/retainage capture; vpImportAPUnapproved functional; multi-file import summary modal; 3-segment AR donut on dashboard; 5 billing fields on Project Overview |
| v21 | Mobile layout, undo reset, disconnect team file, 12 audit fixes |
| v20 | Viewpoint Vista import engine added |

See [CHANGELOG.md](docs/CHANGELOG.md) for full history.

---

## Tech Stack

- **Vanilla JS** — no framework, no build toolchain
- **SheetJS (xlsx 0.18.5)** — lazy-loaded from CDN for XLS/XLSX parsing
- **File System Access API** — team file sharing (Chrome/Edge only)
- **localStorage** — default persistence
- **CSS custom properties** — theming + dark mode

---

## License

Internal tool — 1910 Legacy Enterprises. Not licensed for public redistribution.
