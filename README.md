# 🔔 Notion Status Change Handler

**Make.com automation · Built by Gloria Njorteah · April 2026**

An end-to-end automation that watches a Notion database for status changes, fires a Slack notification when a request is marked **Done**, logs every event to Google Sheets, and uses a Data Store to block duplicate triggers — completely hands-free.

---

## 📌 Project Overview

| Field | Detail |
|---|---|
| **Platform** | Make.com |
| **Trigger** | Notion — Watch Requests Database (every 15 min) |
| **Actions** | Slack notification · Google Sheets log · Data Store record |
| **Protection** | Duplicate trigger blocking via Make.com Data Store |
| **Status** | ✅ Live — tested and passing |

---

## 🗂️ Table of Contents

- [Full Workflow](#full-workflow)
- [How It Works](#how-it-works)
- [Module Breakdown](#module-breakdown)
- [Test Results](#test-results)
- [Tools Used](#tools-used)
- [Assignment Checklist](#assignment-checklist)
- [Author](#author)

---

## 🔁 Full Workflow

![Full Make.com Scenario — All Modules Active and Green](Full%20test%20run_%20recent.jpg)

> All 6 modules active and passing — **Watch DB → Filter → Router → Slack → Google Sheets → Data Store**

---

## ⚙️ How It Works

When a record in the Notion `Requests` database is updated, Make.com checks if the `Status` field has changed to **Done**. If yes, the scenario fires three actions in sequence — a Slack alert, a Google Sheets log entry, and a Data Store save to prevent the same record from triggering the automation again on future runs.

If the record has already been processed (its ID exists in the Data Store), the filter catches it and **0 bundles pass** — no duplicate notifications, no duplicate log rows.

---

## 🧩 Module Breakdown

### 1 · Watch Requests Database — Notion
Polls the `Requests` database every 15 minutes for updated records. Passes each updated record as a bundle to the next step. Fields tracked: `Title`, `Status`, `Owner`, `Updated at`.

![Notion Requests Database](Notion_requests_database_jpg.jpg)
---

### 2 · Filter — Status is Done

![Filter Module — Status is Done](Filter%20Module%20_Note.JPG)

Checks that the record's `Status` field equals **Done**. Stops execution immediately if the status is anything else. Only confirmed Done records reach the Router.

---

### 3 · Router
Splits the flow into conditional branches. Branch 1 handles confirmed Done records and fires all 3 downstream actions. Branch 2 is a fallback for any other status that passes the initial filter.

---

### 4 · Notify Slack — Request Completed

![Slack Notification — #automation-test](Slack%20notifcation_new.JPG)

---

### 5 · Log to Automation Sheet — Google Sheets

![Google Sheets Automation Log](Automation%20log_new.jpg)

Appends a new row to the **Automation Log** spreadsheet with columns: `Title` · `Owner` · `Status` · `Timestamp`. Uses `ifempty()` to handle blank fields without breaking the scenario.

---

### 6 · Save Processed Record — Data Store
Saves the record ID to the **Processed Requests** Data Store. Future runs check this store — if the ID already exists, the scenario does **not** re-fire. This is what makes duplicate protection work.

---

## 🧪 Test Results

### Test Run 1 — Trigger fires correctly ✅

| Field | Result |
|---|---|
| Record | Test Request 3 — Joe Keshi |
| Status | Done |
| Slack message sent | ✅ Yes — to #automation-test |
| Sheet row added | ✅ Yes — Automation Log updated |
| Data Store saved | ✅ Yes — Record ID stored |
| All modules | 🟢 Green |

---

### Test Run 2 — Duplicate protection blocks re-trigger ✅

![Duplicate Protection — 0 Bundles Passed](duplicate%20protection%20blocks%20a%20re-trigger%20.jpg)

| Field | Result |
|---|---|
| Record | Same record as Run 1 |
| Filter result | **0 bundles passed** |
| Slack message sent | ❌ Blocked |
| Sheet row added | ❌ Blocked |
| Data Store | Already contained the record ID |
| Outcome | 🛡️ Re-trigger successfully prevented |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| ![Make.com](https://img.shields.io/badge/Make.com-6D00CC?style=flat&logo=make&logoColor=white) | Automation platform — scenario builder |
| ![Notion](https://img.shields.io/badge/Notion-000000?style=flat&logo=notion&logoColor=white) | Source database — Requests tracked here |
| ![Slack](https://img.shields.io/badge/Slack-4A154B?style=flat&logo=slack&logoColor=white) | Team notification — #automation-test channel |
| ![Google Sheets](https://img.shields.io/badge/Google_Sheets-34A853?style=flat&logo=googlesheets&logoColor=white) | Automation Log — persistent event record |
| **Make.com Data Store** | Duplicate protection — stores processed record IDs |

---

## ✅ Assignment Checklist

- ✅ Watch a Notion `Requests` database — Title, Status, Owner, Updated at
- ✅ Fire only when `Status` equals **Done**
- ✅ Send a formatted Slack message to `#automation-test`
- ✅ Log Title, Owner, Status, Timestamp to Google Sheets
- ✅ Use a **filter** to check Status equals Done
- ✅ Use a **router** with at least one conditional branch
- ✅ Handle empty fields with `ifempty()` fallback → "Unknown"
- ✅ Prevent duplicate triggers using Data Store logic
- ✅ Two test runs — one firing correctly, one blocked by duplicate protection

---

## 👩🏾‍💻 Author

**Gloria Njorteah** · Make.com Automation · April 2026

*Built with Make.com — no-code automation that actually works.*
