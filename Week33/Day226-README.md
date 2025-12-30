# ✅ CMS146 / Measure 66

**Appropriate Testing for Pharyngitis**

---

## 🧩 CONDITION 1 — Patient Age (≥ 3 years)

```sql
SELECT
  patient_registration_id,
  EXTRACT(YEAR FROM AGE('2025-01-01', patient_registration_dob)) AS age
FROM patient_registration
WHERE patient_registration_id = 2942;
```

**Result**

```
age = 4
```

✅ **PASS** (age ≥ 3)

---

## 🧩 CONDITION 2 — Eligible Encounter (IPP trigger)

### Valid visit CPTs (examples)

* 99341–99345
* 99201–99205
* 99211–99215
* etc.

```sql
SELECT
  sd.service_detail_id,
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
99341  → 2025-12-11
99342  → 2025-12-11
```

✅ **PASS** (qualifying outpatient visit)

➡️ **IPP = 1**

---

## 🧩 CONDITION 3 — Pharyngitis Diagnosis

### Allowed ICD-10 (partial)

* J02.0
* J02.8
* J02.9

```sql
SELECT
  patient_assessments_dxcode,
  patient_assessments_encounterdate
FROM patient_assessments
WHERE patient_assessments_patientid = 2942;
```

**Result**

```
J02.0
J02.8
```

✅ **PASS**

➡️ **DENOMINATOR = 1**

---

## 🧩 CONDITION 4 — Antibiotic Prescribed (Denominator logic)

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
Amoxicillin   → 2025-12-11
Minocycline  → 2025-12-12
```

✅ Antibiotic ordered
➡️ Patient **stays in denominator** (not excluded)

---

## 🧩 CONDITION 5 — Strep Test Done (NUMERATOR CONDITION)

### Rule:

> **Group A Strep test performed within ±3 days of encounter date**

### Encounter date:

```
2025-12-11
```

### Valid window:

```
2025-12-08  → 2025-12-14
```

---

### 🔍 IMPORTANT: Use **CORRECT chartId**

You already confirmed:

```
patientId = 2942
chartId   = 2055
```

---

### Check lab entry

```sql
SELECT
  lab_entries_test_desc,
  lab_entries_loinc,
  lab_entries_perf_on
FROM lab_entries
WHERE lab_entries_chartid = 2055
  AND lab_entries_perf_on
      BETWEEN '2025-12-08' AND '2025-12-14';
```

**Result**

```
STREPTOCOCCUS, GROUP A CULTURE
LOINC: 11268-0
2025-12-10
```

✅ **PASS**

➡️ **NUMERATOR = 1**

---

## 🧩 FINAL MEASURE OUTPUT (SYSTEM TRUTH)

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

**Result**

```
IPP = 1
DENOM = 1
NUM = 1
```

---

# 🧠 ONE-SCREEN SUMMARY (MEMORIZE THIS)

| Step | Condition           | Result |
| ---- | ------------------- | ------ |
| 1    | Age ≥ 3             | ✅      |
| 2    | Valid visit CPT     | ✅      |
| 3    | Pharyngitis DX      | ✅      |
| 4    | Antibiotic ordered  | ✅      |
| 5    | Strep test ±3 days  | ✅      |
|      | **Final Numerator** | **1**  |

---
