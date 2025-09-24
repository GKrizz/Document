# 📋 CMS146v13 - Appropriate Testing for Pharyngitis

### Reporting Year: **2025**

---

## 🧠 Overview

**Measure ID:** 66
**Measure Title:** CMS146v13 – *Appropriate Testing for Pharyngitis*
**Reporting Method:** eCQM (QRDA I & III)
**Provider:** Jesse Greer
**Employee ID:** 2445

This measure ensures that **antibiotics are prescribed only when appropriate**, i.e., when a **Group A Streptococcus (Strep)** test is performed for patients diagnosed with pharyngitis.

---

## 🔧 CHANGES IN 2025 (vs 2024)

### ➕ **1. Rule Logic Update (Drools)**

**Rule Modified:** `Measure 66 - Denominator Exclusionv13 - 1`

#### ✅ New Logic:

* Added validation for **Diagnosis OID** using `checkCodeAvailability(...)` to ensure the diagnosis is **Acute Pharyngitis or Acute Tonsillitis**.
* Ensured **medication start date is before diagnosis start date**.
* Checked for **overlap** between medication period and **30-day window before diagnosis**.

#### 🗑️ Removed:

* OID-based diagnosis filtering was missing previously — added explicitly in the new version.
* Any broad matching without value set validation was **tightened**.

---

### ❌ 2. Value Set (OID) Removals

| OID                                        | Description                       | 2025 Status |
| ------------------------------------------ | --------------------------------- | ----------- |
| `2.16.840.1.113883.3.464.1003.101.12.1002` | Initial Hospital Observation Care | ❌ Removed   |
| `2.16.840.1.113883.3.464.1003.101.12.1086` | Observation                       | ❌ Removed   |

These OIDs were removed from the **encounter value sets** as per 2025 CMS specifications.

---

### 🧬 3. Clinical Data Processing (QRDA I Support)

#### ✅ Updated Classes:

| File                             | Method(s) Updated                                               | Purpose                           |
| -------------------------------- | --------------------------------------------------------------- | --------------------------------- |
| `GetQDMData.java`                | `getLabTestPerformed(...)`                                      | Ensure correct lab test linkage   |
| `PatientDataSectionHandler.java` | `getLabTestComponent(...)`, `getMedicationActiveComponent(...)` | Accurate QRDA I output generation |

These ensure **QRDA I** includes the correct **lab result and medication data** for Cypress validation.

---

### 🧑‍⚕️ 4. Provider Configuration (SQL)

```sql
-- Insert Provider MIPS Configuration
INSERT INTO macra_provider_configuration (...) VALUES (
  2445, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);

-- Map Measure to Provider
INSERT INTO quality_measures_provider_mapping (...) VALUES (
  2445, '2025', '66'
);
```

---

## ✅ Cypress Test Comparison

| Source        | IPOP | DENOM | NUMER | DENEX |
| ------------- | ---- | ----- | ----- | ----- |
| **Cypress**   | 21   | 21    | 4     | 10    |
| **My Report** | 21   | 21    | 3     | 11    |

### ⚠️ Discrepancy

* Patient: **Billie Austin**

  * **Cypress Expected:** NUMER = ✅, DENEX = ❌
  * **Your Result:** NUMER = ❌, DENEX = ✅
* **Cause:** Denominator Exclusion rule was too broad — allowed patients with antibiotics even when **a Strep test was performed**.

---

## 🔬 Test Data Summary

### 🧾 Encounters

| Code  | Date       | Match |
| ----- | ---------- | ----- |
| 99213 | 2025-01-28 | ✅     |
| 99202 | 2025-02-02 | ✅     |
| 99213 | 2025-05-04 | ✅     |

### 🧾 Diagnoses

| Code (SNOMED)     | Description      | Date       | Match |
| ----------------- | ---------------- | ---------- | ----- |
| 363746003         | Pharyngitis      | 2025-05-04 | ✅     |
| 10811161000119107 | Linked Diagnosis | 2025-02-02 | ✅     |

### 🧪 Lab Test

| Code (LOINC) | Description        | Date       | Result   | Match |
| ------------ | ------------------ | ---------- | -------- | ----- |
| 11268-0      | Group A Strep Test | 2025-05-04 | Negative | ✅     |

### 💊 Medications

| RXNORM Code | Description                | Start Date | Match |
| ----------- | -------------------------- | ---------- | ----- |
| 197454      | Antibiotic for Pharyngitis | 2025-02-02 | ✅     |
| 197454      | Antibiotic for Pharyngitis | 2025-05-04 | ✅     |

---

## 🧪 QRDA Validation Results

 **QRDA I**   : ✅ Pass 
 
 ![QRDA I](https://github.com/user-attachments/assets/bcfb0f9b-13f4-4f11-9f84-6698983ac358)   
 
 **QRDA III**  : ✅ Pass
 
 ![QRDA III](https://github.com/user-attachments/assets/80bdb795-83e4-420d-a86f-f1929f9e40ec) 

---
