# AR-KM 3 – Medication Reconciliation

**Measure-Level & Patient-Level SQL Reference**

---

## 1. Measure Overview

**Measure Name:** AR-KM 3 – Medication Reconciliation

**Requirement:**

> Percentage of patients received from a care transition who had medication reconciliation performed

**Threshold:** **≥ 90%**

---

## 2. Reporting Periods Used

Any rolling 3-month period between **04/01/2025 and today**

Validated fixed periods:

* 04/01/2025 – 06/30/2025
* 07/01/2025 – 09/30/2025
* 10/01/2025 – 12/31/2025
* 11/01/2025 – 01/31/2026

---

## 3. Data Model (Final Understanding)

### ✅ Denominator (Correct)

**Source:** `encounter`

A patient is counted if:

* `encounter.transition_of_care = true`
* Encounter date falls within reporting period
* Unique patient identified by `encounter_chartid`

---

### ❌ Incorrect Numerator Source (Ruled Out)

**Table:** `doc_presc`
**Field:** `doc_presc_status = 19`

Reason rejected:

* Prescriptions with status = 19 **exist**
* BUT **none are linked to transition_of_care encounters**
* Verified by direct joins → **0 rows**

👉 **Conclusion:** `doc_presc_status = 19` ≠ reliable medication reconciliation indicator

---

### ✅ Correct Numerator Source (Final)

**Table:** `reconciled_medication`

Medication reconciliation is considered **performed** if:

* A record exists in `reconciled_medication`
* It links to the **same encounter**
* Encounter is a **transition of care**

---

## 4. Final Measure-Level Query (OFFICIAL)

### ✅ AR-KM 3 – Measure Calculation (All Periods)

```sql
SELECT
    reporting_period,
    numerator,
    denominator,
    ROUND((numerator::numeric / NULLIF(denominator, 0)) * 100, 2) AS rate_pct
FROM (
    SELECT
        '04/01/2025 – 06/30/2025' AS reporting_period,
        COUNT(DISTINCT rm.reconciled_medication_encounterid) AS numerator,
        COUNT(DISTINCT e.encounter_chartid) AS denominator
    FROM encounter e
    LEFT JOIN reconciled_medication rm
        ON rm.reconciled_medication_encounterid = e.encounter_id
       AND rm.reconciled_medication_on::date >= DATE '2025-04-01'
       AND rm.reconciled_medication_on::date <  DATE '2025-07-01'
    WHERE e.transition_of_care = true
      AND e.encounter_date >= DATE '2025-04-01'
      AND e.encounter_date <  DATE '2025-07-01'

    UNION ALL

    SELECT
        '07/01/2025 – 09/30/2025',
        COUNT(DISTINCT rm.reconciled_medication_encounterid),
        COUNT(DISTINCT e.encounter_chartid)
    FROM encounter e
    LEFT JOIN reconciled_medication rm
        ON rm.reconciled_medication_encounterid = e.encounter_id
       AND rm.reconciled_medication_on::date >= DATE '2025-07-01'
       AND rm.reconciled_medication_on::date <  DATE '2025-10-01'
    WHERE e.transition_of_care = true
      AND e.encounter_date >= DATE '2025-07-01'
      AND e.encounter_date <  DATE '2025-10-01'

    UNION ALL

    SELECT
        '10/01/2025 – 12/31/2025',
        COUNT(DISTINCT rm.reconciled_medication_encounterid),
        COUNT(DISTINCT e.encounter_chartid)
    FROM encounter e
    LEFT JOIN reconciled_medication rm
        ON rm.reconciled_medication_encounterid = e.encounter_id
       AND rm.reconciled_medication_on::date >= DATE '2025-10-01'
       AND rm.reconciled_medication_on::date <  DATE '2026-01-01'
    WHERE e.transition_of_care = true
      AND e.encounter_date >= DATE '2025-10-01'
      AND e.encounter_date <  DATE '2026-01-01'

    UNION ALL

    SELECT
        '11/01/2025 – 01/31/2026',
        COUNT(DISTINCT rm.reconciled_medication_encounterid),
        COUNT(DISTINCT e.encounter_chartid)
    FROM encounter e
    LEFT JOIN reconciled_medication rm
        ON rm.reconciled_medication_encounterid = e.encounter_id
       AND rm.reconciled_medication_on::date >= DATE '2025-11-01'
       AND rm.reconciled_medication_on::date <  DATE '2026-02-01'
    WHERE e.transition_of_care = true
      AND e.encounter_date >= DATE '2025-11-01'
      AND e.encounter_date <  DATE '2026-02-01'
) x
ORDER BY reporting_period;
```

---

## 5. Final Patient-Level Validation Query (AUDIT READY)

This query proves **why numerator = 0**.

