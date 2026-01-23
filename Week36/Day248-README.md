# 📘 MIPS / PQRS / QPP – FAQ & Troubleshooting Guide

**Day 246 – January 19, 2026 (Monday)**

This document captures frequently asked questions, root causes, configurations, queries, and troubleshooting steps related to **MIPS, PQRS, QPP, QRDA, and HEDIS** workflows in GlaceEMR.

---

## 📌 MIPS – Frequently Asked Questions (FAQ)

### 1. Why is the MIPS report not updating properly?

* Kafka daily job is not running/updating correctly
* Report continues to show old data

**Solution**

* Run **MIPS Performance Job** in **Mode 3** for the reported year

---

### 2. Why is the Depression Screening score still low even though it is documented?

Common reasons:

* Screening documented but **not in the required tab**
* Positive screening without **required follow-up**
* Follow-up documented **outside the 2-day window**
* **Wrong age category** (Adult vs Adolescent)
* **Bipolar disorder diagnosis** exclusion
* Incorrect **SNOMED / LOINC codes**

---

### 3. Why are help documents not up to date?

* CMS releases updated measure specifications **once per year**
* Documents require **manual review and update**
* Short delay is expected after CMS releases

---

### 4. HbA1c documented but not reflected in MACRA tab or MIPS report

Possible causes:

* HbA1c not performed during the **measurement period**
* HbA1c result missing
* HbA1c performed outside the measurement period
* **GlaceCDS not running**

**Fix**

* Restart **GlaceCDS (all 3 servers)**
* Check GlaceCDS logs
* Once restarted, values will reflect correctly

**Inverse Measure Logic**

| HbA1c Result   | Measure Status         |
| -------------- | ---------------------- |
| < 9%           | NOT MET (Good Control) |
| ≥ 9% / Missing | MET (Poor Control)     |

---

### 5. How to generate QRDA-I and QRDA-III reports?

1. Go to **Reports**
2. Open **MIPS Performance Report**
3. Select **QRDA-I** or **QRDA-III**
4. Download the **XML file**

---

### 6. QRDA-I / QRDA-III report issues

Common causes:

* CDA XML data missing
* Report generation timeout
* Special characters in patient names (symbols)

---

### 7. How do I configure the PQRS tab?

#### Step 1: Enable PQRS in Template

* Utilities → Add/Edit Clinical Template
* Select template → Enable **PQRS**
* Save

#### Step 2: Configure Measures for Provider

* Measures must be mapped for:

  * Provider
  * Reporting Year
  * PQRS Measure IDs

**Database Table**

```sql
quality_measures_provider_mapping
```

Without configuration, system shows:

> *“Measures not configured for this Reporting Year”*

**Notes**

* PQRS checkbox options are fetched dynamically from **DataGateway**
* Configuration team must enable required tabs
* CPT codes must be selected based on **Claims vs Registry**

---

### 8. Generate PCMH reports for a given measure

* ❗ Requires **manual query preparation**

---

### 9. High-Risk Medication in the Elderly – High NOT MET count

* This is an **inverse measure**
* Higher NOT MET = **better quality**

**Common reasons**

1. Repeated prescriptions
2. Long-term usage
3. High daily dosage

| Status  | Meaning                       | Quality |
| ------- | ----------------------------- | ------- |
| MET     | High-risk medication detected | Poor    |
| NOT MET | No high-risk medication       | Good    |

---

### 10. Why is Tobacco Measure Criteria 2 count low?

* Applies only to **identified tobacco users**
* Fewer tobacco users → lower count

---

### 11. Smoking status documented but still NOT MET

* Smoking status alone is insufficient
* **Cessation counseling or medication** must also be documented

---

### 12. Patient info icon missing in some measures

* Configuration / eligibility based
* Measure-level limitations

---

### 13. Improvement Activities showing incorrect weightage

* Incorrect or incomplete configuration

---

### 14. Why does MIPS show “NA” for all measures?

* **GlaceCDS not running**

**Fix**

* Restart GlaceCDS

---

### 15. Blood Pressure vs Current Medication count mismatch

| Measure                     | Type          | Counting Method      |
| --------------------------- | ------------- | -------------------- |
| CMS165 – Blood Pressure     | Patient-based | One per patient/year |
| CMS68 – Current Medications | Episode-based | Every visit          |

* One patient with multiple visits may still count as **1**
* Only eligible records included

---

### 16. What should be documented for a Denominator Exception?

Valid reasons:

* **Medical reason** (unsafe treatment)
* **Patient reason** (refusal)

---

### 17. XML tab documentation but no score received

* Scores are **not calculated directly from XML tab**
* XML data is copied from:

  * `leaf_tab_patient` → `patient_assessment`
* Documentation must be in correct clinical sections (HPI, Screening, Assessment)

---

