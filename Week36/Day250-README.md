
# ✅ AR-KM 3 – FINAL SQL QUERIES

(**Fixed 3-month calendar periods**)

## Definition Recap (what the query implements)

* **Denominator**
  Distinct patients (`encounter_chartid`)
  where `transition_of_care = true`

* **Numerator**
  Same patients with **Medication Reconciliation conducted**
  → `doc_presc.doc_presc_status = 19`
  → joined using `doc_presc_encounter_id = encounter_id`

---

## 🔹 1️⃣ 04/01/2025 – 06/30/2025

```sql
SELECT
    '04/01/2025 – 06/30/2025' AS reporting_period,
    COUNT(DISTINCT CASE
        WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
    END) AS numerator,
    COUNT(DISTINCT e.encounter_chartid) AS denominator,
    ROUND(
        COUNT(DISTINCT CASE
            WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
        END)::numeric
        /
        NULLIF(COUNT(DISTINCT e.encounter_chartid), 0)
        * 100,
        2
    ) AS rate_pct
FROM encounter e
LEFT JOIN doc_presc d
    ON d.doc_presc_encounter_id = e.encounter_id
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-04-01'
  AND e.encounter_date <  DATE '2025-07-01';
```

---

## 🔹 2️⃣ 07/01/2025 – 09/30/2025

```sql
SELECT
    '07/01/2025 – 09/30/2025' AS reporting_period,
    COUNT(DISTINCT CASE
        WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
    END) AS numerator,
    COUNT(DISTINCT e.encounter_chartid) AS denominator,
    ROUND(
        COUNT(DISTINCT CASE
            WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
        END)::numeric
        /
        NULLIF(COUNT(DISTINCT e.encounter_chartid), 0)
        * 100,
        2
    ) AS rate_pct
FROM encounter e
LEFT JOIN doc_presc d
    ON d.doc_presc_encounter_id = e.encounter_id
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-07-01'
  AND e.encounter_date <  DATE '2025-10-01';
```

---

## 🔹 3️⃣ 10/01/2025 – 12/31/2025

```sql
SELECT
    '10/01/2025 – 12/31/2025' AS reporting_period,
    COUNT(DISTINCT CASE
        WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
    END) AS numerator,
    COUNT(DISTINCT e.encounter_chartid) AS denominator,
    ROUND(
        COUNT(DISTINCT CASE
            WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
        END)::numeric
        /
        NULLIF(COUNT(DISTINCT e.encounter_chartid), 0)
        * 100,
        2
    ) AS rate_pct
FROM encounter e
LEFT JOIN doc_presc d
    ON d.doc_presc_encounter_id = e.encounter_id
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-10-01'
  AND e.encounter_date <  DATE '2026-01-01';
```

---

## 🔹 4️⃣ 11/01/2025 – 01/31/2026

(Allowed as an alternate fixed 3-month window)

```sql
SELECT
    '11/01/2025 – 01/31/2026' AS reporting_period,
    COUNT(DISTINCT CASE
        WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
    END) AS numerator,
    COUNT(DISTINCT e.encounter_chartid) AS denominator,
    ROUND(
        COUNT(DISTINCT CASE
            WHEN d.doc_presc_status = 19 THEN e.encounter_chartid
        END)::numeric
        /
        NULLIF(COUNT(DISTINCT e.encounter_chartid), 0)
        * 100,
        2
    ) AS rate_pct
FROM encounter e
LEFT JOIN doc_presc d
    ON d.doc_presc_encounter_id = e.encounter_id
WHERE e.transition_of_care = true
  AND e.encounter_date >= DATE '2025-11-01'
  AND e.encounter_date <  DATE '2026-02-01';
```

---

## ✅ HOW TO USE THIS

* Run **each query**
* Capture:

  * Reporting period
  * Numerator
  * Denominator
  * Rate
* If **any period ≥ 90%** → measure passes
* If **all < 90%** → measure fails (as in your data)

---
