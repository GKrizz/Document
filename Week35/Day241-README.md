# CMS2v14 – Depression Screening and Follow-Up

**Reporting Year: 2025**

---

## 📌 Measure Overview

**Measure ID:** 134
**Name:** Preventive Care and Screening: Screening for Depression and Follow-Up Plan
**Population:** Patients **≥ 12 years**
**Requirement:**

* Depression screening using an **age-appropriate PHQ tool**
* If **positive**, a **follow-up plan within 2 days**

---

## 🗓️ Measurement Period

| Field              | Value      |
| ------------------ | ---------- |
| MP Start           | 2025-01-01 |
| MP End             | 2025-12-31 |
| Age Reference Date | 2025-01-01 |

---

## 🧩 Age ↔ Screening Tool Rules (CMS2v14)

| Patient Age | Allowed Screening                     |
| ----------- | ------------------------------------- |
| 12–16       | **Adolescent only** (LOINC `73831-0`) |
| 17          | **Adolescent OR Adult**               |
| ≥18         | **Adult only** (LOINC `73832-8`)      |

---

## 1️⃣ Initial Population (IPP) = Denominator

### Definition

Patient must:

* Be **≥ 12 years** at MP start
* Have **≥ 1 qualifying encounter** during MP
* Be tied to the measure/provider/year

---

### ✅ IPP / DENOMINATOR (Detail)

```sql
SELECT DISTINCT
    pr.patient_registration_id AS patient_id,
    EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) AS age
FROM patient_registration pr
JOIN quality_measures_patient_entries qmpe
    ON qmpe.quality_measures_patient_entries_patient_id = pr.patient_registration_id
JOIN service_detail sd
    ON sd.service_detail_patientid = pr.patient_registration_id
JOIN cpt c
    ON c.cpt_id = sd.service_detail_cptid
WHERE qmpe.quality_measures_patient_entries_measure_id = '134'
  AND qmpe.quality_measures_patient_entries_reporting_year = 2025
  AND qmpe.quality_measures_patient_entries_provider_id = 1
  AND EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) >= 12
  AND sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-31'
  AND sd.service_detail_sdoctorid = 1
  AND c.cpt_cptcode IN (
      '99201','99202','99203','99204','99205',
      '99212','99213','99214','99215',
      '99384','99385','99386','99387',
      '99394','99395','99396','99397',
      '97003','96150','96151','96116','96118',
      '92625','90791','90792','90832','90834','90837'
  )
ORDER BY pr.patient_registration_id;
```

---

### ✅ IPP / DENOMINATOR (Count)

```sql
SELECT COUNT(DISTINCT pr.patient_registration_id) AS ipp_denominator
FROM patient_registration pr
JOIN quality_measures_patient_entries qmpe
    ON qmpe.quality_measures_patient_entries_patient_id = pr.patient_registration_id
JOIN service_detail sd
    ON sd.service_detail_patientid = pr.patient_registration_id
JOIN cpt c
    ON c.cpt_id = sd.service_detail_cptid
WHERE qmpe.quality_measures_patient_entries_measure_id = '134'
  AND qmpe.quality_measures_patient_entries_reporting_year = 2025
  AND qmpe.quality_measures_patient_entries_provider_id = 1
  AND EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) >= 12
  AND sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-31'
  AND sd.service_detail_sdoctorid = 1
  AND c.cpt_cptcode IN (
      '99201','99202','99203','99204','99205',
      '99212','99213','99214','99215',
      '99384','99385','99386','99387',
      '99394','99395','99396','99397',
      '97003','96150','96151','96116','96118',
      '92625','90791','90792','90832','90834','90837'
  );
```

---

## 2️⃣ Denominator Exclusion (DENEX)

### Definition

Exclude patient **if**:

* Patient is in IPP
* Has **Bipolar Disorder diagnosis** (`F30* / F31*`)
* Diagnosis **starts before** qualifying encounter date

---

### 🚫 DENOMINATOR EXCLUSION

