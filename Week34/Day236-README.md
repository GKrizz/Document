## CMS2v14 – Preventive Care and Screening: Screening for Depression and Follow-Up Plan (Measure 134)

**Day:** 236
**Date:** January 09, 2026 (Friday)
**Measure:** CMS2v14
**CMS ID:** 134
**Reporting Year:** 2025

---

## 1. Objective

To validate **CMS2v14 (Measure 134)** data flow by:

* Identifying mapped providers
* Filtering active/eligible providers
* Verifying providers with patient data
* Building denominator logic
* Linking qualifying encounters and services
* Extracting depression screening (PHQ) clinical elements

---

## 2. Step 1 – Providers Mapped to Measure 134

```sql
SELECT
  qmpm.quality_measures_provider_mapping_provider_id AS provider_id,
  ep.emp_profile_fullname                            AS provider_name,
  qmpm.quality_measures_provider_mapping_reporting_year AS year,
  qmpm.quality_measures_provider_mapping_measure_id  AS measure_id
FROM quality_measures_provider_mapping qmpm
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = qmpm.quality_measures_provider_mapping_provider_id
WHERE qmpm.quality_measures_provider_mapping_measure_id = '134'
  AND qmpm.quality_measures_provider_mapping_reporting_year = 2025
ORDER BY provider_id;
```

### Result

| Provider ID | Provider Name       | Year | Measure |
| ----------- | ------------------- | ---- | ------- |
| 1           | Test Doctor M.D.    | 2025 | 134     |
| 11          | ANAND KANJULIA M.D. | 2025 | 134     |
| 1343        | test1343, Mark      | 2025 | 134     |
| 1359        | Billing Doctor      | 2025 | 134     |
| 1361        | Service Doctor M.D  | 2025 | 134     |

---

## 3. Step 2 – Active / Eligible Providers (MACRA Config)

Filters providers who are **active and eligible** for reporting in 2025.

```sql
SELECT
  qmpm.quality_measures_provider_mapping_provider_id AS provider_id,
  ep.emp_profile_fullname                            AS provider_name,
  pc.macra_provider_configuration_reporting_year     AS year,
  qmpm.quality_measures_provider_mapping_measure_id  AS measure_id
FROM quality_measures_provider_mapping qmpm
JOIN macra_provider_configuration pc
  ON pc.macra_provider_configuration_provider_id = qmpm.quality_measures_provider_mapping_provider_id
 AND pc.macra_provider_configuration_reporting_year = qmpm.quality_measures_provider_mapping_reporting_year
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = qmpm.quality_measures_provider_mapping_provider_id
WHERE qmpm.quality_measures_provider_mapping_measure_id = '134'
  AND qmpm.quality_measures_provider_mapping_reporting_year = 2025
ORDER BY provider_id;
```

### Result

| Provider ID | Provider Name      | Year | Measure |
| ----------- | ------------------ | ---- | ------- |
| 1           | Test Doctor M.D.   | 2025 | 134     |
| 1359        | Billing Doctor     | 2025 | 134     |
| 1361        | Service Doctor M.D | 2025 | 134     |

---

## 4. Step 3 – Providers with Patient Data

Identifies providers who actually have **patient entries** for Measure 134.

```sql
SELECT DISTINCT
  pe.quality_measures_patient_entries_provider_id AS provider_id,
  ep.emp_profile_fullname                          AS provider_name
FROM quality_measures_patient_entries pe
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = pe.quality_measures_patient_entries_provider_id
WHERE pe.quality_measures_patient_entries_measure_id = '134'
  AND pe.quality_measures_patient_entries_reporting_year = 2025
ORDER BY provider_id;
```

---

## 5. Denominator Logic – Patient Eligibility

### 5.1 Base Patients (Measure + Provider)

