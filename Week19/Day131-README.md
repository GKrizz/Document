# 🚀 Day 131 Progress Report

This document captures the detailed progress made on Day 131, including the review and remediation of findings related to **Catching Generic Exceptions** and **CWE-117/93 – Log Injection**. Each entry outlines the issue, suggested remediation, current status, and developer notes.

---

## 🧾 Section 1: Catching Generic Exceptions

> **Problem**: Catching broad exceptions (e.g., `Exception`, `Throwable`) can mask real issues and hinder debugging. Specific exceptions should be caught explicitly to allow better error handling and reporting.

### 🔍 Summary Table

| S.No | File Path | Exception Type | Status | Module | Developer | Notes |
|------|-----------|----------------|--------|--------|-----------|-------|
| 1 | `CurrentMedicationEntryServiceImpl.java` | `DrugNotFoundException` | ✅ Done | chart | Vignesh | - |
| 2 | `PointClickCareServiceImpl.java` | `RateLimitExceededException` | ❌ Dont change | ppc | Parthipan / Sharik | Already caught specifically at UI level |
| 3 | `PointClickCareServiceImpl.java` | `RateLimitExceededException` | ❌ Dont change | ppc | Parthipan / Sharik | - |
| 4 | `PointClickCareServiceImpl.java` | `RateLimitExceededException` | ❌ Dont change | ppc | Parthipan / Sharik | - |
| 5 | `PrescriptionEntryServiceImpl.java` | `DrugNotFoundException` | ✅ Done | chart | Vignesh | - |
| 6 | `PrescriptionEntryServiceImpl.java` | `DrugNotFoundException` | ⏳ Pending | chart | - | - |
| 7 | `DirectMailServiceImpl.java` | `IOException` | ⏳ Pending | - | - | - |
| 8 | `DirectMailController.java` | `IOException` | ⏳ Pending | - | - | - |

---

## 🛡️ Section 2: CWE-117, CWE-93 – Log Injection

> **Problem**: Unsanitized user input in logs can lead to **log injection**, which can manipulate logs, forge entries, or bypass monitoring tools. Sanitization is essential before logging dynamic input.

### 🧠 Summary Table

| S.No | File Path | Input Source | Status | Module | Developer | Notes |
|------|-----------|--------------|--------|--------|-----------|-------|
| 1 | `InvestigationController.java` | `barcode` | ✅ Yes | Investigation | Ram Kumar | - |
| 2 | `ChartSummaryController.java` | `reqBodyObj` | ❌ No | Chart | Santhosh | Logging removed per Santhosh Sir’s instruction – barcode logging not needed |

---