### 18. NOT MET = 0 but score shows 7 (Current Medication)

* Score is based on **eligible encounters**, not patient count

```java
$measureStatus.setIpp($measureStatus.getIpp()+1);
$measureStatus.setDenominator($measureStatus.getDenominator()+1);
```

---

### 19. What does “Partially Met” mean?

#### Episode-Based Measure Example

| Visit | Medication Documented | Result  |
| ----- | --------------------- | ------- |
| 1     | Yes                   | Met     |
| 2     | Yes                   | Met     |
| 3     | No                    | Not Met |
| 4     | Yes                   | Met     |

➡ **Status: Partially Met**

#### Patient-Based Measure Example

| Patient | Screening Done | Result  |
| ------- | -------------- | ------- |
| A       | Yes            | Met     |
| B       | No             | Not Met |
| C       | Yes            | Met     |

➡ **Status: Partially Met**

---

### 20. BP & Follow-up documented but still NOT MET

* Measure evaluates **most recent BP**
* BP must be **< 140 / 90**
* Follow-up alone does not satisfy the measure

---

### 21. HCPCS documented but measures show NA

* Configuration or eligibility issue

---

### 22. Specification files throwing error

* **Version mismatch**

---

### 23. Measures not configured properly

* Not fully mapped for:

  * Provider
  * Reporting Year

---

### 24. MIPS-related tabs not visible

* Feature/configuration not enabled

---

### 25. Generate NOT MET patient list

* Requires **manual query**

---

### 26. CPT II automation not working

* Works only when:

  * HEDIS enabled
  * Measures mapped to provider

---

### 27. Update MIPS performance report

* Run MIPS performance update job

---

### 28. Atorvastatin documented but still NOT MET

* Measure-specific exclusion or logic issue

---

### 29. Measures with “No Benchmark” showing score

* Internal scoring shown
* Does not convert to **QPP score**

---

### 30. Stratification filter not working

* Incorrect filter logic

---

## 📤 QPP / Data Submission Issues

1. New questions appear during first-time submission
2. Complete.json download blocked for some accounts

---

## 🧾 JSON & Measure Validation Issues

3. Measures not available in QPP directory
4. Improvement activities missing in QPP
5. Report counts vs QPP mismatch

   * Must run `calculateAndPerform` API
6. Measures submitted but scoring missing

---

## 📝 Documentation / Workflow Issues

7. Data documented in wrong section

   * Example: Fall risk in HPI instead of Screening

---

## 📊 Reporting Type Questions

8. Fetch reports for PQRS (Claims-based) accounts
9. Automation accounts → EHR-based or Claims-based?

---

## 🚀 2026 Measure Readiness (Critical)

10. Status of new 2026 measures
11. Number of new measures & help docs
12. Status of outdated measures

---

## 🧮 Measure Calculation Issues

13. MET / NOT MET count mismatch with patient list

---

## 🔧 Configuration & Queries

### ActionMappings.properties

```properties
MeasureURL.Action=com.glenwood.hcare.actions.chart.leafs.MeasureUrl
MeasurePatientEntries.Action=com.glenwood.hcare.actions.chart.leafs.MeasurePatientEntries
Measurecode.Screen=/jsp/chart/leafs/soap/Measurecode1.jsp
```

---

### Verify HEDIS Configuration

```sql
SELECT hedis_configuration_isactive
FROM hedis_configuration
WHERE hedis_configuration_reporting_year = 2026;
```

---

### HEDIS Measure Provider Mapping

```sql
SELECT
  hedis_measure_provider_configuration_provider_id AS provider_id,
  hedis_measure_provider_configuration_reporting_year AS year,
  hedis_measure_provider_configuration_measure_id AS measure_id
FROM hedis_measure_provider_configuration
WHERE provider_id = 1
  AND reporting_year = 2026;
```

---

### Collect Measure IDs

```sql
SELECT string_agg(hedis_measure_provider_configuration_measure_id, ',')
FROM hedis_measure_provider_configuration
WHERE provider_id = 1
  AND reporting_year = 2026;
```

---

### Read DataGateway Base URL

```sql
SELECT initial_settings_option_value
FROM initial_settings
WHERE initial_settings_option_name = 'Quarkus Datagateway URL-PQRS Controller';
```

---

### MeasureURL Ajax Call

```
https://dev2.glaceemr.com:444/D2Desktop/jsp/chart/leafs/soap/MeasureURL.Action?providerId=1&patientId=3237&encYear=2026
```

---

## ✅ Today’s Work Status

* Prepared **final query** for **CMS2v14 – Depression Screening & Follow-Up**
* Reviewed **PQRS tab configuration**
* Completed answers for **~50% of MIPS FAQs**
* Remaining items are **in progress** and will be updated **tomorrow**

---

📌 **End of README**
