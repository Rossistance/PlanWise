# PlanWise Data Model

The entire application state is a single JavaScript object (`state`) persisted to `localStorage` and optionally to a shared JSON team file. This document describes every field.

---

## Top-Level Shape

```js
state = {
  project:        { ...ProjectInfo },
  costs:          { ...CostCategories },
  changeOrders:   [ ...ChangeOrder ],
  purchaseOrders: [ ...PurchaseOrder ],
  subcontractors: [ ...Subcontractor ],
  rfis:           [ ...RFI ],
  submittals:     [ ...Submittal ],
  specExceptions: [ ...SpecException ],
  drawings:       [ ...Drawing ],
  schedule:       { ...Schedule },
  lookAhead:      { ...LookAhead },
  activityLog:    [ ...ActivityEntry ],
  knowledgeBase:  [ ...KBEntry ],
  _version:       1
}
```

---

## `project` — Project Overview

```js
{
  // Identity
  projectName:          string,   // e.g. "Customer - Job Site" (from Contract Descript field)
  jobNumber:            string,   // "24-003"
  jobType:              string,   // "EPC" | "EPC-M" | "BOS" | ...
  workType:             string,   // "New Build" | "Retrofit" | ...
  sizeDC:               string,   // kWdc, e.g. "1518"
  description:          string,

  // Contract & Billing
  contractAmount:       number,   // Original contract value
  contractType:         string,   // "Lump Sum" | "T&M" | "Cost Plus" | "GMP" | "Unit Price"
  revisedContractAmount:number,   // Original + approved COs (from Viewpoint import)
  approvedCOsTotal:     number,   // Sum of approved CO amounts
  billedToDate:         number,   // From AR Aging import
  collectedToDate:      number,   // From AR Aging import
  retainageBalance:     number,   // From AR Aging import

  // Customer
  customer:             string,
  customerAddress:      string,
  jobContact:           string,
  jobContactPhone:      string,
  jobContactEmail:      string,

  // Team
  pm:                   string,
  superintendent:       string,
  estimator:            string,
  fieldLeader:          string,

  // Dates
  scheduleStart:        string,   // ISO date "YYYY-MM-DD"
  scheduleEnd:          string,
  startDate:            string,
  endDate:              string,
  mechCompletion:       string,
  substCompletion:      string,

  // Site & Engineering
  address:              string,
  engineer:             string,
  engineerAddress:      string,
  engineeringPurview:   string,

  // Compliance
  bondRequired:         "Yes" | "No",
  insuranceCert:        "Yes" | "No",
  certifiedPayroll:     "Yes" | "No",
  plaDavisBacon:        "Yes" | "No",
}
```

---

## `costs` — Cost Breakdown

```js
{
  labor:          [ ...CostLine ],
  indirectLabor:  [ ...CostLine ],
  material:       [ ...CostLine ],
  subs:           [ ...CostLine ],
  equipment:      [ ...CostLine ],
  admin:          [ ...CostLine ],
  other:          [ ...CostLine ],
  subcontract:    [ ...CostLine ],  // legacy alias for subs
}

// CostLine
{
  id:        string,   // uid, e.g. "vp-c01"
  name:      string,   // "Self-Perform Labor"
  budget:    number,
  committed: number,
  actual:    number,
  forecast:  number,
}
```

**Referential integrity:** `changeOrders[].costLineId` → `costs.{category}[].id`

---

## `changeOrders` — Change Orders

```js
{
  id:              string,   // uid
  coNumber:        string,   // "1", "2", ... or "PCO-001"
  dateSubmitted:   string,   // ISO date
  dateApproved:    string,   // ISO date (optional)
  description:     string,
  amountSubmitted: number,   // Can be negative for credits
  amountApproved:  number,
  amountPending:   number,
  custCONumber:    string,   // e.g. "PCO-006"
  approvedBy:      string,
  costLineId:      string,   // → costs.{cat}[].id
  linkedPOId:      string,   // → purchaseOrders[].id
}
```

---

## `purchaseOrders` — Purchase Orders

```js
{
  id:             string,   // uid, e.g. "vp-po1"
  poNumber:       string,   // "24-003092" or "PAY-APPS"
  vendor:         string,
  description:    string,
  costLineId:     string,   // → costs.{cat}[].id
  originalAmount: number,
  adjustedAmount: number,
  orderDate:      string,
  orderedBy:      string,
  invoices:       [ ...Invoice ],
}

// Invoice
{
  id:           string,
  number:       string,   // Invoice # from vendor
  dateReceived: string,
  dateApproved: string,
  datePaid:     string,
  amount:       number,
  description:  string,
}
```

