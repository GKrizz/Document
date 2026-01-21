
# 📘 CMS2v14 – Depression Screening & Follow-Up

**(Adult Patients ≥18 years)**

## 📌 Measure Overview

This report evaluates **CMS2v14 (Depression Screening and Follow-Up Plan)** for **adult patients (18+)** during the **measurement period (MP)**.

The report determines whether a patient:

* Had a **qualifying encounter** (Denominator)
* Received **PHQ-9 Adult depression screening** within the allowed timeframe
* Had **appropriate follow-up** if screening was **positive**
* **Meets the Numerator** (PASS / NOT MET)

---

## 📅 Measurement Period

```
Start Date: 2026-01-01
End Date  : 2026-12-31
```

---

## 👤 Patient Eligibility (Initial Population – IPP)

A patient is included if:

* **Age ≥ 18** as of `2026-01-01`
* Has **at least one qualifying encounter** during the measurement period

---

## 🏥 Qualifying Encounters (Denominator)

Encounters are identified from `service_detail` using the following **CPT codes**:

```
99201–99205
99212–99215
99384–99387
99394–99397
90791, 90792
90832, 90834, 90837
```

---

## 🧠 Depression Screening (Numerator Requirement)

### Accepted Screenings

* **PHQ-9 Adult**

  * LOINC: `73832-8`
* (Adolescent code `73831-0` exists but adults only are included)

### Valid Screening Results

| Result   | SNOMED Code       |
| -------- | ----------------- |
| Negative | `428171000124102` |
| Positive | `428181000124104` |

### Timing Rule

* Screening must occur **within 14 days on or before the encounter date**

---

## 🔁 Follow-Up Plan (Required ONLY if Screening is Positive)

If screening is **Positive**, a follow-up intervention must be documented **within 2 days** of screening.

### Follow-Up Identification

* Table: `patient_clinical_elements`
* SNOMED Code:

```
88848003  → Follow-up for depression - adult
```

---

## ✅ Numerator Logic

| Condition                            | Status  |
| ------------------------------------ | ------- |
| Negative screening                   | PASS    |
| Positive screening + valid follow-up | PASS    |
| Positive screening without follow-up | NOT MET |
| No valid screening                   | NOT MET |

---

## 📊 Output Columns

| Column Name                  | Description                   |
| ---------------------------- | ----------------------------- |
| ACCOUNT NO                   | Patient ID                    |
| LAST NAME                    | Patient last name             |
| FIRST NAME                   | Patient first name            |
| AGE                          | Age as of MP start            |
| GENDER                       | Male / Female / Unknown       |
| SERVICE PLACE                | Fixed org name                |
| SERVICE PLACE CITY           | Patient city                  |
| ENCOUNTER DETAILS            | Encounter dates               |
| SERVICEDETAILS               | Encounter + CPT               |
| DENOMINATOR                  | Earliest qualifying encounter |
| DEPRESSION SCREENING DETAILS | PHQ-9 date & score            |
| FOLLOW UP                    | YES / NO                      |
| NUMERATOR                    | PASS / NOT MET                |

---

## 🧾 Final Production Query (SELECT-Only)

