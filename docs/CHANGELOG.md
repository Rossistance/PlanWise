# PlanWise Changelog

All notable changes to PlanWise are documented here.
Format: `[vN] YYYY-MM-DD — Summary`

---

## [v23] 2025-02-27 — Siemens Import Engine + AR Billing Dashboard

### Built On
- Correct base: `index.html` (v21+, 6,903 lines) — includes mobile layout, undo reset, disconnect team

### Added
- **Import Summary Modal** — multi-file drag-drop now shows a results table (file, status icon, detail) after all files process; single-file still uses status toast
- **5 Billing fields on Project Overview** — Revised Contract Amount, Approved COs Total, Billed to Date, Collected to Date, Retainage Balance
- **3-Segment AR Donut on Dashboard** — Cash Flow card shows Collected (green) / Retainage held (gold) / Unbilled (gray) when AR Aging data is present; falls back to original 2-segment when not

### Fixed — Viewpoint Vista Import Engine
- **`vpImportContract`** (full rewrite) — Multi-phase CO support: tracks header rows, accumulates phase `col17` amounts, uses subtotal row to finalize `amountSubmitted`; correctly resolves Approved / Pending status from `Cd` column + approval date; handles single-phase COs (no subtotal row)
- **`vpImportARaging`** (full rewrite) — Captures `totalBilled`, `totalCollected`, `totalRetainage` from AR summary rows; stores to `project.billedToDate / collectedToDate / retainageBalance`; invoice amounts now use `aged` (face value) column, not just retainage column; verbose return string
- **`vpImportAPUnapproved`** — Now functional: counts invoice detail rows, extracts "Total for Job" amount, logs warning activity entry when unapproved invoices exist
- **`vpImportJobCostMain`** — Now extracts Billed/Collected/Retainage/Revised Contract from header block (scans 60 rows vs prior 35)
- **`classifyAndImportWorkbook`** — Now returns result string; no longer calls `saveData/render` internally (single save after all files processed)
- **Drag-drop import handler** — Updated to consume return value from `classifyAndImportWorkbook`

---

## [v21] 2025-02 — Mobile Layout + 12 Audit Fixes

### Added
- **Mobile layout system** — `body.mobile-view`, bottom nav, drawer, sticky header; auto-applied on iOS/Android via `isMobileDevice()`; toggle available for desktop users
- **Undo Reset** — `performFullReset()` saves sessionStorage snapshot; 30-second animated toast allows one-click undo
- **Disconnect Team File** — button in sidebar to clear File System Access handle and return to Local mode

### Fixed (12 audit items)
- Computed totals now reactive — all Cost Breakdown, CO, and PO totals re-render immediately on edit
- CO approved/pending/credit amounts correctly propagate to Revised Contract on Dashboard
- AR Aging PAY-APPS PO no longer double-counted in PO spend metrics
- Activity log timestamps normalized to ISO
- Schedule Gantt render fixed for tasks with same-day start/end
- 2-Week Look-Ahead day grid now always shows 14 columns
- RFI cross-link to Drawing correctly resolved by `linkedDrawingId`
- Submittal cross-link to RFI correctly resolved by `linkedSubmittalId`
- Spec Exception status field normalized (Approved / Pending / Denied)
- Portfolio project picker correctly persists selection across renders
- Export HTML correctly embeds current state (not seed)
- Reset correctly clears all modules including `lookAhead` and `knowledgeBase`

---

## [v20] 2025-01 — Viewpoint Vista Import Engine

### Added
- `tryViewpointImport()` — dispatch routing for 6 report types
- `vpImportJobCostMain()` — Job Cost Summary → Cost Breakdown + Project Info
- `vpImportJobCostDetail()` — Detailed cost actuals by cost type code
- `vpImportContract()` — Change Orders from Contract Control Sheet
- `vpImportCommitments()` — Purchase Orders from Open Commitments
- `vpImportARaging()` — Pay Apps from AR Aging report
- `vpImportAPUnapproved()` — AP Unapproved Invoice tracker
- Vista PDF Purchase Order import (`tryImportPDF`, `importVistaPOFromText`)
- Helper utilities: `vpDate()`, `vpNum()`, `vpFindText()`, `vpCellNear()`
- SheetJS lazy-loader with CDN fallback

---

## [v13] 2024-12 — Portfolio + Knowledge Base

### Added
- Portfolio Dashboard tab — multi-project aggregate KPIs
- Knowledge Base / Document Store — drag-drop file attachment per project
- MS Project XML import (`importMSProjectXML`)

---

## [v11] 2024-11 — Full Module Parity

### Added
- All 12 modules complete: Overview, Costs, COs, POs, Subs, RFIs, Submittals, Spec Exceptions, Drawings, Schedule, Look-Ahead, Dashboard
- Activity log engine — auto-logs all CRUD across modules
- RAG status system — Green/Gold/Red across all dashboard cards
- Donut chart + progress bar render helpers
- Generic XLSX import for Costs, COs, POs, Schedule, RFIs, Submittals, Subs, Spec Exceptions

---

## [v2] 2024-09 — Initial Prototype

- Single-page app shell
- Project Overview + Cost Breakdown modules
- localStorage persistence
- Dark theme, CSS custom properties