**Special PO:** `poNumber === "PAY-APPS"` is the Customer AR pay-application ledger. Its invoices are pay apps billed to the owner, not vendor invoices.

---

## `subcontractors` — Subcontractor Tracker

```js
{
  id:               string,
  tradeCategory:    string,   // "Civil" | "Electrical" | "Landscaping" | ...
  company:          string,
  contact:          string,
  phone:            string,
  email:            string,
  pwa:              boolean,  // Prevailing Wage Act
  union:            boolean,
  contactDate:      string,
  rfpSent:          string,
  pricingReceived:  string,
  quoteAmount:      number,
  selected:         boolean,
  awarded:          boolean,
  costLineId:       string,   // → costs.{cat}[].id
  poId:             string,   // → purchaseOrders[].id
  notes:            string,
}
```

---

## `rfis` — RFI Log

```js
{
  id:               string,
  rfiNumber:        string,   // "001"
  status:           "Open" | "Closed" | "Awaiting Approval",
  dateSubmitted:    string,
  dateRequired:     string,
  dateClosed:       string,
  questionFrom:     string,
  reference:        string,   // Drawing/spec reference
  question:         string,
  answer:           string,
  linkedDrawingId:  string,   // → drawings[].id
  linkedSubmittalId:string,   // → submittals[].id
}
```

---

## `submittals` — Submittal Register

```js
{
  id:          string,
  status:      "Pending" | "Approved" | "Awaiting Approval" | "R&R" | "Rejected",
  description: string,
  fileName:    string,
  uploadedBy:  string,
  uploadDate:  string,
  reviewedBy:  string,
  reviewDate:  string,
  specSection: string,   // e.g. "16120"
  notes:       string,
}
```

---

## `specExceptions` — Spec Exception / Variance Log

```js
{
  id:               string,
  specRef:          string,   // e.g. "1.1.D"
  varianceRequest:  string,
  justification:    string,
  status:           "Approved" | "Pending" | "Denied",
}
```

---

## `drawings` — Drawing Register

```js
{
  id:           string,
  name:         string,
  originalName: string,   // Original filename
  type:         string,   // "pdf" | "dwg" | ...
  category:     string,   // "Electrical" | "Civil" | "Structural" | ...
  uploadDate:   string,
  size:         number,   // bytes
  notes:        string,
}
```

---

## `schedule` — Gantt Schedule

```js
{
  ganttStart:   string,   // ISO date — left edge of Gantt
  ganttEnd:     string,   // ISO date — right edge of Gantt
  showCritical: boolean,
  tasks:        [ ...Task ],
}

// Task
{
  id:              string,   // "sc01"
  name:            string,
  level:           0|1|2,   // 0=phase/milestone, 1=summary, 2=task
  start:           string,  // ISO date
  end:             string,  // ISO date
  percentComplete: number,  // 0-100
  predecessors:    string,  // "sc01,sc02" comma-separated task IDs
}
```

---

## `lookAhead` — 2-Week Look-Ahead

```js
{
  weekStart:  string,   // ISO date — Monday of week 1
  preparedBy: string,
  tasks: [{
    id:    string,
    name:  string,
    days:  { "0":bool, "1":bool, ..., "13":bool },  // 14 days, 0=Mon wk1
    crew:  string,
    notes: string,
  }]
}
```

---

## `activityLog` — Activity Log

```js
{
  id:     string,   // uid
  ts:     string,   // ISO timestamp
  module: string,   // "changeOrders" | "rfis" | "import" | ...
  type:   string,   // "create" | "edit" | "status" | "import" | "info" | "warning"
  action: string,   // Short display string
  detail: string,   // Longer detail string
}
```

---

## `knowledgeBase` — Document Store

```js
{
  id:         string,
  name:       string,
  type:       string,   // MIME type or extension
  size:       number,
  importedAt: string,
  content:    string,   // Text (for PDFs/text) or base64 (for binary)
}
```

---

## Referential Integrity Map

```
costs.{category}[].id
  ← changeOrders[].costLineId
  ← purchaseOrders[].costLineId
  ← subcontractors[].costLineId

purchaseOrders[].id
  ← changeOrders[].linkedPOId
  ← subcontractors[].poId

drawings[].id
  ← rfis[].linkedDrawingId

submittals[].id
  ← rfis[].linkedSubmittalId

schedule.tasks[].id
  ← schedule.tasks[].predecessors  (comma-separated)
```

All cross-module links use `id` fields (stable UIDs). When an item is deleted, dangling references are tolerated (render gracefully shows "—" or skips the link chip).
