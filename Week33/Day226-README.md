# 📘 CMS146v13 – Appropriate Testing for Pharyngitis

**Measure ID: 66 | Reporting Year: 2025**

---

## 📌 Measure Overview

**CMS146v13** evaluates whether patients diagnosed with **pharyngitis or tonsillitis** and prescribed an **antibiotic** received a **Group A Streptococcus (GAS) test** within the required timeframe.

### Core Logic

* **IPP:** Eligible outpatient encounter
* **Denominator:** Pharyngitis + Antibiotic
* **Numerator:** Group A Strep test performed within ±3 days of encounter
* **Denominator Exclusions:** Comorbid respiratory conditions, prior antibiotics, competing diagnoses, hospice, etc.

---

## 🧪 Validation Scenarios Covered

This README documents **two real patient scenarios**:

| Scenario              | Patient ID | Outcome                         |
| --------------------- | ---------- | ------------------------------- |
| Numerator Met         | **2942**   | IPP=1, DEN=1, NUM=1             |
| Denominator Exclusion | **2971**   | IPP=1, DEN=1, DEN EXCL=1, NUM=0 |

---

# ✅ SCENARIO 1 — NUMERATOR MET

### Patient ID: **2942**

---

## 1️⃣ Age Criteria (≥ 3 years)

```sql
SELECT
  patient_registration_id,
  EXTRACT(YEAR FROM AGE('2025-01-01', patient_registration_dob)) AS age
FROM patient_registration
WHERE patient_registration_id = 2942;
```

**Result**

```
Age = 4
```

✅ Pass

---

## 2️⃣ Eligible Encounter (IPP)

**Valid CPTs present on DOS**

```sql
SELECT
  sd.service_detail_dos,
  c.cpt_cptcode
FROM service_detail sd
JOIN cpt c ON c.cpt_id = sd.service_detail_cptid
WHERE sd.service_detail_patientid = 2942
  AND sd.service_detail_sdoctorid = 1
  AND sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-31';
```

**Result**

```
99341 → 2025-12-11
99342 → 2025-12-11
```

➡️ **IPP = 1**

---

## 3️⃣ Pharyngitis Diagnosis

```sql
SELECT patient_assessments_dxcode
FROM patient_assessments
WHERE patient_assessments_patientid = 2942;
```

**Result**

```
J02.0
J02.8
```

➡️ **DENOMINATOR = 1**

---

## 4️⃣ Antibiotic Prescribed

```sql
SELECT
  current_medication_rx_name,
  current_medication_start_date
FROM current_medication
WHERE current_medication_patient_id = 2942
  AND current_medication_is_active = true;
```

**Result**

```
Amoxicillin  → 2025-12-11
Minocycline → 2025-12-12
```

✅ Stays in denominator

---

## 5️⃣ Group A Strep Test (Numerator)

**Encounter Date:** 2025-12-11
**Valid Window:** 2025-12-08 → 2025-12-14
**chartId:** 2055

```sql
SELECT
  lab_entries_test_desc,
  lab_entries_perf_on
FROM lab_entries
WHERE lab_entries_chartid = 2055
  AND lab_entries_perf_on
      BETWEEN '2025-12-08' AND '2025-12-14';
```

**Result**

```
STREPTOCOCCUS, GROUP A CULTURE
2025-12-10
```

➡️ **NUMERATOR = 1**

---

## ✅ Final System Output (Patient 2942)

```sql
SELECT
  quality_measures_patient_entries_ipp,
  quality_measures_patient_entries_denominator,
  quality_measures_patient_entries_numerator
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_patient_id = 2942
  AND quality_measures_patient_entries_measure_id = '66'
  AND quality_measures_patient_entries_reporting_year = 2025;
```

```
IPP = 1 | DEN = 1 | NUM = 1
```

---

# 🚫 SCENARIO 2 — DENOMINATOR EXCLUSION

### Patient ID: **2971**

---

## Patient & Provider Details

| Field          | Value            |
| -------------- | ---------------- |
| Patient Name   | TEST TEST        |
| Chart ID       | 2084             |
| Provider       | Test Doctor M.D. |
| CPT            | 99350            |
| Encounter Date | 2025-10-10       |

---

## Key Findings

| Condition                      | Status |
| ------------------------------ | ------ |
| Age ≥ 3                        | ✅      |
| Eligible CPT                   | ✅      |
| Pharyngitis DX (J02.9)         | ✅      |
| Antibiotic Ordered             | ✅      |
| Group A Strep Test             | ❌      |
| **Comorbid Condition (J84.9)** | ✅      |