```sql
SELECT
    pr.patient_registration_id AS "ACCOUNT NO",
    pr.patient_registration_last_name AS "LAST NAME",
    pr.patient_registration_first_name AS "FIRST NAME",

    EXTRACT(YEAR FROM AGE(DATE '2026-01-01',
        pr.patient_registration_dob)) AS "AGE",

    CASE pr.patient_registration_sex
        WHEN 1 THEN 'Male'
        WHEN 2 THEN 'Female'
        ELSE 'Unknown'
    END AS "GENDER",

    'Medical Walk In Care LLC-OV' AS "SERVICE PLACE",
    pr.patient_registration_city AS "SERVICE PLACE CITY",

    STRING_AGG(enc.encounter_date, ', ' ORDER BY enc.encounter_date)
        AS "ENCOUNTER DETAILS",

    STRING_AGG(enc.service_detail, ', ' ORDER BY enc.encounter_date)
        AS "SERVICEDETAILS",

    MIN(enc.service_detail) AS "DENOMINATOR",

    TO_CHAR(ms.screening_date,'MM/DD/YYYY')
        || ' (Adult Depression Screening [' 
        || COALESCE(ms.result_value::text,'') || '])'
        AS "DEPRESSION SCREENING DETAILS",

    CASE
        WHEN ms.result_code = '428181000124104'
         AND EXISTS (
             SELECT 1
             FROM patient_clinical_elements pce
             JOIN clinical_elements ce
               ON ce.clinical_elements_gwid = pce.patient_clinical_elements_gwid
             WHERE pce.patient_clinical_elements_patientid = pr.patient_registration_id
               AND ce.clinical_elements_snomed = '88848003'
               AND pce.patient_clinical_elements_created_on::date
                   BETWEEN ms.screening_date
                       AND ms.screening_date + INTERVAL '2 days'
         )
        THEN 'YES'
        ELSE 'NO'
    END AS "FOLLOW UP",

    CASE
        WHEN ms.result_code = '428171000124102'
            THEN 'PASS'
        WHEN ms.result_code = '428181000124104'
         AND EXISTS (
             SELECT 1
             FROM patient_clinical_elements pce
             JOIN clinical_elements ce
               ON ce.clinical_elements_gwid = pce.patient_clinical_elements_gwid
             WHERE pce.patient_clinical_elements_patientid = pr.patient_registration_id
               AND ce.clinical_elements_snomed = '88848003'
               AND pce.patient_clinical_elements_created_on::date
                   BETWEEN ms.screening_date
                       AND ms.screening_date + INTERVAL '2 days'
         )
            THEN 'PASS'
        ELSE 'NOT MET'
    END AS "NUMERATOR"

FROM patient_registration pr

JOIN (
    SELECT DISTINCT
        sd.service_detail_patientid AS patient_id,
        TO_CHAR(sd.service_detail_dos,'MM/DD/YYYY') AS encounter_date,
        TO_CHAR(sd.service_detail_dos,'MM/DD/YYYY')
            || '(' || c.cpt_cptcode || ')' AS service_detail
    FROM service_detail sd
    JOIN cpt c ON c.cpt_id = sd.service_detail_cptid
    WHERE sd.service_detail_dos BETWEEN DATE '2026-01-01' AND DATE '2026-12-31'
      AND c.cpt_cptcode IN (
          '99201','99202','99203','99204','99205',
          '99212','99213','99214','99215',
          '99384','99385','99386','99387',
          '99394','99395','99396','99397',
          '90791','90792','90832','90834','90837'
      )
) enc ON enc.patient_id = pr.patient_registration_id

LEFT JOIN LATERAL (
    SELECT
        ra.risk_assessment_date::date AS screening_date,
        ra.risk_assessment_result_code AS result_code,
        ra.risk_assessment_result_value AS result_value
    FROM risk_assessment ra
    WHERE ra.risk_assessment_patient_id = pr.patient_registration_id
      AND ra.risk_assessment_code IN ('73831-0','73832-8')
      AND ra.risk_assessment_result_code IN (
          '428171000124102',
          '428181000124104'
      )
      AND ra.risk_assessment_date
          BETWEEN TO_DATE(enc.encounter_date,'MM/DD/YYYY') - INTERVAL '14 days'
              AND TO_DATE(enc.encounter_date,'MM/DD/YYYY')
    ORDER BY ra.risk_assessment_date DESC
    LIMIT 1
) ms ON TRUE

WHERE EXTRACT(YEAR FROM AGE(DATE '2026-01-01',
        pr.patient_registration_dob)) >= 18

GROUP BY
    pr.patient_registration_id,
    pr.patient_registration_last_name,
    pr.patient_registration_first_name,
    pr.patient_registration_dob,
    pr.patient_registration_sex,
    pr.patient_registration_city,
    ms.screening_date,
    ms.result_code,
    ms.result_value

ORDER BY
    pr.patient_registration_last_name,
    pr.patient_registration_first_name;
```

---
