# CMSys — Trade Headcount Allocation & Zone Team Implementation Plan

## Summary & Revised Objectives

Based on your explicit directives:
1. **No Specific ID Assignment by Admin OIC**: The Admin OIC does **NOT** manually assign individual sailor IDs (e.g. VAS 72324 Nanda Kumar) to locations. Instead, the Admin OIC allocates the **trade headcount numbers** (e.g., A-Zone: 15 MA, 5 CA, 3 WE; Housing Project: 10 MA, 8 PA) for each location.
2. **Zone In-Charge ID Assignment**: The trade headcount assigned by Admin OIC is displayed to each Zone/Location In-Charge on their dashboard sidebar (`⚡ Admin Allocated Headcount`). The Zone In-Charge then selects specific sailor IDs from their available trade pool to assign to their active Projects (P), Jobs (J), and Tasks (T).
3. **Zone Team Concept Integration (15 Members Max)**:
   - Each Zone In-Charge manages a designated **Zone Team** (up to 15 fixed members max).
   - In the Allocation Centre, the Admin OIC can see the Zone Team counts when deciding the location's trade headcount allocation.
   - Generally, the fixed Zone Team headcount is included in the zone's allocation. However, if needed for specific urgent works in another zone/project, the Admin OIC can override/re-assign trade numbers to deploy personnel elsewhere as per Admin IC concern.
4. **Standalone Window Login Independence**:
   - Opening the Allocation Centre in a standalone window (`index.html?window=allocation-centre`) automatically hides the login screen and grants access without asking for separate login credentials.
5. **Visibility of Drafts, Out-Projects, and N/As**:
   - Explicitly categorizes and displays **Temp Drafts** (external bases), **Out-Projects** (external project sites), and **N/A Personnel** (Leave, Sick, Hospital, AWOL) with distinct badges in Phase 1.

---

## Allocation Centre Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      ALLOCATION CENTRE WORKSTATION                          │
│ Target Date: [ Tomorrow ▼ ] [ Today ]          Mode: ⚡ Full Dispatch & Edit│
├─────────────────────────────────────────────────────────────────────────────┤
│ 🏢 PHASE 1 — TOMORROW'S HEADCOUNT ASSESSMENT                                │
│ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────────────┐ │
│ │Net Complement│ │AvailableForce│ │Drafts/OutProj│ │ Leave / Sick / N/A   │ │
│ │     643      │ │     492      │ │      88      │ │          63          │ │
│ └──────────────┘ └──────────────┘ └──────────────┘ └──────────────────────┘ │
│ Trade Breakdown: MA: 187 | CA: 89 | PA: 45 | WE: 34 | PL: 28 | AL: 18 ...  │
│ [▼ Show/Hide N/A, Drafts & Out-Projects List]                               │
├─────────────────────────────────────────────────────────────────────────────┤
│ 📊 PHASE 2 — TRADE HEADCOUNT ALLOCATION MATRIX (CORE DISPATCH)              │
│ 🛡️ Zone Team Concept: Zone ICs manage 15 fixed team members. Admin IC can   │
│    adjust headcount or deploy Zone Team members elsewhere for urgent works.│
│                                                                             │
│ ┌─ Location / Section ──┬─ MA ─┬─ CA ─┬─ PA ─┬─ WE ─┬─ Total Allocated ┐    │
│ │ A-Zone (Team: 15/15)  │ [15] │ [5]  │ [3]  │ [2]  │        25       │    │
│ │ BC-Zone (Team: 15/15) │ [12] │ [4]  │ [2]  │ [1]  │        19       │    │
│ │ Housing Project       │ [10] │ [8]  │ [6]  │ [0]  │        24       │    │
│ │ Central Workshop      │ [6]  │ [0]  │ [0]  │ [10] │        16       │    │
│ └───────────────────────┴──────┴──────┴──────┴──────┴─────────────────┘    │
│ Remaining Unallocated Strength: MA: 144 | CA: 72 | PA: 34 | WE: 21 ...      │
│ [💾 Save & Commit Trade Allocations] [🔄 Reset]                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼ (Committed Headcount Pushed)
┌─────────────────────────────────────────────────────────────────────────────┐
│                      MAIN DASHBOARD (ZONE IC BOARD)                         │
│ ⚡ Admin Allocated Headcount (A-Zone): MA:15 CA:5 WE:3 (Total: 23 IDs)      │
│ 👤 Zone IC selects specific IDs from available pool to assign to P / J / T  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Key Technical Changes

### 1. Allocation Centre Streamlining
- **[MODIFY]** [index.html](file:///c:/Projects/CMSys/index.html)
- Streamlined Allocation Centre into a focused 2-Phase system (Phase 1: Headcount Assessment & N/A Breakdown; Phase 2: Trade Headcount Allocation Matrix).
- Removed redundant individual ID selector table from Allocation Centre as Admin OIC only sets trade numbers.

### 2. Zone Team Integration
- **[MODIFY]** [index.html](file:///c:/Projects/CMSys/index.html)
- Displayed designated Zone Team indicator (`A-Zone Team 15/15`) directly in the Allocation Centre matrix.
- Enabled Admin OIC to view Zone Team counts and reallocate headcount to external projects or other zones as needed.

### 3. Dashboard Allocated Headcount Banner
- **[MODIFY]** [index.html](file:///c:/Projects/CMSys/index.html)
- Added `adminAllocatedHeadcountBadge` container in the left sidebar under Available Force.
- Function `renderDashboardAllocatedHeadcountBanner()` dynamically reads saved quotas and displays the assigned trade numbers to the Zone IC (e.g. `⚡ Admin Allocated Headcount: MA:15 CA:5 WE:3`).

### 4. Standalone Window Auto-Enforcer
- **[MODIFY]** [index.html](file:///c:/Projects/CMSys/index.html)
- `initStandaloneAllocationWindow` detects `window=allocation-centre`, hides `#loginScreen`, sets Admin OIC profile, and uses `setInterval` to continuously maintain fullscreen display.

---

## Verification Checklist

- [x] Allocation Centre operates via Trade Headcount Allocation (no specific ID assignment required by Admin IC).
- [x] Phase 1 correctly categorizes Net Strength, Available Force, Temp Drafts & Out Projects, and Leave/Sick N/As.
- [x] Phase 2 Trade Matrix incorporates Zone Team counts (15 members max per zone).
- [x] Admin IC can adjust trade numbers to deploy personnel across zones/projects.
- [x] Main dashboard sidebar displays `⚡ Admin Allocated Headcount` banner to Zone ICs.
- [x] Standalone window (`?window=allocation-centre`) opens directly without asking for separate login credentials.
- [x] Editing access locked to Admin OIC and LCdr Kahandawa (SCE W/W).
