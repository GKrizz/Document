# Preventive Care and Screening: Depression Screening and Follow-Up

**Adult Patient (Age ≥ 18)**

---

## 📌 Measure Summary

This document validates **Preventive Care and Screening: Screening for Depression and Follow-Up Plan** for an **adult patient** using SQL evidence aligned to the **CQL logic**.

---

## 🧾 Patient Context

| Field                        | Value                       |
| ---------------------------- | --------------------------- |
| Patient ID                   | **3237**                    |
| DOB                          | **2003-04-10**              |
| Age at MP Start (2026-01-01) | **22 years**                |
| Measurement Period           | **2026-01-01 → 2026-12-31** |
| Encounter Date               | **2026-01-14**              |
| CPT Code                     | **99204**                   |

---

## 🧩 Measure Logic (Adult ≥ 18)

### Numerator (Adult Path)

Patient **meets Numerator** if:

1. **Most recent Adult Depression Screening (PHQ-9)** within **14 days** before the encounter **AND**
2. Screening result is:

   * **Negative**, **OR**
   * **Positive + documented follow-up** within:

     * Encounter period **OR**
     * ≤ **2 days after encounter**

---

## ✅ Final Outcome

| Measure Component                  | Status     |
| ---------------------------------- | ---------- |
| Initial Population (IPP)           | ✅ PASS     |
| Denominator                        | ✅ PASS     |
| Denominator Exclusion (Bipolar Dx) | ❌ NO       |
| Numerator                          | ✅ **PASS** |
| Measure Result                     | ✅ **MET**  |

> ⚠️ If the UI shows **NOT MET**, the issue is **mapping / timing logic**, not missing data.

---

## 🔎 Step-by-Step Validation with SQL

---

### **STEP 1 — Age at Start of Measurement Period**

```sql
SELECT
    patient_registration_id,
    EXTRACT(YEAR FROM AGE('2026-01-01', patient_registration_dob)) AS age_at_mp_start
FROM patient_registration
WHERE patient_registration_id = 3237;
```

✔ Result: **22 years**

---

### **STEP 2 — Qualifying Encounter During MP**

```sql
SELECT
    sd.service_detail_id,
    sd.service_detail_dos,
    c.cpt_cptcode
FROM service_detail sd
JOIN cpt c ON c.cpt_id = sd.service_detail_cptid
WHERE sd.service_detail_patientid = 3237
  AND sd.service_detail_dos BETWEEN '2026-01-01' AND '2026-12-31'
  AND c.cpt_cptcode IN (
      '99202','99203','99204','99205',
      '99212','99213','99214','99215',
      '99384','99385','99386','99387',
      '99394','99395','99396','99397',
      '90791','90792','90832','90834','90837'
  );
```

✔ Result: **99204 on 2026-01-14**

---

### **STEP 3 — Denominator Exclusion (Bipolar Disorder)**

```sql
SELECT *
FROM (
    SELECT
        pa.patient_assessments_patientid AS patient_id,
        pa.patient_assessments_encounterdate::date AS dx_date
    FROM patient_assessments pa
    WHERE pa.patient_assessments_patientid = 3237
      AND pa.patient_assessments_dxcode ~ '^F3[01]'

    UNION ALL

    SELECT
        pl.problem_list_patient_id,
        COALESCE(pl.problem_list_onset_date::date,
                 pl.problem_list_createdon::date)
    FROM problem_list pl
    WHERE pl.problem_list_patient_id = 3237
      AND pl.problem_list_dx_code ~ '^F3[01]'
) dx
WHERE dx.dx_date < '2026-01-14';
```

✔ Result: **No rows → NOT excluded**

---

### **STEP 4 — Most Recent Adult Depression Screening (PHQ-9)**

**LOINC:** `73832-8`
**Window:** 14 days before encounter

```sql
SELECT
    risk_assessment_id,
    risk_assessment_date,
    risk_assessment_code,
    risk_assessment_result_code
FROM risk_assessment
WHERE risk_assessment_patient_id = 3237
  AND risk_assessment_code = '73832-8'
  AND risk_assessment_date BETWEEN
        '2026-01-14'::date - INTERVAL '14 days'
    AND '2026-01-14'
ORDER BY risk_assessment_date DESC;
```

