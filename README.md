# notion-status-change-handler
  A Make.com automation that watches a Notion database and triggers on status change to Done , then sends a Slack alert, logs to Google Sheets, and blocks duplicate triggers via Data Store. Built with a filter, router, and ifempty() fallbacks.
# 🔔 Notion Status Change Handler

> **Make.com automation** · Built by Gloria Njorteah · April 2026

An end-to-end automation that watches a Notion database for status changes, fires a Slack notification when a request is marked **Done**, logs every event to Google Sheets, and uses a Data Store to block duplicate triggers — completely hands-free.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Workflow Diagram](#workflow-diagram)
- [How It Works](#how-it-works)
- [Module Breakdown](#module-breakdown)
- [Screenshots](#screenshots)
- [Test Results](#test-results)
- [Tools Used](#tools-used)
- [Assignment Brief](#assignment-brief)

---

## Overview

| Field | Detail |
|---|---|
| **Platform** | Make.com |
| **Trigger** | Notion — Watch Requests Database (every 15 min) |
| **Actions** | Slack notification · Google Sheets log · Data Store record |
| **Protection** | Duplicate trigger blocking via Make.com Data Store |
| **Status** | ✅ Live — tested and passing |

---

## Workflow Diagram

### Full Scenario Canvas — All Modules Green ✅

![Full Make.com Scenario — All Modules Active](./screenshots/workflow_full.jpg)

> Complete Make.com scenario showing all 6 modules active and passing.
> **Trigger → Filter → Router → Slack → Google Sheets → Data Store**

---

## How It Works

```
Notion DB updated
       │
       ▼
┌─────────────────┐
│  Watch Requests │  Polls every 15 minutes for updated records
│  Database       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Filter:        │  Stops execution if Status ≠ Done
│  Status is Done │  Only "Done" records proceed
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Router         │  Branch 1 → Done records
│  (4 branches)   │  Branch 2 → Fallback
└────────┬────────┘
         │
    ┌────┴────────────────┬───────────────────┐
    ▼                     ▼                   ▼
┌──────────┐      ┌──────────────┐    ┌──────────────┐
│  Slack   │      │ Google       │    │  Data Store  │
│ Notify   │      │ Sheets Log   │    │ Save Record  │
│ #auto-   │      │ Add a Row    │    │ Add/replace  │
│ test     │      │              │    │ a record     │
└──────────┘      └──────────────┘    └──────────────┘
```

---

## Module Breakdown

### 1. Watch Requests Database (Notion)
- Polls the `Requests` database every **15 minutes** for updated records
- Passes each updated record as a bundle to the next step
- Fields tracked: `Title`, `Status`, `Owner`, `Updated at`

### 2. Filter — Status is Done
- Checks that the record's `Status` field equals **Done**
- Stops execution immediately if the status is anything else
- Only confirmed Done records reach the Router

### 3. Router
- Splits the flow into conditional branches
- **Branch 1** — handles confirmed Done records (fires all 3 actions)
- **Branch 2** — fallback for any other status that passes the initial filter

### 4. Notify Slack — Request Completed
- Sends a formatted message to **#automation-test**
- Message format:
  ```
  Request completed
  Title: [Title]
  Owner: [Owner]
  Completed at: [Updated at]
  ```
- Uses `ifempty()` fallback — substitutes **"Unknown"** if Title or Owner is blank

### 5. Log to Automation Sheet (Google Sheets)
- Appends a new row to the **Automation Log** spreadsheet
- Columns populated: `Title` · `Owner` · `Status` · `Timestamp`
- Uses `ifempty()` to handle blank fields without breaking the scenario

### 6. Save Processed Record (Data Store)
- Saves the record ID to the **Processed Requests** Data Store
- Future runs check this store — if the ID already exists, the scenario does **not** re-fire
- This is what makes duplicate protection work ✅

---

## Screenshots

### 🔵 Filter Module — Status is Done

![Filter Module Note](./screenshots/filter_module_note.jpg)

> The filter stops execution if the record's Status is not Done. Only Done records proceed to the router.

---

### 🟢 Automation Log — Google Sheets

![Google Sheets Automation Log](./screenshots/automation_log.jpg)

> The Automation Log sheet showing all logged rows with Title, Owner, Status, and Timestamp columns correctly populated by the scenario.

---

### 🔴 Duplicate Protection — Test Run 2

![Duplicate Protection Blocks Re-trigger](./screenshots/duplicate_protection.jpg)

> Test Run 2: Status is Done filter shows **0 bundles passed** (highlighted in red). No Slack message sent, no Sheet row added — re-trigger successfully blocked.

---

## Test Results

### Test Run 1 — Trigger fires correctly ✅

| Field | Value |
|---|---|
| Record | Test Request 5 |
| Owner | Ofeng Obong |
| Status | Done |
| Slack message sent | ✅ Yes — to #automation-test |
| Sheet row added | ✅ Yes — Automation Log updated |
| Data Store saved | ✅ Yes — Record ID stored |
| All modules | 🟢 Green |

**Slack message received:**
```
Request completed
Title: Test Request 5
Owner: Ofeng Obong
Completed at: 2026-04-04
```

---

### Test Run 2 — Duplicate protection blocks re-trigger ✅

| Field | Value |
|---|---|
| Record | Same record as Run 1 |
| Filter result | 0 bundles passed |
| Slack message sent | ❌ Blocked |
| Sheet row added | ❌ Blocked |
| Data Store | Already contained the record ID |
| Result | 🛡️ Re-trigger successfully prevented |

---

## Tools Used

| Tool | Purpose |
|---|---|
| ![Make.com](https://img.shields.io/badge/Make.com-6D00CC?style=flat&logo=make&logoColor=white) | Automation platform — scenario builder |
| ![Notion](https://img.shields.io/badge/Notion-000000?style=flat&logo=notion&logoColor=white) | Source database — Requests tracked here |
| ![Slack](https://img.shields.io/badge/Slack-4A154B?style=flat&logo=slack&logoColor=white) | Team notification channel #automation-test |
| ![Google Sheets](https://img.shields.io/badge/Google_Sheets-34A853?style=flat&logo=googlesheets&logoColor=white) | Automation Log — persistent event record |
| **Make.com Data Store** | Duplicate protection — stores processed record IDs |

---

## Assignment Brief

This project was built as part of a Make.com automation assignment. The requirements were:

- ✅ Watch a Notion `Requests` database (fields: Title, Status, Owner, Updated at)
- ✅ Fire only when `Status` equals **Done**
- ✅ Send a formatted Slack message to `#automation-test`
- ✅ Log Title, Owner, Status, and Timestamp to Google Sheets
- ✅ Use a **filter** to check Status equals Done
- ✅ Use a **router** with at least one conditional branch
- ✅ Handle empty Title or Owner fields with `ifempty()` fallback ("Unknown")
- ✅ Prevent duplicate triggers using Data Store logic
- ✅ Test with at least 2 runs — one firing correctly, one blocked by duplicate protection

---

## File Structure

```
notion-status-change-handler/
│
├── README.md                          ← You are here
│
└── screenshots/
    ├── workflow_full.jpg              ← Full scenario canvas, all modules green
    ├── filter_module_note.jpg         ← Filter — Status is Done module
    ├── automation_log.jpg             ← Google Sheets Automation Log
    └── duplicate_protection.jpg       ← Test Run 2 — duplicate block
```

> **How to use screenshots:** Create a `screenshots/` folder in your repo root and upload the images with the filenames above. The README will display them automatically on the GitHub front page.

---

## Author

**Gloria Njorteah** · Make.com Automation · April 2026

---

*Built with Make.com — no-code automation that actually works.*
