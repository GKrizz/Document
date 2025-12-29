# 📘 Day 219 – December 23, 2025

## HbA1c Validation & Measure Analysis (CMS122 / CMS2 / CMS146)

---

## 🔬 HbA1c Measure Analysis (2025)

### 📊 Aggregate Results

| Category  | Row Count |
| --------- | --------- |
| HbA1c < 9 | **2652**  |
| HbA1c ≥ 9 | **248**   |
| **Total** | **2900**  |

> Minor delta observed between UI counts (2660 / 249) and DB results due to unmapped or invalid parameters.

---

## 1️⃣ Unmapped HbA1c Parameters Check

### Objective

Identify **HbA1c lab parameters** that exist but are **not mapped to valid LOINC codes**, potentially causing misclassification.

```sql
SELECT
    lab_entries_parameter_name,
    lab_entries_parameter_code,
    COUNT(*) AS total_rows
FROM lab_entries_parameter
WHERE lab_entries_parameter_isactive = true
  AND lab_entries_parameter_name ILIKE '%A1C%'
  AND (
        lab_entries_parameter_code IS NULL
        OR TRIM(lab_entries_parameter_code) = ''
        OR lab_entries_parameter_code = '0'
        OR lab_entries_parameter_code NOT LIKE '%-%'
      )
GROUP BY lab_entries_parameter_name, lab_entries_parameter_code;
```

### Findings

| Parameter Name             | Code  | Count |
| -------------------------- | ----- | ----- |
| Hb A1c Diabetic Assessment | NULL  | 8     |
| HEMOGLOBIN A1c             | 37292 | 2     |
| HEMOGLOBIN A1c             | NULL  | 4     |

⚠ **Unmapped parameters can falsely inflate HbA1c ≥ 9 counts**

---

## 2️⃣ Validate Allowed HbA1c LOINC Codes

### Allowed CMS HbA1c Codes

```
17855-8, 17856-6, 4548-4, 4549-2, 96595-4, 97506-0
```

```sql
SELECT DISTINCT
    lab_entries_parameter_code,
    lab_entries_parameter_name
FROM lab_entries_parameter
WHERE lab_entries_parameter_isactive = true
  AND lab_entries_parameter_name ILIKE '%A1C%'
  AND lab_entries_parameter_date BETWEEN '2025-01-01' AND '2025-12-31'
ORDER BY lab_entries_parameter_code;
```

### Result

| LOINC Code | Name           |
| ---------- | -------------- |
| 4549-2     | Hemoglobin A1c |

✅ Only **valid CMS-accepted LOINC codes** are used in measure calculation.

---

## 3️⃣ CMS Rule: Most Recent HbA1c → Lowest Value on That Date

> CMS requires selecting the **lowest numeric HbA1c value on the most recent test date**.

```sql
-- Latest date + lowest value logic
```

✔ Ensures correct classification of patients with multiple results on the same day.

---

## 4️⃣ Identify “False HbA1c ≥ 9” Patients

### Logic

Patients whose **latest HbA1c < 9** but are still classified as ≥ 9.

```sql
SELECT
  patient_id,
  a1c_date,
  a1c_value
FROM ranked_a1c
WHERE rnk = 1
  AND a1c_value < 9;
```

🔍 Used to validate numerator/denominator mismatches.

---

## 5️⃣ Patients with HbA1c Data but Unmapped Codes

```sql
SELECT DISTINCT
    lab_entries_parameter_chartid AS patient_id,
    lab_entries_parameter_name,
    lab_entries_parameter_value,
    lab_entries_parameter_date,
    lab_entries_parameter_code
FROM lab_entries_parameter
WHERE lab_entries_parameter_isactive = true
  AND lab_entries_parameter_name ILIKE '%A1C%'
  AND (
        lab_entries_parameter_code IS NULL
        OR TRIM(lab_entries_parameter_code) = ''
        OR lab_entries_parameter_code = '0'
        OR lab_entries_parameter_code NOT LIKE '%-%'
      )
ORDER BY lab_entries_parameter_date DESC;
```

### Sample Findings

| Patient ID | Value | Date       | Code |
| ---------- | ----- | ---------- | ---- |
| 6419       | 9.3   | 2022-06-02 | NULL |
| 14226      | 6.0   | 2022-06-02 | NULL |
| 9416       | 10.4  | 2015-04-01 | NULL |

⚠ These patients are **excluded from CMS calculation** due to invalid mapping.

---

# 📗 CMS2v14 – Depression Screening and Follow-Up

**(Patient-Based Measure)**

### IPP / Denominator

* Age ≥ **12**
* ≥ 1 qualifying encounter

### Denominator Exclusion

* **Any history of Bipolar Disorder**
* Diagnosis can occur **any time prior**

---

### Numerator (ANY ONE required)

#### Case A – Negative Screening

* PHQ-9 / PHQ-2
* Result = Negative
* No follow-up required
  ✅ **MET**

#### Case B – Positive Screening + Follow-up

* Follow-up within **2 days**

  * Counseling
  * Referral
  * Medication
    ✅ **MET**

❌ **NOT MET**

* No screening
* Positive without follow-up
* Outside time window

---

### Timing Rules

| Event     | Allowed Time                       |
| --------- | ---------------------------------- |
| Screening | Encounter date OR ≤ 14 days before |
| Follow-up | Encounter date OR ≤ 2 days after   |

---

### Data Mapping

| Step       | UI              | Table                               |
| ---------- | --------------- | ----------------------------------- |
| Encounter  | Superbill       | `service_detail`                    |
| Bipolar Dx | Assessment      | `service_detail`                    |
| Screening  | Screening / HPI | `patient_clinical_findings`         |
| Metadata   | Screening       | `risk_assessment`                   |
| Follow-up  | Plan / Meds     | `risk_assessment`, `service_detail` |

---

# 📕 CMS146v13 – Appropriate Testing for Pharyngitis

**(Episode-Based Measure)**

### Core Logic

* Age ≥ **3**
* Qualifying encounter
* Pharyngitis diagnosis
* Antibiotic prescribed
* **Group A Strep test within ±3 days**

❌ No strep test → **Numerator = 0**

---

