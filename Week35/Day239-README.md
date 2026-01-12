
# 📘 README — CMS2v14

## Preventive Care and Screening: Screening for Depression and Follow-Up Plan

**Measure ID:** 134
**Reporting Year:** 2025
**Measure Type:** Behavioral Health Measure
**NCQA AR Criterion:** AR-QI 01

---

## 1️⃣ Measure Overview

**CMS2v14** evaluates whether patients aged **12 years and older** were:

* **Screened for depression** using a standardized tool (PHQ), **AND**
* Received **appropriate follow-up** if the screening result was **positive**

This measure applies to **patients with at least one qualifying encounter** during the measurement period.

---

## 2️⃣ Measurement Period

| Item               | Value           |
| ------------------ | --------------- |
| Measurement Year   | **2025**        |
| Start Date         | **01-Jan-2025** |
| End Date           | **31-Dec-2025** |
| Age Reference Date | **01-Jan-2025** |

---

## 3️⃣ Provider Mapping

Only providers mapped to **Measure 134** and **active for 2025** are considered.

### Provider Mapping Query

```sql
SELECT
  qmpm.quality_measures_provider_mapping_provider_id AS provider_id,
  ep.emp_profile_fullname AS provider_name,
  qmpm.quality_measures_provider_mapping_reporting_year AS year
FROM quality_measures_provider_mapping qmpm
JOIN macra_provider_configuration pc
  ON pc.macra_provider_configuration_provider_id = qmpm.quality_measures_provider_mapping_provider_id
 AND pc.macra_provider_configuration_reporting_year = qmpm.quality_measures_provider_mapping_reporting_year
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = qmpm.quality_measures_provider_mapping_provider_id
WHERE qmpm.quality_measures_provider_mapping_measure_id = '134'
  AND qmpm.quality_measures_provider_mapping_reporting_year = 2025;
```

✅ **Active Providers Used:** 3

---

## 4️⃣ Initial Population (IPP)

### IPP Definition

A patient is included in **IPP** if:

* Patient exists in `quality_measures_patient_entries`
* Measure = **134**
* Reporting Year = **2025**
* Provider mapped to the measure

### IPP Count

| Metric  | Count   |
| ------- | ------- |
| **IPP** | **593** |

---

## 5️⃣ Age Criteria (IPP Validation)

### Rule

* Patient must be **≥ 12 years old** as of **01-Jan-2025**

### Age Validation Query

```sql
EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) >= 12
```

### Result

| Metric         | Count |
| -------------- | ----- |
| Age ≥ 12 (raw) | 601   |
| Final IPP      | 593   |

✅ **All IPP patients meet age criteria**

---

## 6️⃣ Denominator (DENOM)

### Denominator Rule

Patient must have **at least one qualifying encounter** during 2025 with any of the following CPT codes:

```
99201–99205
99212–99215
99384–99387
99394–99397
97003
96150, 96151
96116, 96118
92625
90791, 90792
90832, 90834, 90837
```

### Encounter Requirements

* Encounter date between **01-Jan-2025** and **31-Dec-2025**
* Encounter provider = mapped provider

### Denominator Count

| Metric    | Count   |
| --------- | ------- |
| **DENOM** | **593** |

➡️ For CMS2, **IPP = DENOM** when all IPP patients have qualifying encounters.

---

## 7️⃣ Denominator Exclusions (DENEX)

### DENEX Rule

Exclude patients who have **ANY history of Bipolar Disorder** **before** their **first qualifying encounter**.

#### Diagnosis Sources

| Source       | Table                 |
| ------------ | --------------------- |
| Encounter DX | `patient_assessments` |
| Chronic DX   | `problem_list`        |

#### Diagnosis Codes

ICD-10 codes starting with:

```
F30.*
F31.*
```

### Key Rule

* Diagnosis date must be **before** the qualifying encounter date
* Diagnosis may occur **any time in the past**
* Inactive diagnoses still count

### DENEX Count

| Metric    | Count |
| --------- | ----- |
| **DENEX** | **1** |

### Example DENEX Patient

| Patient ID | First Encounter | Bipolar DX Date | Source                  |
| ---------- | --------------- | --------------- | ----------------------- |
| 4370       | 2025-05-21      | 2020-02-19      | Assessment Encounter DX |

---

## 8️⃣ Final Denominator

| Metric          | Count   |
| --------------- | ------- |
| DENOM           | 593     |
| DENEX           | 1       |
| **Final DENOM** | **592** |

---

## 9️⃣ Numerator (NUM)

### Numerator Rule

Patient must meet **ONE** of the following:

#### ✔ Option A — Negative Screening

* Most recent PHQ score during 2025 is **negative**
* No follow-up required

#### ✔ Option B — Positive Screening + Follow-Up

* PHQ score **≥ 5**
* **Follow-up documented** within required timeframe

---

### Age-Based Screening Logic

| Age   | Allowed Screening               |
| ----- | ------------------------------- |
| 12–16 | PHQ-Adolescent                  |
| 17    | PHQ-Adolescent **or** PHQ-Adult |
| ≥18   | PHQ-Adult                       |

---

## 🔍 Important Finding — PHQ-2 vs PHQ-9

### ❌ PHQ-2 Issue

* **PHQ-2** does **NOT store a numeric score** in `risk_assessment_result_value`
* CMS2 numerator logic **requires a score**
* Therefore, **PHQ-2 alone does NOT satisfy numerator**

### ✅ PHQ-9 Fix

* **PHQ-9** stores a numeric score
* Negative PHQ-9 correctly satisfies numerator

---

## 🧪 Example Test Patient (3052)

| Field                | Value                     |
| -------------------- | ------------------------- |
| DOB                  | 2000-10-08                |
| Age (01-Jan-2025)    | 24                        |
| Qualifying Encounter | 27-Oct-2025 (99213)       |
| PHQ-2                | Negative (no score) ❌     |
| PHQ-9                | Negative (score stored) ✅ |

### Measure Entry Result

| IPP | DENOM | DENEX | NUM                   |
| --- | ----- | ----- | --------------------- |
| 1   | 1     | 0     | **0 → 1 after PHQ-9** |

---

## 10️⃣ Final Measure Summary

| Metric      | Value            | Status               |
| ----------- | ---------------- | -------------------- |
| IPP         | 593              | ✅                    |
| DENOM       | 593              | ✅                    |
| DENEX       | 1                | ✅                    |
| FINAL DENOM | 592              | ✅                    |
| NUMERATOR   | Depends on PHQ-9 | ⚠️ PHQ-2 alone fails |

---

## ✅ Key Takeaways

* **PHQ-2 alone is insufficient** for CMS2 numerator
* **PHQ-9 (Adult)** or **Adolescent PHQ with score** is required
* Always validate:

  * Age logic
  * Qualifying encounter
  * Bipolar exclusions
  * Numeric PHQ score presence

---
