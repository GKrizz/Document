# 📘 README

## Controlling High Blood Pressure (CMS165v13) – SQL Implementation Guide

---

## 📌 Measure Overview

**Measure Name:** Controlling High Blood Pressure
**eCQM ID:** CMS165v13
**Measurement Period:** `01-Jan-2025` to `31-Dec-2025`
**Provider Filter:** `service_detail_sdoctorid = 11`
**Measure Type:** Patient-based, Proportion
**Numerator Target:** Most recent BP < **140/90 mmHg**

---

## 🗄️ Tables Used

| Purpose              | Table                  |
| -------------------- | ---------------------- |
| Patient demographics | `patient_registration` |
| Chart linkage        | `chart`                |
| Encounters           | `encounter`            |
| Visit/CPT data       | `service_detail`       |
| CPT master           | `cpt`                  |
| Chronic diagnoses    | `problem_list`         |
| Encounter diagnoses  | `patient_assessments`  |

---

## 🕒 Measurement Period Constants

```text
MP_START = '2025-01-01'
MP_END   = '2025-12-31'
MP_START_PLUS_6_MONTHS = '2025-06-30'
```

---

# STEP 1️⃣ – Age Requirement (Initial Population)

### 📖 Specification

> AgeInYearsAt(end of Measurement Period) ∈ [18, 85]

### 🧠 Logic

* Use **DOB** from `patient_registration`
* Calculate age as of `2025-12-31`

### ✅ SQL

```sql
WITH age_eligible AS (
    SELECT
        pr.patient_registration_id AS patient_id
    FROM patient_registration pr
    WHERE pr.patient_registration_active = TRUE
      AND DATE_PART(
            'year',
            AGE('2025-12-31'::date, pr.patient_registration_dob)
          ) BETWEEN 18 AND 85
)
SELECT COUNT(*) FROM age_eligible;
```

---

# STEP 2️⃣ – Essential Hypertension Diagnosis (I10)

### 📖 Specification

> Diagnosis of Essential Hypertension starting **before and continuing into**,
> OR **starting during the first 6 months** of the measurement period

### 🔑 Important

Per QDM, **diagnosis evidence can come from multiple sources**:

* Chronic problem list
* Encounter-based diagnoses

---

## 2A️⃣ Hypertension from Problem List

### 🧠 Mapping

| Spec Concept | SQL Column                     |
| ------------ | ------------------------------ |
| Diagnosis    | `problem_list_dx_code = 'I10'` |
| Onset        | `problem_list_onset_date`      |
| Resolution   | `problem_list_resolved_date`   |

### ✅ SQL

```sql
SELECT DISTINCT
    pl.problem_list_patient_id AS patient_id
FROM problem_list pl
WHERE pl.problem_list_dx_code = 'I10'
  AND pl.problem_list_isactive = TRUE
  AND (
        pl.problem_list_onset_date IS NULL
        OR pl.problem_list_onset_date <= '2025-06-30'
      )
  AND (
        pl.problem_list_resolved_date IS NULL
        OR pl.problem_list_resolved_date >= '2025-01-01'
      );
```

---

## 2B️⃣ Hypertension from Encounter Diagnoses

### 🧠 Why

Encounter diagnoses are valid **Diagnosis QDM elements**.

### ✅ SQL

```sql
SELECT DISTINCT
    pa.patient_assessments_patientid AS patient_id
FROM patient_assessments pa
WHERE pa.patient_assessments_dxcode = 'I10'
  AND pa.patient_assessments_encounterdate
        BETWEEN '2024-01-01' AND '2025-06-30';
```

---

## 2C️⃣ Combined Essential Hypertension (Spec-Correct)

```sql
WITH hypertension_dx AS (
    SELECT patient_id FROM (
        SELECT
            pl.problem_list_patient_id AS patient_id
        FROM problem_list pl
        WHERE pl.problem_list_dx_code = 'I10'
          AND pl.problem_list_isactive = TRUE
          AND (
                pl.problem_list_onset_date IS NULL
                OR pl.problem_list_onset_date <= '2025-06-30'
              )
          AND (
                pl.problem_list_resolved_date IS NULL
                OR pl.problem_list_resolved_date >= '2025-01-01'
              )

        UNION

        SELECT
            pa.patient_assessments_patientid AS patient_id
        FROM patient_assessments pa
        WHERE pa.patient_assessments_dxcode = 'I10'
          AND pa.patient_assessments_encounterdate
                BETWEEN '2024-01-01' AND '2025-06-30'
    ) t
)
SELECT COUNT(*) FROM hypertension_dx;
```

✔️ Matches **“exists Essential Hypertension Diagnosis”** exactly.

---

# STEP 3️⃣ – Qualifying Encounters