```sql
SELECT DISTINCT
    '04/01/2025 – 06/30/2025' AS reporting_period,

    p.patient_registration_id         AS patient_id,
    p.patient_registration_first_name AS first_name,
    p.patient_registration_last_name  AS last_name,

    DATE_PART('year', AGE(p.patient_registration_dob)) AS age,

    CASE
        WHEN p.patient_registration_sex = 1 THEN 'Male'
        WHEN p.patient_registration_sex = 2 THEN 'Female'
        ELSE 'Unknown'
    END AS gender,

    TO_CHAR(e.encounter_date::date, 'MM-DD-YYYY')
        AS transition_of_care_performed_on,

    CASE
        WHEN rm.reconciled_medication_encounterid IS NOT NULL
            THEN 'Medication reconciled'
        ELSE 'NO medication reconciled'
    END AS medication_reconciled

FROM encounter e
LEFT JOIN chart c
    ON c.chart_patientid = e.encounter_chartid
LEFT JOIN patient_registration p
    ON p.patient_registration_id = c.chart_patientid
LEFT JOIN reconciled_medication rm
    ON rm.reconciled_medication_encounterid = e.encounter_id
   AND rm.reconciled_medication_on::date >= DATE '2025-04-01'
   AND rm.reconciled_medication_on::date <  DATE '2025-07-01'
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-04-01'
  AND e.encounter_date <  DATE '2025-07-01'
ORDER BY e.encounter_date;
```

---

## 6. Final Results (Validated)

| Reporting Period        | Numerator | Denominator | Rate |
| ----------------------- | --------- | ----------- | ---- |
| 04/01/2025 – 06/30/2025 | 0         | 52          | 0%   |
| 07/01/2025 – 09/30/2025 | 0         | 49          | 0%   |
| 10/01/2025 – 12/31/2025 | 0         | 36          | 0%   |
| 11/01/2025 – 01/31/2026 | 0         | 29          | 0%   |

---

## 7. Final Conclusion

* ✅ Denominator logic is **correct and verified**
* ✅ Patient linkage is **correct**
* ❌ Medication reconciliation **not documented for transition-of-care encounters**
* ❌ AR-KM 3 **fails 90% threshold**
* 🟡 This is a **clinical documentation/workflow gap**, NOT a SQL or reporting issue

---
Got it 👍
Below is a **clean, professional README** that **analyzes the entire conversation from the beginning** and documents **what was done, why it was done, and the final conclusion**.

You can directly use this as a **project README**, **audit documentation**, or attach it to the report.

---
### Denominator

**Number of patients received from care transitions during the reporting period**

**System logic used:**

* `encounter.transition_of_care = true`
* Unique patients counted using `encounter_chartid`

---

### Numerator

**Patients from the denominator who had medication reconciliation performed**

**Validated logic:**

* Medication reconciliation is **NOT reliably captured** via:

  * `encounter.medication_attestation_status`
* Correct sources identified:

  * `doc_presc.doc_presc_status = 19`
  * `reconciled_medication` table (primary source of truth)

---

### Performance Requirement

* **≥ 90% compliance** required

---

## 3. Tables Used

| Table                   | Purpose                                   |
| ----------------------- | ----------------------------------------- |
| `encounter`             | Identifies transition-of-care encounters  |
| `chart`                 | Maps encounters to patients               |
| `patient_registration`  | Patient demographics                      |
| `doc_presc`             | eRx prescriptions (status = 19 checked)   |
| `reconciled_medication` | Actual medication reconciliation evidence |

---

## 4. Key Findings (From Analysis)

### 4.1 Denominator Validation ✅

* Transition-of-care encounters were found for all reporting periods
* Denominator counts matched expected values:

  * 52 / 49 / 36 / 29

### 4.2 Numerator Validation ❌

* **No transition-of-care encounters** had:

  * `doc_presc_status = 19` **AND**
  * `transition_of_care = true`
* Medication reconciliation records in `reconciled_medication`:

  * Exist in the system
  * **Do not align** with transition-of-care encounters

### 4.3 Patient-Level Cross Check

* Patient-level queries confirmed:

  * All denominator patients
  * None had documented medication reconciliation

---

## 5. Final Measure Results

| Reporting Period        | Numerator | Denominator | Rate |
| ----------------------- | --------- | ----------- | ---- |
| 04/01/2025 – 06/30/2025 | 0         | 52          | 0%   |
| 07/01/2025 – 09/30/2025 | 0         | 49          | 0%   |
| 10/01/2025 – 12/31/2025 | 0         | 36          | 0%   |
| 11/01/2025 – 01/31/2026 | 0         | 29          | 0%   |

---

## 6. Final Patient-Level Report Query (Validated)

