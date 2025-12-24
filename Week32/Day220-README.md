# ✅ 1️⃣ Check if PROVIDER is configured for the YEAR

```sql
SELECT *
FROM macra_provider_configuration
WHERE macra_provider_configuration_provider_id = 1
  AND macra_provider_configuration_reporting_year = 2025;
```

🔎 **Tells you**

* Is provider enabled for MIPS?
* Reporting period
* Hardship exemption

🗣️ **Say**

> “First I confirm provider-year configuration from `macra_provider_configuration`.”

---

# ✅ 2️⃣ Check which MEASURES are configured for the PROVIDER

```sql
SELECT *
FROM quality_measures_provider_mapping
WHERE quality_measures_provider_mapping_provider_id = 1
  AND quality_measures_provider_mapping_reporting_year = 2025;
```

🔎 **Tells you**

* Which measures are selected
* Quality measures list

🗣️ **Say**

> “Then I check configured measures from `quality_measures_provider_mapping`.”

---

# ✅ 3️⃣ Check PATIENT-LEVEL measure data (MOST IMPORTANT)

```sql
SELECT
  quality_measures_patient_entries_patient_id AS patient_id,
  quality_measures_patient_entries_measure_id AS measure_id,
  quality_measures_patient_entries_criteria   AS criteria,
  quality_measures_patient_entries_ipp        AS ipp,
  quality_measures_patient_entries_denominator AS denom,
  quality_measures_patient_entries_numerator   AS num,
  quality_measures_patient_entries_provider_id AS provider_id
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_reporting_year = 2025
  AND quality_measures_patient_entries_provider_id = 1
ORDER BY quality_measures_patient_entries_updated_on DESC
LIMIT 20;
```

🔎 **Tells you**

* Was patient evaluated?
* Why counts are 0 / non-zero
* Criteria-wise status

🗣️ **Say**

> “Patient-level truth is in `quality_measures_patient_entries`.”

---

# ✅ 4️⃣ Check WHY numerator / denominator is ZERO (per patient)

```sql
SELECT
  quality_measures_patient_entries_patient_id,
  quality_measures_patient_entries_measure_id,
  quality_measures_patient_entries_ipp,
  quality_measures_patient_entries_denominator,
  quality_measures_patient_entries_denominator_exclusion,
  quality_measures_patient_entries_denominator_exception,
  quality_measures_patient_entries_numerator,
  quality_measures_patient_entries_numerator_exclusion
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_reporting_year = 2025
  AND quality_measures_patient_entries_measure_id = 'ACI_PEA_1';
```

🔎 **Interpretation**

* `ipp = 0` → patient not eligible
* `denominator = 0` → not in denominator
* `numerator = 0` → did not meet measure

---

# ✅ 5️⃣ Aggregate PATIENT data manually (simulate macra_measures_rate)

```sql
SELECT
  quality_measures_patient_entries_provider_id AS provider_id,
  quality_measures_patient_entries_measure_id  AS measure_id,
  quality_measures_patient_entries_criteria    AS criteria,
  COUNT(*)                                     AS total_patients,
  SUM(quality_measures_patient_entries_ipp)    AS ipp,
  SUM(quality_measures_patient_entries_denominator) AS denominator,
  SUM(quality_measures_patient_entries_numerator)   AS numerator
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_reporting_year = 2025
  AND quality_measures_patient_entries_provider_id = 1
GROUP BY
  quality_measures_patient_entries_provider_id,
  quality_measures_patient_entries_measure_id,
  quality_measures_patient_entries_criteria;
```

🔎 **This shows**

* What `macra_measures_rate` **should look like**
* Confirms aggregation logic

🗣️ **Say**

> “`macra_measures_rate` is a GROUP BY of patient entries.”

---

# ✅ 6️⃣ Check if AGGREGATION job has run

```sql
SELECT *
FROM macra_measures_rate
WHERE macra_measures_rate_reporting_year = 2025;
```

🔎 **Result**

* 0 rows → MIPS job not run
* Rows present → aggregation done

🗣️ **Say**

> “If this table is empty, the MIPS aggregation job hasn’t run.”

---

# ✅ 7️⃣ Check WEIGHTAGE for scoring

```sql
SELECT *
FROM mips_weightage
WHERE mips_weightage_year = 2025;
```

🔎 **Important**

* If no row → scores cannot be calculated

---

# ✅ 8️⃣ Check FINAL MIPS SCORE

```sql
SELECT *
FROM mips_scores
WHERE mips_scores_provider_id = 1
  AND mips_scores_reporting_year = 2025;
```

🔎 **Meaning**

* Row exists → job fully completed
* No row → scoring not done

---

# 🧠 ONE MASTER QUERY (FAST CHECK EVERYTHING)

