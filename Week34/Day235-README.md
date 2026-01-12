# QRDA I Timeout & Patient Clinical Elements Investigation

## Date

**Day 235 – January 08, 2026 (Thursday)**

---

## 1. Problem Statement

### 1.1 Follow-up Status Issue

Follow-up is already getting marked **even when the status is “Not Met”**.

**Suspected Area**

* Follow-up checkbox logic
* Glenwood ID (GWID) mapping
* Data stored in `patient_clinical_elements` table

---

## 2. Clinical Element IDs Involved

### 2.1 XML / HPI Elements

```
xml_hpi_element_3_0000423900000000001
element_0000423900000000001
element_0000423900000000003
```

### 2.2 Current Element Under Investigation

```
element_0000400400000000701
```

---

## 3. Table Structure

### 3.1 patient_clinical_elements

```sql
\d patient_clinical_elements;
```

Key columns:

* `patient_clinical_elements_id` (PK)
* `patient_clinical_elements_patientid`
* `patient_clinical_elements_encounterid`
* `patient_clinical_elements_gwid`
* `patient_clinical_elements_value`
* `patient_clinical_elements_created_on`
* `patient_clinical_elements_last_modified_on`

Indexes:

* `patient_clinical_elements_gwid_idx`
* `idx_pce_encounter_gwid`
* `patient_clinical_elements_patient_encounter_idx`

Triggers:

* `patient_clinical_elements_chistory`
* `patient_clinical_elements_chistory_delete`
* `trg_keep_latest_patient_element`

⚠️ **Important Trigger**
`trg_keep_latest_patient_element`
This trigger may overwrite or suppress older entries and is a strong candidate for unexpected follow-up behavior.

---

## 4. Queries Used for Analysis

### 4.1 Fetch Clinical Elements for GWID (2025)

```sql
SELECT patient_clinical_elements_id,
       patient_clinical_elements_patientid,
       patient_clinical_elements_encounterid,
       patient_clinical_elements_created_on
FROM patient_clinical_elements
WHERE patient_clinical_elements_gwid = '0000400400000000701'
  AND EXTRACT(YEAR FROM patient_clinical_elements_created_on) = 2025
ORDER BY patient_clinical_elements_created_on;
```

---

### 4.2 Search for GWID `0000423900000000003`

```sql
SELECT
    pce.patient_clinical_elements_id,
    pce.patient_clinical_elements_gwid AS qwid,
    pce.patient_clinical_elements_patientid AS patient_id,
    pce.patient_clinical_elements_encounterid AS encounter_id,
    pce.patient_clinical_elements_created_on AS created_on,
    qmpe.quality_measures_patient_entries_provider_id AS provider_id,
    ep.emp_profile_fullname AS provider_name,
    qmpe.quality_measures_patient_entries_reporting_year AS reporting_year,
    qmpe.quality_measures_patient_entries_cmsid AS measure_name
FROM patient_clinical_elements pce
LEFT JOIN quality_measures_patient_entries qmpe
       ON qmpe.quality_measures_patient_entries_patient_id = pce.patient_clinical_elements_patientid
      AND qmpe.quality_measures_patient_entries_reporting_year = 2025
LEFT JOIN emp_profile ep
       ON ep.emp_profile_empid = qmpe.quality_measures_patient_entries_provider_id
WHERE pce.patient_clinical_elements_gwid = '0000423900000000003'
  AND EXTRACT(YEAR FROM pce.patient_clinical_elements_created_on) = 2025
ORDER BY pce.patient_clinical_elements_created_on;
```

---

### 4.3 Provider Aggregation for GWID `0000400400000000701`

```sql
SELECT
    pce.patient_clinical_elements_id,
    pce.patient_clinical_elements_patientid AS patient_id,
    pce.patient_clinical_elements_encounterid AS encounter_id,
    pce.patient_clinical_elements_created_on AS created_on,
    ARRAY_AGG(DISTINCT qmpe.quality_measures_patient_entries_provider_id) AS provider_ids,
    ARRAY_AGG(DISTINCT ep.emp_profile_fullname) AS provider_names
FROM patient_clinical_elements pce
LEFT JOIN quality_measures_patient_entries qmpe
       ON qmpe.quality_measures_patient_entries_patient_id = pce.patient_clinical_elements_patientid
      AND qmpe.quality_measures_patient_entries_reporting_year = 2025
LEFT JOIN emp_profile ep
       ON ep.emp_profile_empid = qmpe.quality_measures_patient_entries_provider_id
WHERE pce.patient_clinical_elements_gwid = '0000400400000000701'
  AND pce.patient_clinical_elements_created_on >= '2025-01-01'
  AND pce.patient_clinical_elements_created_on < '2026-01-01'
GROUP BY pce.patient_clinical_elements_id,
         pce.patient_clinical_elements_patientid,
         pce.patient_clinical_elements_encounterid,
         pce.patient_clinical_elements_created_on
ORDER BY pce.patient_clinical_elements_created_on;
```

---

## 5. Possible Reasons for Follow-up Being Marked Incorrectly

1. **Trigger `trg_keep_latest_patient_element`**

   * Older “Not Met” records may be replaced by newer records without explicit UI action.

2. **GWID Reuse Across Elements**

   * Same GWID used for multiple logical questions (HPI / Follow-up / Checkbox).

3. **Encounter-Level Index (`idx_pce_encounter_gwid`)**

   * New encounter entries may override previous encounter values.

4. **Value Interpretation**

   * `patient_clinical_elements_value = false`
   * UI logic may treat existence of record as “follow-up done”.

5. **QRDA / Quality Measure Join**

   * Quality measure joins may implicitly mark follow-up based on patient-level data, not encounter-level.

---

## 6. New Task – QRDA I Timeout Issue

### 6.1 Problem Description

When clicking **QRDA I**, a ZIP file is generated.
If there is a **large number of patient connections**, the request **times out**.

### 6.2 URL Involved

```
http://localhost:8080/GlaceMaster/jsp/Measures/MIPSPerformanceReport.Action?mode=6&zipPath=
```

### 6.3 Current Response

```json
{
  "zipPath": ""
}
```

This indicates:

* ZIP generation did not complete
* Request timed out before response was finalized

---

## 7. Possible Causes of QRDA I Timeout

1. **Synchronous ZIP Generation**

   * Large patient volume processed in a single request

2. **Long-Running DB Queries**

   * Clinical elements + quality measures + provider joins

3. **No Pagination / Batch Processing**

   * Entire dataset processed at once

4. **Servlet / Reverse Proxy Timeout**

   * Tomcat / application server request timeout exceeded

5. **Memory Pressure**

   * ZIP generation in memory instead of streaming

---

## 8. Recommended Solutions

### 8.1 Backend Improvements

* Move QRDA I generation to **async background job**
* Store ZIP on server and return `jobId`
* Poll status via API

### 8.2 Performance

* Generate ZIP **per provider / per batch**
* Stream ZIP output instead of loading in memory
* Add indexes on:

  * `patient_clinical_elements_created_on`
  * `quality_measures_patient_entries_patient_id`

### 8.3 UI Handling

* Show “Processing…” status
* Download link once ZIP is ready

---

## 9. Summary

* Follow-up checkbox issue is **likely data + trigger related**
* GWID reuse and “latest element” trigger are prime suspects
* QRDA I timeout is due to **synchronous heavy processing**
* Async ZIP generation is the safest long-term fix

---