➡️ **Denominator Exclusion applies**

---

## Final Measure Result (Patient 2971)

```sql
SELECT
  quality_measures_patient_entries_ipp,
  quality_measures_patient_entries_denominator,
  quality_measures_patient_entries_denominator_exclusion,
  quality_measures_patient_entries_numerator
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_patient_id = 2971
  AND quality_measures_patient_entries_measure_id = '66'
  AND quality_measures_patient_entries_reporting_year = 2025;
```

| IPP | DEN | DEN EXCL | NUM |
| --- | --- | -------- | --- |
| 1   | 1   | **1**    | 0   |

✅ Correct CMS behavior

---

# 📊 Provider-Level Report Validation

```sql
SELECT
    qmpe.quality_measures_patient_entries_patient_id AS patient_id,
    qmpe.quality_measures_patient_entries_ipp AS ipp,
    qmpe.quality_measures_patient_entries_denominator AS denominator,
    qmpe.quality_measures_patient_entries_denominator_exclusion AS exclusion,
    qmpe.quality_measures_patient_entries_numerator AS numerator
FROM quality_measures_patient_entries qmpe
WHERE qmpe.quality_measures_patient_entries_measure_id = '66'
  AND qmpe.quality_measures_patient_entries_reporting_year = 2025
ORDER BY patient_id;
```

| Patient  | IPP | DEN | EXCL  | NUM |
| -------- | --- | --- | ----- | --- |
| 1529     | 1   | 1   | 0     | 1   |
| 2942     | 1   | 1   | 0     | 1   |
| **2971** | 1   | 1   | **1** | 0   |

---

# 🧾 FINAL CORRECT QUERY (Encounter-Level)

```sql
SELECT
    pr.patient_registration_id AS patient_id,
    pr.patient_registration_first_name || ' ' ||
    pr.patient_registration_last_name AS patient_name,

    EXTRACT(YEAR FROM AGE(sd.service_detail_dos,
                          pr.patient_registration_dob)) AS patient_age,

    ep.emp_profile_fullname AS provider_name,

    'CMS146v13 – Appropriate Testing for Pharyngitis' AS measure_name,
    qmpe.quality_measures_patient_entries_reporting_year AS reporting_year,

    qmpe.quality_measures_patient_entries_ipp AS ipp,
    qmpe.quality_measures_patient_entries_denominator AS denominator,
    MAX(qmpe.quality_measures_patient_entries_denominator_exclusion) AS exclusion,
    qmpe.quality_measures_patient_entries_numerator AS numerator,

    sd.service_detail_dos AS encounter_date,

    STRING_AGG(DISTINCT c.cpt_cptcode, ', ') AS cpt_codes,

    STRING_AGG(
        DISTINCT pa.patient_assessments_dxcode || ' - ' ||
        pa.patient_assessments_dxdescription,
        '; '
    ) AS diagnoses

FROM quality_measures_patient_entries qmpe
JOIN patient_registration pr
  ON pr.patient_registration_id = qmpe.quality_measures_patient_entries_patient_id
JOIN service_detail sd
  ON sd.service_detail_patientid = pr.patient_registration_id
 AND sd.service_detail_sdoctorid = qmpe.quality_measures_patient_entries_provider_id
JOIN cpt c ON c.cpt_id = sd.service_detail_cptid
JOIN patient_assessments pa
  ON pa.patient_assessments_patientid = pr.patient_registration_id
 AND pa.patient_assessments_encounterdate::date = sd.service_detail_dos
 AND pa.patient_assessments_dxcode LIKE 'J02%'
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = qmpe.quality_measures_patient_entries_provider_id
WHERE qmpe.quality_measures_patient_entries_measure_id = '66'
  AND qmpe.quality_measures_patient_entries_reporting_year = 2025
GROUP BY
    pr.patient_registration_id,
    patient_name,
    pr.patient_registration_dob,
    ep.emp_profile_fullname,
    reporting_year,
    ipp,
    denominator,
    numerator,
    sd.service_detail_dos
ORDER BY sd.service_detail_dos;
```

---

<img width="1289" height="626" alt="image" src="https://github.com/user-attachments/assets/f78b0891-4da4-47af-bb3b-4e81f0249b18" />

---