```sql
WITH base_patients AS (
  SELECT DISTINCT
    qmpe.quality_measures_patient_entries_patient_id AS patient_id
  FROM quality_measures_patient_entries qmpe
  WHERE qmpe.quality_measures_patient_entries_measure_id = '134'
    AND qmpe.quality_measures_patient_entries_reporting_year = 2025
    AND qmpe.quality_measures_patient_entries_provider_id = 1
),
```

---

### 5.2 Qualifying Encounters (CPT-Based)

Includes valid **office visits, preventive visits, behavioral health services**.

```sql
qualifying_encounters AS (
  SELECT DISTINCT
    sd.service_detail_patientid AS patient_id,
    sd.service_detail_dos::date AS dos,
    c.cpt_cptcode
  FROM service_detail sd
  JOIN cpt c
    ON c.cpt_id = sd.service_detail_cptid
  WHERE sd.service_detail_sdoctorid = 1
    AND sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-31'
    AND c.cpt_cptcode IN (
      '99201','99202','99203','99204','99205',
      '99212','99213','99214','99215',
      '99384','99385','99386','99387',
      '99394','99395','99396','99397',
      '97003','96150','96151',
      '96116','96118','92625',
      '90791','90792',
      '90832','90834','90837'
    )
),
```

---

### 5.3 Encounter & Service Aggregation

```sql
encounter_agg AS (
  SELECT
    patient_id,
    STRING_AGG(
      DISTINCT TO_CHAR(dos,'MM/DD/YYYY'),
      ', ' ORDER BY TO_CHAR(dos,'MM/DD/YYYY')
    ) AS encounter_details
  FROM qualifying_encounters
  GROUP BY patient_id
),

service_agg AS (
  SELECT
    patient_id,
    STRING_AGG(
      DISTINCT TO_CHAR(dos,'MM/DD/YYYY') || '(' || cpt_cptcode || ')',
      ', ' ORDER BY TO_CHAR(dos,'MM/DD/YYYY') || '(' || cpt_cptcode || ')'
    ) AS servicedetails
  FROM qualifying_encounters
  GROUP BY patient_id
)
```

---

## 6. Denominator Output

Criteria:

* **Age ≥ 12**
* **At least one qualifying encounter in 2025**

```sql
CASE
  WHEN EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) >= 12
   AND ea.patient_id IS NOT NULL
  THEN 1
  ELSE 0
END AS "DENOMINATOR"
```

---

## 7. Depression Screening (PHQ) Extraction

Pulls **PHQ screening data** from `patient_clinical_elements`.

```sql
depression_screening AS (
  SELECT
      pce.patient_clinical_elements_patientid AS patient_id,
      STRING_AGG(
          TO_CHAR(pce.patient_clinical_elements_created_on::date, 'MM/DD/YYYY')
          || ' (PHQ [' || pce.patient_clinical_elements_value || '])',
          ', ' ORDER BY pce.patient_clinical_elements_created_on
      ) AS depression_screening_details
  FROM patient_clinical_elements pce
  WHERE pce.patient_clinical_elements_gwid ILIKE '%PHQ%'
    AND pce.patient_clinical_elements_created_on
        BETWEEN '2025-01-01' AND '2025-12-31'
  GROUP BY pce.patient_clinical_elements_patientid
)
```

---

## 8. Final Output Fields

* Account Number
* Patient Name
* Age
* Gender
* Service Place & City
* Encounter Details
* Service Details
* Denominator Flag
* **Depression Screening Details (PHQ)**

---

## 9. Key Observations

* Provider eligibility must be validated via **MACRA configuration**
* Denominator logic strictly enforces **age ≥ 12**
* CPT filtering is critical for valid encounters
* PHQ screening relies on **GWID naming conventions**
* Missing or inconsistent GWIDs may cause numerator gaps

---

## 10. Next Steps

* Map **Follow-Up Plan** logic for positive depression screens
* Validate **Numerator & Exclusions**
* Cross-check QRDA I output vs SQL results
* Investigate PHQ GWID consistency

---
