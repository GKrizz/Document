# 📘 PQRS Documentation (Chart → Final Output)

## 📌 Purpose

**PQRS Documentation** enables providers who report via **Claims** or **Registry** to document **quality measures for a patient** during a reporting period.

* **Claims / Registry providers** → use **PQRS**
* **EHR providers** → use **MACRA** (PQRS tab not applicable)

---

## 🧭 End-to-End Flow Overview

```
Patient Chart
 → PQRS Tab
 → Configured Measures
 → CPT Selection
 → Document Action
 → Storage (PQRS + optional Superbill)
 → Tooltip / Audit
```

---

## 🟦 Step 1: Patient Chart Entry

### Source Tables

* `patient_registration` – patient demographics
* `chart` – patient chart container
* `encounter` – visit context (DOS, provider)

### Purpose

* Identify **patient**
* Identify **provider**
* Identify **date of service (DOS)**

---

## 🟦 Step 2: Determine Reporting Mode

### Logic

* If **Provider Reporting Type = Claims or Registry**

  * 👉 **PQRS tab is enabled**
* If **Provider Reporting Type = EHR**

  * 👉 **MACRA workflow is used instead**

---

## 🟦 Step 3: Load Configured Measures

### Source Table

```text
quality_measures_provider_mapping
```

### Purpose

* Determines **which measures** are available for the provider
* Only mapped measures appear in the **PQRS tab**

📌 If a measure is **not mapped**, it will **not appear**.

---

## 🟦 Step 4: PQRS Tab – Measure & CPT Selection

### Rules

* Each measure requires **exactly ONE CPT**
* Provider selects:

  * Measure
  * Corresponding CPT

### UI Action

* Click **Document**

---

## 🟦 Step 5: Document CPT (Core Action)

When **Document** is clicked:

### Always happens

* Selected CPT is saved in:

```text
pqrs_patient_entries
```

This table stores:

* Patient ID
* Provider ID
* Measure ID
* CPT
* Date of Service
* Reporting metadata

📌 This is the **source of truth** for PQRS reporting.

---

## 🟦 Step 6: Behavior Based on Reporting Type

### 🔹 Claims Reporting

* CPT is:

  * Saved in `pqrs_patient_entries`
  * **Also added to Superbill** (`service_detail`)

📌 Required for claims submission to CMS.

---

### 🔹 Registry Reporting

* CPT is:

  * Saved in `pqrs_patient_entries`
  * ❌ **NOT added to Superbill**

📌 Registry submission does not require billing CPTs.

---

## 🟦 Step 7: Report Again or Cancel

After documenting a CPT:

### 🔁 Report Again

* Allows provider to:

  * Report the **same measure again**
  * Use a **different CPT**
  * On the **same DOS**

### ❌ Cancel

* Stops reporting for that measure
* No further CPTs recorded

---

## 🟦 Step 8: Tooltip & Audit Visibility

For each documented PQRS entry, UI tooltip shows:

* **Provider Name**
* **Date of Service (DOS)**

### Source

* `pqrs_patient_entries`
* `emp_profile`

📌 Used for:

* Verification
* Audits
* Support review

---

## 🗂️ Key Tables Summary

| Purpose                   | Table                               |
| ------------------------- | ----------------------------------- |
| Provider → Measure config | `quality_measures_provider_mapping` |
| PQRS documentation        | `pqrs_patient_entries`              |
| Claims billing CPT        | `service_detail`                    |
| Patient details           | `patient_registration`              |
| Provider details          | `emp_profile`                       |

---

## ✅ Completion Criteria

PQRS documentation is considered **complete** when:

* CPT is saved in `pqrs_patient_entries`
* (If Claims) CPT is added to Superbill
* Tooltip shows provider & DOS
* Measure status is visible in PQRS tab

---

## 🧠 Important Rules & Notes

* PQRS ≠ MACRA (different workflows)
* PQRS CPTs are **explicitly documented**, not inferred
* Registry reporting **never affects billing**
* Superbill updates happen **only for Claims**
* Same measure can be reported multiple times (Report Again)

---

## 🧾 One-Line Summary

> **PQRS Documentation allows Claims and Registry providers to explicitly document quality measures by selecting CPTs in the PQRS tab. CPTs are always stored in `pqrs_patient_entries` and are added to the Superbill only for Claims-based reporting.**

---