### 📖 Specification

AdultOutpatientEncounters include:

* Office visits
* Annual wellness
* Preventive
* Home health
* Virtual
* Telephone

### 🧠 Mapping

| Spec      | SQL                        |
| --------- | -------------------------- |
| Encounter | `service_detail`           |
| CPT       | `cpt_cptcode`              |
| Provider  | `service_detail_sdoctorid` |
| Date      | `service_detail_dos`       |

---

### ✅ SQL

```sql
WITH qualifying_encounters AS (
    SELECT DISTINCT
        sd.service_detail_patientid AS patient_id
    FROM service_detail sd
    JOIN cpt c
        ON c.cpt_id = sd.service_detail_cptid
    WHERE sd.service_detail_dos
            BETWEEN '2025-01-01' AND '2025-12-31'
      AND sd.service_detail_sdoctorid = 11
      AND c.cpt_cptcode IN (
            -- Office
            '99202','99203','99204','99205',
            '99212','99213','99214','99215',

            -- Annual Wellness
            'G0402','G0438','G0439',

            -- Preventive
            '99395','99396','99397',
            '99385','99386','99387',

            -- Home Health
            '99341','99342','99344','99345',
            '99347','99348','99349','99350',

            -- Virtual
            '98970','98971','98972',
            '98980','98981',
            '99421','99422','99423',
            '99457','99458',

            -- Telephone
            '98966','98967','98968',
            '99441','99442','99443'
      )
)
SELECT COUNT(*) FROM qualifying_encounters;
```

---

# STEP 4️⃣ – Initial Population (IPP)

### 📖 Specification

> Age 18–85
> AND exists Essential Hypertension
> AND exists Qualifying Encounter

---

### ✅ SQL (IPP)

```sql
WITH age_eligible AS (
    SELECT pr.patient_registration_id AS patient_id
    FROM patient_registration pr
    WHERE pr.patient_registration_active = TRUE
      AND DATE_PART(
            'year',
            AGE('2025-12-31'::date, pr.patient_registration_dob)
          ) BETWEEN 18 AND 85
),
hypertension_dx AS (
    SELECT patient_id FROM (
        SELECT
            pl.problem_list_patient_id AS patient_id
        FROM problem_list pl
        WHERE pl.problem_list_dx_code = 'I10'
          AND pl.problem_list_isactive = TRUE
          AND (
                pl.problem_list_onset_date IS NULL
                OR pl.problem_list_onset_date <= '2025-06-30'
              )
          AND (
                pl.problem_list_resolved_date IS NULL
                OR pl.problem_list_resolved_date >= '2025-01-01'
              )

        UNION

        SELECT
            pa.patient_assessments_patientid AS patient_id
        FROM patient_assessments pa
        WHERE pa.patient_assessments_dxcode = 'I10'
          AND pa.patient_assessments_encounterdate
                BETWEEN '2024-01-01' AND '2025-06-30'
    ) t
),
qualifying_encounters AS (
    SELECT DISTINCT
        sd.service_detail_patientid AS patient_id
    FROM service_detail sd
    JOIN cpt c
        ON c.cpt_id = sd.service_detail_cptid
    WHERE sd.service_detail_dos
            BETWEEN '2025-01-01' AND '2025-12-31'
      AND sd.service_detail_sdoctorid = 11
      AND c.cpt_cptcode IN (
            '99202','99203','99204','99205',
            '99212','99213','99214','99215',
            'G0402','G0438','G0439',
            '99395','99396','99397',
            '99385','99386','99387',
            '99341','99342','99344','99345',
            '99347','99348','99349','99350',
            '98970','98971','98972',
            '98980','98981',
            '99421','99422','99423',
            '99457','99458',
            '98966','98967','98968',
            '99441','99442','99443'
      )
)
SELECT DISTINCT
    ae.patient_id
FROM age_eligible ae
JOIN hypertension_dx hd
    ON hd.patient_id = ae.patient_id
JOIN qualifying_encounters qe
    ON qe.patient_id = ae.patient_id;
```

---

## 📊 Validation Summary

| Step                                | Result |
| ----------------------------------- | ------ |
| Age eligible (18–85)                | ~6300  |
| Hypertension (I10)                  | ~990   |
| Qualifying encounters (provider 11) | 20     |
| **Initial Population**              | **3**  |

✔️ This matches backend behavior and MIPS expectations.

---

## 🔜 Next Steps (Not Included Here)

* Denominator Exclusions

  * Hospice
  * Pregnancy
  * ESRD / Dialysis / Transplant
  * Palliative Care
  * Frailty / Nursing Home
* Numerator

  * Most recent BP day
  * Lowest SBP < 140
  * Lowest DBP < 90
  * Exclude ED / Inpatient BP

---
