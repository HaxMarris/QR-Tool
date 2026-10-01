# QR Converter

**File:** `QR_Converter_26-9-28.html`

Turns a customer's bid sheet, in whatever layout the customer sent, into either of these:

- **QR#**: TQL's `QR#.xlsx` lane sheet for Pricing.
- **Working copy**: the salesperson's priced sheet. The truck pay, margin, fuel and billed-rate formulas are already set up.

The whole tool is one HTML file. There is nothing to install and no server. Open it in Chrome or Edge.

---

## Using it

The page has three steps.

### 1 · Output
- **Build:** choose **QR#** or **Working copy**.
- **Rate Search start / end** (QR# only): written to columns AF/AG. If you leave them blank, the tool uses the last 30 days.
- **Pricing setup** (working copy only):
  - Margin basis: $ per load or % of bill.
  - Fuel basis: $ per mile or % of bill.
  - A fuel rate, and a margin for each of the seven mileage bands (0–100, 101–250, 251–500, 501–1000, 1001–1500, 1501–2000, 2000+).
  - All of these are written to the working copy's **Settings** tab, where they can be changed later.
- **Lane overrides & PC\*MILER** (optional):
  - A fixed origin or destination, for sheets that list only one end of each lane.
  - One equipment type for all lanes.
  - How the PC\*MILER functions are linked to your copy of the PC\*MILER add-in (leave the default unless miles show `#NAME?`).
  - When a sheet needs one of these, this box turns amber.

### 2 · Bid file
Drop the customer's file on the box (or click to browse), then click **Review lanes →**.

The tool reads `.xlsx`, `.xlsm`, `.xls`, `.xlsb`, `.ods`, `.csv` and `.tsv`.

### 3 · Review & export
Every lane the tool found is shown in an editable table before any file is written.

- **Edit any cell.** What you type is used exactly as typed. Changing a city or state clears that side's ZIP so the QR looks it up again.
- **✕ leaves a row out.** ↺ puts it back.
- **Cell colours:**
  - Red: needs attention before pricing.
  - Amber: worth a look.
  - Hover a highlighted cell to see why.
- **Filters:** All lanes · Flagged · Needs attention · Left out.
- **What was flagged** groups the flags by kind, with a lane count. Click a line to show only those lanes.
- **Not included** lists every tab the tool skipped and every row it removed as "not a lane", with the reason. If it got one wrong, click **Include** and the row comes back as a red lane to check.
- Large files show 200 lanes per page.
- **⬇ Build & download** writes the file and downloads it. A "Download again" link stays underneath, and the table stays on screen so you can fix something and build again.

File names: `QR_<customer file>_<yyyymmdd>.xlsx` or `WorkingCopy_<customer file>_<yyyymmdd>.xlsx`.

---

## What goes into the QR#

| Column | Contents |
|---|---|
| A | Lane number |
| B–G | Origin city, state, ZIP · Destination city, state, ZIP |
| H | Equipment (VAN / REEFER / FLAT) |
| I | # Loads |
| J | PC\*MILER miles: zip-to-zip, falling back to city/state |
| O | Customer Miles, when the customer's sheet gives a mileage |
| AC | Comments: the source of anything the tool resolved, plus any flags |
| AD / AE | Origin / destination country |
| AF / AG | Rate Search Period |

Rate columns are left blank for Pricing. Every file is integrity-checked before it downloads, so Excel won't report it as corrupt.

---

## Rules the tool follows

- **The customer's data is never overwritten or made up. Anything doubtful is flagged.** A state the customer gave is kept even when it looks wrong. A city that exists in several states and came without one is left blank and flagged rather than guessed.
- **Tabs:**
  - Every lane tab in a workbook is converted.
  - **History tabs** (history, past shipments, prior-year loads) are never used for quotes.
  - Instruction tabs and fuel-surcharge tables are skipped and listed under *Not included*.
- **State-only lanes** use the state's central city (Indiana → Indianapolis).
- **Regions** (e.g. "North Central → South") use the workbook's own region key when it has one. Each region becomes the central city of the states, 3-digit ZIPs or cities the key lists. A region matched to the key by meaning rather than exact name is flagged.
- **One-sided lists:** the missing end comes from a title such as `OUTBOUND SMACKOVER, AR`, or from the override in step 1. A row that is itself a whole lane ("Cleburne to Smackover") keeps both of its ends.
- **Volume:** taken from an annual column, or the Jan–Dec monthly columns added together. A lane with no volume is written as 1 load.
- **Customer miles:** a per-lane mileage column is copied into Customer Miles exactly as given. Rate-per-mile, mileage-band, deadhead and kilometre columns are never used.
- **Spelling:** known misspellings and one-letter typos are corrected, and the correction is noted in Comments. A name the tool doesn't recognise *with* a state given is left as is and **not** flagged, because many real small towns aren't in its reference list. Check those by eye (e.g. "Cincinatti").
- **Equipment:** DRY → VAN; FROZEN / COOLER → REEFER; flatbed family → FLAT.

---

## Privacy & IT notes

- **Nothing leaves the computer.** The file is read and built entirely inside the browser tab. The page makes no network requests and stores nothing between sessions.
- **Self-contained:** everything is in the one file (~4.2 MB), including:
  - SheetJS 0.18.5 (Apache-2.0), for reading `.xls` / `.xlsb` / `.ods`.
  - JSZip 3.10.1 (MIT).
  - US and Canadian city / ZIP / postal reference data. The 3-digit ZIP centroids come from the `zipcodes` package (BSD).
  - The QR# and working-copy templates.
- **If something goes wrong,** the page shows a plain message and a **Copy details for IT** button. Paste those details into the report.

---

## Troubleshooting

| You see | Do this |
|---|---|
| "A bit more info needed" | The sheet lists only one end of each lane. Enter the missing origin or destination in the amber box in step 1, then click **Review lanes** again. |
| "This file couldn't be opened" | The file is damaged, password-protected, or not really a spreadsheet. Open it in Excel and save a fresh `.xlsx`. |
| Fewer lanes than expected | Open **Not included** in step 3 to see what was skipped and why. Use **Include** to bring rows back. |
| Miles show `#NAME?` in Excel | Change **PC\*MILER functions** in step 1 to *Add-in, fall back to template link*, and build again. |
| A lane is red | Read its Comments. Fix the cell in the table, or leave the row out. |

---

## Version

**26-9-28**
- New three-step layout matching the Contract Award Converter.
- The "no volume found" flag was removed (26-9-25). Such lanes are still written as 1 load.
- One-sided lists read their origin from an `OUTBOUND <City, ST>` title, and a "<place> to <place>" row is no longer mistaken for a header (26-9-24).
