# Implementation Plan — Sailor Summary, Daily Details & In-Charge Feed Precision

Fix sailor count summaries, sub-totals/grand-totals calculations, daily details list synchronization with Zone In-Charge profile feeds, and overall system stability in CMSys.

## Technical Root Cause Analysis

Based on deep inspection of `app.js`, `api.php`, and `index.html`:
1. **Omission of Job Cards in Daily Details & Exports**: Zone In-Charges assign sailors to both Work Orders (`work_orders`) and Job Cards (`job_cards`). However, `renderDailyDetailsSpecialView()`, `exportLmdCSV()`, and `printLmdDetails()` were only querying `store.workOrders` and completely ignoring `store.jobCards`. As a result, duties fed by In-Charges into Job Cards were omitted from daily details lists, printed PDFs, and CSV exports.
2. **Data Source Mismatch (Today vs Historical Dates)**:
   - When `dateVal === today`, summary functions were reading exclusively from `task.assigned` array and ignoring `store.dailyAllocations`.
   - When `dateVal !== today`, functions were reading exclusively from `store.dailyAllocations` and ignoring `task.assigned`.
   - If an allocation was stored in `store.dailyAllocations` for today or in `task.assigned` for a selected date, it was dropped.
3. **Database ID vs Firebase Key Mismatches**: Matching `a.work_order_id` or `a.sailor_id` against numerical `id` failed when Firebase string keys (`_fbKey`) were stored.
4. **Header Stats Discrepancy**: Top-bar statistics (`NET`, `TASK`, `TODAY N/A`, `AVAIL`) occasionally drifted because `TODAY N/A` and `TASK` were calculated using conflicting filter criteria compared to `renderSummaryView()`.

---

## Proposed Changes

### Core JavaScript Logic & Data Pipeline

#### [MODIFY] [app.js](file:///c:/Projects/CMSys/app.js)

1. **Unified Task & Assignment Accessor**:
   - Implement `getTasksForZoneAndDate(zoneId, dateVal)` to retrieve all active Work Orders AND Job Cards for any given zone and date.
   - Implement `getTaskAssignedSailors(task, dateVal)` to resolve assigned sailors by merging `task.assigned` and `store.dailyAllocations` (matching both numerical `id` and Firebase `_fbKey`), while excluding sailors on Leave/Sick/NA for `dateVal`.

2. **Total Sailor Summary & Counts Precision (`renderSummaryView`)**:
   - Use the unified accessor to populate all summary matrix sections: Ongoing Constructions, Workshop Subsections, Zone Subsections, Others Duty, Out Projects, Housing Projects, Other Bases, and Leave/Sick/Attendance.
   - Verify trade indexing: VSS (MA, CA, PA, PL, BB, RW, WL, AL, SW) and Regular (S/S, LME, ME, OJT).
   - Ensure `getColumnsSum` exact math calculation for VSS Sub Total, Reg Sub Total, and Full Total rows/columns, leading to mathematically exact Grand Totals.

3. **Daily Details List Harmonization (`renderDailyDetailsSpecialView`)**:
   - Update daily details view to use `getTasksForZoneAndDate` and `getTaskAssignedSailors`.
   - Display complete tasks fed by Zone In-Charges from their respective profiles, categorized under Zone headers with accurate total counts, S/S counts, and trade breakdown.

4. **Header Stats Uniformity (`updateCounters`)**:
   - Ensure header stats enforce:
     $$\text{NET} = \text{TASK} + \text{AVAIL} + \text{TODAY N/A}$$
   - Synchronize filter logic for `TODAY N/A` (Leave, Weekend, Sick, NA) with the summary view logic.

5. **Print & Export Synchronization**:
   - Update `exportLmdCSV()`, `printLmdDetails()`, and `shareLmdWhatsApp()` to consume the unified accessor, ensuring 100% data parity across screen views, CSV downloads, and PDF printouts.

---

## Verification Plan

### Manual Verification
1. **Summary View Matrix**:
   - Navigate to `#summary` view (`Daily Duties Summary`).
   - Check that VSS columns + Regular columns sum up correctly for each row, section sub-total, and the Grand Total row.
   - Verify that Leave, Sick & Attendance section lists all sailors on leave with accurate numbers.

2. **Daily Details List**:
   - Open `#dailydetails` view.
   - Verify that all tasks assigned by Zone In-Charges (both Work Orders and Job Cards) appear with full sailor details (Ser No, Rank, Name, Service Type, Service No, Trade).

3. **Header Stats Parity**:
   - Verify top header stats bar: `NET = TASK + AVAIL + TODAY N/A`.

4. **CSV & PDF Export Verification**:
   - Trigger `Export CSV` and `Print / PDF`.
   - Confirm that exported CSV and printed PDF match the daily details list exactly with no missing duties or sailors.