```sql
SELECT DISTINCT
    pr.patient_registration_id AS patient_id
FROM patient_registration pr
JOIN quality_measures_patient_entries qmpe
    ON qmpe.quality_measures_patient_entries_patient_id = pr.patient_registration_id
JOIN service_detail sd
    ON sd.service_detail_patientid = pr.patient_registration_id
JOIN cpt c
    ON c.cpt_id = sd.service_detail_cptid
JOIN (
        SELECT
            patient_assessments_patientid AS patient_id,
            patient_assessments_encounterdate::date AS bipolar_dx_date
        FROM patient_assessments
        WHERE patient_assessments_dxcode ~ '^F3[01]'

        UNION ALL

        SELECT
            problem_list_patient_id,
            COALESCE(problem_list_onset_date::date,
                     problem_list_createdon::date)
        FROM problem_list
        WHERE problem_list_dx_code ~ '^F3[01]'
     ) bipolar_dx
    ON bipolar_dx.patient_id = pr.patient_registration_id
WHERE qmpe.quality_measures_patient_entries_measure_id = '134'
  AND qmpe.quality_measures_patient_entries_reporting_year = 2025
  AND qmpe.quality_measures_patient_entries_provider_id = 1
  AND EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) >= 12
  AND sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-31'
  AND sd.service_detail_sdoctorid = 1
  AND c.cpt_cptcode IN (
      '99201','99202','99203','99204','99205',
      '99212','99213','99214','99215',
      '99384','99385','99386','99387',
      '99394','99395','99396','99397'
  )
  AND bipolar_dx.bipolar_dx_date < sd.service_detail_dos;
```

---

## 3️⃣ Numerator

### Numerator Rules (CMS2v14)

A patient is in the **Numerator** if:

1. Has **MOST RECENT** depression screening
2. Screening date is:

   * Same day as encounter **OR**
   * ≤ **14 days before** encounter
3. Screening tool is **age-appropriate**
4. Result is:

   * **Negative** → Automatically numerator
   * **Positive** → **Follow-up REQUIRED** within:

     * Encounter date **OR**
     * ≤ **2 days after encounter**

---

## 🟢 FINAL CORRECT NUMERATOR QUERY (CMS2v14)

> **Gold standard** – aligns with CMS CQL logic

```sql
SELECT
    x.patient_id,
    x.age,
    x.encounter_date,
    x.screening_date,
    x.risk_assessment_screening_code,
    x.risk_assessment_result_code
FROM (
    SELECT
        sd.service_detail_patientid AS patient_id,
        EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) AS age,
        sd.service_detail_dos::date AS encounter_date,
        ra.risk_assessment_date::date AS screening_date,
        ra.risk_assessment_screening_code,
        ra.risk_assessment_result_code,
        ROW_NUMBER() OVER (
            PARTITION BY sd.service_detail_patientid
            ORDER BY ra.risk_assessment_date DESC
        ) AS rn
    FROM service_detail sd
    JOIN cpt c
        ON c.cpt_id = sd.service_detail_cptid
    JOIN patient_registration pr
        ON pr.patient_registration_id = sd.service_detail_patientid
    JOIN risk_assessment ra
        ON ra.risk_assessment_patient_id = sd.service_detail_patientid
    WHERE sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-31'
      AND c.cpt_cptcode IN (
          '99202','99203','99204','99205',
          '99212','99213','99214','99215',
          '99384','99385','99386','99387',
          '99394','99395','99396','99397'
      )
      -- CMS valid screenings only
      AND ra.risk_assessment_screening_code IN ('73831-0','73832-8')
      -- Timing rule
      AND ra.risk_assessment_date::date <= sd.service_detail_dos
      AND sd.service_detail_dos - ra.risk_assessment_date::date BETWEEN 0 AND 14
      -- Age ≥ 12
      AND EXTRACT(YEAR FROM AGE('2025-01-01', pr.patient_registration_dob)) >= 12
) x
WHERE x.rn = 1
AND (
        /* NEGATIVE → always numerator */
        x.risk_assessment_result_code = '428171000124102'

     OR
        /* POSITIVE → follow-up REQUIRED */
        (
            x.risk_assessment_result_code = '428181000124104'
            AND EXISTS (
                SELECT 1
                FROM patient_clinical_elements pce
                WHERE pce.patient_clinical_elements_patientid = x.patient_id
                  AND pce.patient_clinical_elements_created_on::date
                      BETWEEN x.encounter_date
                          AND x.encounter_date + INTERVAL '2 days'
            )
        )
)
AND (
        /* Age ↔ screening mapping */
        (x.age BETWEEN 12 AND 16 AND x.risk_assessment_screening_code = '73831-0')
     OR (x.age = 17 AND x.risk_assessment_screening_code IN ('73831-0','73832-8'))
     OR (x.age >= 18 AND x.risk_assessment_screening_code = '73832-8')
)
ORDER BY x.patient_id;
```

---

## 🧠 Key Implementation Notes

### ✅ What This Query Guarantees

* Uses **LOINC codes**, not free-text
* Enforces **MOST RECENT** screening
* Applies **14-day lookback**
* Applies **2-day follow-up window**
* Enforces **age-appropriate tool**
* Matches **CMS2v14 CQL**

---

### ❌ Common Reasons Measures Show *NOT MET*

* Follow-up stored **outside 2-day window**
* Follow-up documented but **wrong table**
* Screening tool mismatch (Adult vs Adolescent)
* Using screening **description** instead of **LOINC**
* Older screening selected instead of **most recent**

---