```sql
SELECT
  pc.macra_provider_configuration_provider_id AS provider_id,
  pc.macra_provider_configuration_reporting_year AS year,
  COUNT(DISTINCT pm.quality_measures_provider_mapping_measure_id) AS configured_measures,
  COUNT(pe.quality_measures_patient_entries_id) AS patient_measure_rows,
  COUNT(mr.macra_measures_rate_id) AS aggregated_measures,
  COUNT(ms.mips_scores_id) AS score_rows
FROM macra_provider_configuration pc
LEFT JOIN quality_measures_provider_mapping pm
  ON pm.quality_measures_provider_mapping_provider_id = pc.macra_provider_configuration_provider_id
 AND pm.quality_measures_provider_mapping_reporting_year = pc.macra_provider_configuration_reporting_year
LEFT JOIN quality_measures_patient_entries pe
  ON pe.quality_measures_patient_entries_provider_id = pc.macra_provider_configuration_provider_id
 AND pe.quality_measures_patient_entries_reporting_year = pc.macra_provider_configuration_reporting_year
LEFT JOIN macra_measures_rate mr
  ON mr.macra_measures_rate_provider_id = pc.macra_provider_configuration_provider_id
 AND mr.macra_measures_rate_reporting_year = pc.macra_provider_configuration_reporting_year
LEFT JOIN mips_scores ms
  ON ms.mips_scores_provider_id = pc.macra_provider_configuration_provider_id
 AND ms.mips_scores_reporting_year = pc.macra_provider_configuration_reporting_year
WHERE pc.macra_provider_configuration_reporting_year = 2025
  AND pc.macra_provider_configuration_provider_id = 1
GROUP BY
  pc.macra_provider_configuration_provider_id,
  pc.macra_provider_configuration_reporting_year;
```

### 🧾 How to read this

| Value                    | Meaning                    |
| ------------------------ | -------------------------- |
| configured_measures = 0  | Provider not configured    |
| patient_measure_rows = 0 | No patients evaluated      |
| aggregated_measures = 0  | MIPS job not run           |
| score_rows = 0           | Final score not calculated |

---

# 🎯 FINAL INTERVIEW-READY STATEMENT (MEMORIZE)

> “I validate MIPS data by checking provider configuration, configured measures, patient-level entries, aggregated rates, and final scores — in that order.”

---


## 1. Core MIPS / MACRA Configuration Tables

These tables define **yearly setup, thresholds, provider configuration, and scoring rules**.

| Table Name                     | Purpose                                                                          |
| ------------------------------ | -------------------------------------------------------------------------------- |
| `macra_configuration`          | MIPS configuration for each reporting year                                       |
| `macra_provider_configuration` | Provider-wise MIPS configuration (measures, reporting period, submission method) |
| `macra_measures_rate`          | Calculated measure rates (numerator/denominator) per provider                    |
| `mips_weightage`               | Category weightage (Quality, PI, IA, Cost) for each year                         |
| `mips_scores`                  | Final MIPS scores per provider (used for dashboard & PDF)                        |
| `mips_notification`            | MIPS alerts / notifications shown in UI                                          |

---

## 2. Quality Measures – Patient-Level Data

These tables store **patient-level measure results**, which drive numerators/denominators.

| Table Name                                 | Purpose                                                 |
| ------------------------------------------ | ------------------------------------------------------- |
| `quality_measures_patient_entries`         | Current year patient-wise measure status                |
| `quality_measures_patient_entries_history` | History of patient measure status                       |
| `quality_measures_provider_mapping`        | Which measures are configured for which provider & year |

---

## 3. Improvement Activities (IA)

Used for **Improvement Activities documentation and scoring**.

| Table Name                         | Purpose                                 |
| ---------------------------------- | --------------------------------------- |
| `ia_measures`                      | Improvement Activities master data      |
| `mips_imporvement_activities_20xx` | Year-specific IA definitions (CMS data) |

---

## 4. Promoting Interoperability (PI)

Handles **PI exclusions and hardship exceptions**.

| Table Name             | Purpose                                |
| ---------------------- | -------------------------------------- |
| `pi_exclusion`         | PI exclusion configuration             |
| `pi_exclusion_details` | Provider-specific PI exclusion details |

---

## 5. Year-Specific Quality Measure Master Tables (CMS Data)

These tables are **recreated every year (20xx)** and loaded from CMS / ECQI.

### Main Measure Definition

| Table                               |
| ----------------------------------- |
| `quality_measures_2024` (or `20xx`) |

### Submission Method Mapping

| Table                                     |
| ----------------------------------------- |
| `quality_measures_submission_method_2024` |

---

## 6. eCQM Specifications & Value Sets

Used during **measure calculation & CDS validation**.

| Table                       | Purpose                                        |
| --------------------------- | ---------------------------------------------- |
| `ecqm_specifications_2024`  | QDM categories & value sets                    |
| `additional_valuesets_2024` | Extra/manual value sets (RXNorm, SNOMED, etc.) |

---

## 7. Benchmarks & Scoring Reference Tables

Used to **calculate performance points**.

| Table                 | Purpose                                  |
| --------------------- | ---------------------------------------- |
| `mips_benchmark_2024` | CMS benchmark thresholds                 |
| `macra_measures_rate` | Measure performance used with benchmarks |

---

## 8. Claims & Registry Single Source Tables

Used for **claims-based and registry-based reporting**.

| Table                                             |
| ------------------------------------------------- |
| `2024_claims_individual_measures_single_source`   |
| `2024_registry_individual_measures_single_source` |

---

## 9. Supporting / Derived Tables (Updated via APIs)

Populated by datagateway APIs.

| Table                                | Updated By                    |
| ------------------------------------ | ----------------------------- |
| `quality_measure_specification`      | `updateECQMSpecification` API |
| `quality_measure_benchmark_2024`     | `updateECQMSpecification` API |
| `quality_measure_pqrs_specification` | `updatePQRSSpecification` API |

---

## 10. High-Level Table Flow (Simple View)

```
CMS / ECQI Data
   ↓
quality_measures_20xx
ecqm_specifications_20xx
additional_valuesets_20xx
   ↓
Provider Configuration
(macra_provider_configuration)
   ↓
Patient Validation
(quality_measures_patient_entries)
   ↓
Rate Calculation
(macra_measures_rate)
   ↓
Scoring
(mips_scores, mips_weightage)
   ↓
Reports / PDF / QPP JSON
```

---
