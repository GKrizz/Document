# 📘 MIPS Measures – Glace EHR

This README documents **how MIPS eCQM measures are mapped in Glace EHR**, focusing on:

* **CMS122v13 – Diabetes: Glycemic Status Assessment > 9%**
* **CMS138v13 – Tobacco Use: Screening and Cessation Intervention**

It explains:

* Measure intent
* UI tabs used by providers
* Backend database tables involved
* How MET / NOT MET is derived

This document is intended for **developers, QA, support, and MIPS verification**.

---

## 🧩 Measure 1: CMS122v13

### Diabetes: Glycemic Status Assessment > 9%

### 🔹 Measure Type

* **Inverse Measure**

| HbA1c Status   | Measure Result |
| -------------- | -------------- |
| HbA1c < 9%     | ❌ NOT MET      |
| HbA1c ≥ 9%     | ✅ MET          |
| HbA1c NOT DONE | ✅ MET          |

> Good glycemic control = NOT MET

---

### 🔹 Measure Logic Overview

1. Patient must have **eligible encounter**
2. Patient must have **diabetes diagnosis**
3. System checks for **HbA1c lab result**
4. MET / NOT MET decided based on HbA1c value

---

### 🖥️ UI Tabs → 🗄️ Database Tables

| Requirement          | UI Tab               | Database Table               |
| -------------------- | -------------------- | ---------------------------- |
| Eligible encounter   | Encounter            | `encounter`                  |
| CPT visit support    | Superbill            | `service_detail`             |
| Diabetes diagnosis   | Assessment           | `service_detail` (dx fields) |
| HbA1c test & value   | Investigation / Labs | ⭐ `lab_entries_parameter`    |
| Provider attribution | Encounter            | `encounter`, `emp_profile`   |

---

### 🗄️ Key Tables Explained

#### `encounter`

* Defines visit date, status, provider
* Only **completed encounters** (`encounter_status = 1`) are used

#### `service_detail`

* Stores:

  * CPT codes (office visits)
  * ICD-10 diabetes diagnoses (E10*, E11*)
* Supports denominator qualification

#### `lab_entries_parameter`

* **Source of truth for HbA1c**
* Stores:

  * HbA1c numeric value
  * Result date
* Drives numerator decision

---

## 🧩 Measure 2: CMS138v13

### Preventive Care and Screening: Tobacco Use

### 🔹 Measure Logic Overview

A patient is **MET** if:

* Tobacco use is screened **AND**

  * Patient is a **non-smoker**, OR
  * Patient is a **smoker with cessation counseling**

---

### 🖥️ UI Tabs → 🗄️ Database Tables

| Requirement                | UI Tab                   | Database Table                |
| -------------------------- | ------------------------ | ----------------------------- |
| Eligible encounter         | Encounter / Superbill    | `encounter`, `service_detail` |
| Tobacco screening          | History → Social History | `patient_clinical_findings`   |
| Smoker / non-smoker status | History                  | `patient_clinical_findings`   |
| Cessation counseling       | Plan Instructions        | `risk_assessment`             |
| Provider attribution       | Encounter                | `encounter`, `emp_profile`    |

---

### 🗄️ Key Tables Explained

#### `patient_clinical_findings`

* Stores structured answers for:

  * Smoking status
  * Tobacco use screening

#### `risk_assessment`

* Stores:

  * Tobacco cessation counseling
  * Follow-up / intervention plans

---