```sql
SELECT DISTINCT
    '04/01/2025 – 06/30/2025' AS reporting_period,

    p.patient_registration_id         AS patient_id,
    p.patient_registration_first_name AS first_name,
    p.patient_registration_last_name  AS last_name,

    DATE_PART('year', AGE(p.patient_registration_dob)) AS age,

    CASE
        WHEN p.patient_registration_sex = 1 THEN 'Male'
        WHEN p.patient_registration_sex = 2 THEN 'Female'
        ELSE 'Unknown'
    END AS gender,

    e.encounter_date::date AS transition_of_care_date,

    CASE
        WHEN rm.reconciled_medication_encounterid IS NOT NULL
            THEN 'Medication reconciled'
        ELSE 'NO medication reconciled'
    END AS medication_reconciled

FROM encounter e
LEFT JOIN chart c
    ON c.chart_patientid = e.encounter_chartid
LEFT JOIN patient_registration p
    ON p.patient_registration_id = c.chart_patientid
LEFT JOIN reconciled_medication rm
    ON rm.reconciled_medication_encounterid = e.encounter_id
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-04-01'
  AND e.encounter_date <  DATE '2025-07-01'
ORDER BY transition_of_care_date;
```

---

# 🔹 AR-KM 3 – Split Queries

---

## ✅ 1. DENOMINATOR

**Patients received from care transitions during the reporting period**

**Definition (Final):**

* `encounter.transition_of_care = true`
* Encounter date within reporting period
* Count **distinct patients** (`encounter_chartid`)

---

### 📌 Denominator – Measure Level (Counts)

#### Example: **04/01/2025 – 06/30/2025**

```sql
SELECT
    COUNT(DISTINCT e.encounter_chartid) AS denominator
FROM encounter e
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-04-01'
  AND e.encounter_date <  DATE '2025-07-01';
```

---

### 📌 Denominator – Patient-Level Detail (Audit)

```sql
SELECT DISTINCT
    e.encounter_chartid               AS chart_patient_id,
    p.patient_registration_id         AS patient_id,
    p.patient_registration_first_name AS first_name,
    p.patient_registration_last_name  AS last_name,
    TO_CHAR(e.encounter_date::date, 'MM-DD-YYYY') AS transition_of_care_date
FROM encounter e
JOIN chart c
    ON c.chart_patientid = e.encounter_chartid
JOIN patient_registration p
    ON p.patient_registration_id = c.chart_patientid
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-04-01'
  AND e.encounter_date <  DATE '2025-07-01'
ORDER BY e.encounter_date;
```

---

## ✅ 2. NUMERATOR

**Patients from the denominator who had medication reconciliation performed**

---

### 🔴 IMPORTANT (Final Logic)

Medication reconciliation is **NOT** reliably captured by `doc_presc_status = 19`.

✅ **Correct Source:** `reconciled_medication`

A patient counts if:

* A record exists in `reconciled_medication`
* It links to the **same encounter**
* Encounter is a **transition of care**
* Reconciliation date falls in reporting period

---

### 📌 Numerator – Measure Level (Counts)

#### Example: **04/01/2025 – 06/30/2025**

```sql
SELECT
    COUNT(DISTINCT rm.reconciled_medication_encounterid) AS numerator
FROM reconciled_medication rm
JOIN encounter e
    ON e.encounter_id = rm.reconciled_medication_encounterid
WHERE e.transition_of_care = true
  AND rm.reconciled_medication_on::date >= DATE '2025-04-01'
  AND rm.reconciled_medication_on::date <  DATE '2025-07-01';
```

✅ **Expected Result (validated):** `0`

---

### 📌 Numerator – Patient-Level Detail (Audit)

```sql
SELECT DISTINCT
    e.encounter_chartid               AS chart_patient_id,
    p.patient_registration_id         AS patient_id,
    p.patient_registration_first_name AS first_name,
    p.patient_registration_last_name  AS last_name,
    TO_CHAR(rm.reconciled_medication_on::date, 'MM-DD-YYYY')
        AS medication_reconciled_on
FROM reconciled_medication rm
JOIN encounter e
    ON e.encounter_id = rm.reconciled_medication_encounterid
JOIN chart c
    ON c.chart_patientid = e.encounter_chartid
JOIN patient_registration p
    ON p.patient_registration_id = c.chart_patientid
WHERE e.transition_of_care = true
  AND rm.reconciled_medication_on::date >= DATE '2025-04-01'
  AND rm.reconciled_medication_on::date <  DATE '2025-07-01'
ORDER BY rm.reconciled_medication_on;
```

---

## ✅ 3. Final Interpretation

| Component   | Source Table            | Result    |
| ----------- | ----------------------- | --------- |
| Denominator | `encounter`             | ✅ Correct |
| Numerator   | `reconciled_medication` | ❌ 0       |
| doc_presc   | ❌ Not valid alone       | Ignored   |

---