✔ Result:

* **Date:** 2026-01-14
* **Result Code:** `428181000124104` (**Positive**)

---

### **STEP 5 — Follow-Up After Positive Screening**

**Allowed window:**

* Encounter date → **+2 days**

**SNOMED Follow-Up Codes:** includes `88848003`

```sql
SELECT
    pce.patient_clinical_elements_created_on::date AS followup_date,
    ce.clinical_elements_snomed
FROM patient_clinical_elements pce
JOIN clinical_elements ce
  ON ce.clinical_elements_gwid = pce.patient_clinical_elements_gwid
WHERE pce.patient_clinical_elements_patientid = 3237
  AND ce.clinical_elements_snomed = '88848003'
  AND pce.patient_clinical_elements_created_on::date
        BETWEEN '2026-01-14' AND '2026-01-16';
```

✔ Result:

* **Follow-up documented on 2026-01-14**
* **Within allowed timeframe**

---

## 🧠 Final Combined SQL (Reusable Template)

```sql
WITH params AS (
    SELECT
        3237::bigint AS patient_id,
        DATE '2026-01-01' AS mp_start,
        DATE '2026-12-31' AS mp_end
),
age_check AS (
    SELECT
        pr.patient_registration_id AS patient_id,
        EXTRACT(YEAR FROM AGE(p.mp_start, pr.patient_registration_dob)) AS age_at_mp_start
    FROM patient_registration pr
    JOIN params p ON p.patient_id = pr.patient_registration_id
),
qualifying_encounter AS (
    SELECT
        sd.service_detail_patientid AS patient_id,
        sd.service_detail_dos AS encounter_date
    FROM service_detail sd
    JOIN cpt c ON c.cpt_id = sd.service_detail_cptid
    JOIN params p ON p.patient_id = sd.service_detail_patientid
    WHERE sd.service_detail_dos BETWEEN p.mp_start AND p.mp_end
      AND c.cpt_cptcode IN ('99204','99203','99202','99205','99212','99213','99214','99215')
),
most_recent_screening AS (
    SELECT DISTINCT ON (ra.risk_assessment_patient_id)
        ra.risk_assessment_patient_id AS patient_id,
        ra.risk_assessment_result_code
    FROM risk_assessment ra
    JOIN qualifying_encounter qe ON qe.patient_id = ra.risk_assessment_patient_id
    WHERE ra.risk_assessment_code = '73832-8'
      AND ra.risk_assessment_date BETWEEN
            qe.encounter_date - INTERVAL '14 days'
        AND qe.encounter_date
    ORDER BY ra.risk_assessment_patient_id, ra.risk_assessment_date DESC
),
followup AS (
    SELECT DISTINCT pce.patient_clinical_elements_patientid AS patient_id
    FROM patient_clinical_elements pce
    JOIN clinical_elements ce
      ON ce.clinical_elements_gwid = pce.patient_clinical_elements_gwid
    JOIN qualifying_encounter qe
      ON qe.patient_id = pce.patient_clinical_elements_patientid
    WHERE ce.clinical_elements_snomed = '88848003'
      AND pce.patient_clinical_elements_created_on::date
            BETWEEN qe.encounter_date
                AND qe.encounter_date + INTERVAL '2 days'
)
SELECT
    CASE
        WHEN ac.age_at_mp_start >= 18
         AND (
             ms.risk_assessment_result_code = '428171000124102'
             OR (
                 ms.risk_assessment_result_code = '428181000124104'
                 AND EXISTS (SELECT 1 FROM followup)
             )
         )
        THEN 'NUMERATOR MET'
        ELSE 'NUMERATOR NOT MET'
    END AS measure_result
FROM age_check ac
LEFT JOIN most_recent_screening ms ON ms.patient_id = ac.patient_id;
```

---

## 🛠️ Common Reasons UI Shows **NOT MET**

1. Follow-up SNOMED not mapped to **Adult Follow-Up Value Set**
2. Follow-up date stored as `created_on` but logic checks `performed_on`
3. Encounter linkage missing
4. Risk assessment not marked **Performed**
5. UI uses **authorDatetime** but DB uses **created_on**
