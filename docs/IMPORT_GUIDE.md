# PlanWise Import Guide

## Viewpoint Vista — How to Export & Import

PlanWise recognizes 6 standard Viewpoint report exports. Export as `.xls` or `.xlsx` from Viewpoint, then drag-and-drop onto PlanWise (or click **Import Files** in the toolbar).

You can drop all 6 files at once — PlanWise processes them sequentially and shows a summary modal when done.

---

### Report 1 — Job Cost Detail Main Page

**Where in Viewpoint:** Job Cost → Reports → Job Cost Detail → run with "Summary Page" option  
**What PlanWise imports:**
- Project name, customer, PM from header block
- Original contract, revised contract, approved COs amounts
- Billed to Date, Collected to Date, Retainage Balance (if present in header)
- Cost breakdown by type: Labor / Material / Subcontract / Equipment / Indirect / Admin

**Detection signature:** `"Custom JC Contract Cost Report"` or `"Contract Costs to Date"` + `"Estimated"` in top 8 rows

---

### Report 2 — Job Cost Detail (Transaction Detail)

**Where in Viewpoint:** Job Cost → Reports → Job Cost Detail → run without summary, phase detail  
**What PlanWise imports:**
- Cost actuals by cost type from "Total for Cost Type: N" summary rows
- Only imports main EPC phase (95-xxx codes); skips overhead phase (92-xxx)

**Detection signature:** `"JC Detail"` in header, or filename contains `job_cost_detail` (not `main`)

---

### Report 3 — Contract Control Sheet

**Where in Viewpoint:** Job Cost → Contract → Contract Control Sheet  
**What PlanWise imports:**
- All change orders: PCO#, ACO#, description, date submitted, date approved
- Amount: correctly handles multi-phase COs (sums phase rows, uses subtotal row to finalize)
- Status: Approved (Cd = "A" or approval date present), Pending (Cd = "P")
- Credit COs (negative amounts) handled correctly

**Detection signature:** `"Contract Control Sheet"` or `"PCO"` + `"ACO"` + `"Approved"` in top 8 rows

**Multi-phase CO note:** Viewpoint sometimes splits one CO across multiple cost phase rows. PlanWise accumulates all phase amounts and finalizes on the "Subtotal for OTHER:" row.

---

### Report 4 — Open Commitments

**Where in Viewpoint:** Accounts Payable → Reports → Open Commitments  
**What PlanWise imports:**
- Purchase orders: PO number, vendor, description, order date, ordered by
- Total ordered amount, total invoiced to date
- Creates a "VP-Import" summary invoice on each PO for the invoiced-to-date amount

**Detection signature:** `"Open Committments"` or `"Open Commitments"` in top 8 rows (Viewpoint has a typo in older versions)

---

### Report 5 — AR Aging

**Where in Viewpoint:** Accounts Receivable → Reports → AR Aged Analysis  
**What PlanWise imports:**
- All pay applications as invoices under the `PAY-APPS` PO
- Invoice number, date, description, face amount (aged column), retainage held
- Summary totals: Total Billed to Date, Total Collected, Total Retainage
- Stores billing progress on project: `billedToDate`, `collectedToDate`, `retainageBalance`
- These totals power the 3-segment Cash Flow donut on the Dashboard

**Detection signature:** `"AR Aged"` or `"AR Aging"` or `"Aged Analysis"` in top 8 rows

---

### Report 6 — AP Unapproved Invoices

**Where in Viewpoint:** Accounts Payable → Reports → AP Unapproved Invoices  
**What PlanWise imports:**
- Count of unapproved invoices for this job
- Total dollar amount pending approval
- Logs a warning activity entry visible in the Dashboard activity feed

**Detection signature:** `"AP Unapproved"` or `"Unapproved Invoices"` in top 8 rows

---

## Vista PDF Purchase Orders

PlanWise can import Vista-generated Purchase Agreement PDFs.

**Requirements:** PDF must be digitally created (not scanned). Text must be extractable by the browser.

**What gets imported:**
- PO number from `"Purchase Agreement #: XXXXX"`
- Vendor name
- Job number
- Subtotal amount

**Limitation:** Axis Energy and Vista PDF layouts vary. If import fails, the status bar shows a warning suggesting copy-paste instead.

---

## Generic XLSX / CSV Imports

PlanWise also imports standard spreadsheets for other modules. Detection is by content analysis:

| Content detected | Module populated |
|-----------------|-----------------|
| `variance request` or `justification` columns | Spec Exceptions |
| `pwa`, `rfp sent`, `pricing received` columns | Subcontractors |
| `rfi` + `question` or `response` | RFI Log |
| `submittal` + `review status` | Submittals |
| `change order` or `co #` + `amount` | Change Orders |
| `predecessor` or `task name` + `start` + `finish` | Schedule |
| `budget` + `actual` + `committed` | Cost Breakdown |

---

## Troubleshooting

**"Could not classify data" warning:**  
The file type wasn't recognized. Check that it's one of the 6 Viewpoint report types and that the Viewpoint header row is present (don't trim the file before importing).

**COs imported with $0 amounts:**  
This usually means the file has multi-phase COs where the amount is only on phase rows, not the header. v23+ handles this — ensure you're on the latest version.

**AR Aging shows retainage not billed amount:**  
Older versions used the retainage column as the invoice amount. v23+ uses the aged (face value) column. Re-import with v23.

**Duplicate POs after import:**  
PlanWise matches by PO number. If you imported a PO from both Open Commitments and a PDF, the second import updates the existing PO rather than creating a duplicate.
