# 🩺 CMS139v13 - Falls: Screening for Future Fall Risk (MIPS 2025)

**📝 Task:** Analyze the specification for CMS139v13 'Falls: Screening for Future Fall Risk' in MIPS.

---

## 📋 Measure Details Reference

The `measure_details` table contains metadata about various MIPS measures tracked in our system. For example, the **Falls: Screening for Future Fall Risk** measure is stored as follows:

| Column       | Sample Value                          | Description                       |
| ------------ | ------------------------------------- | --------------------------------- |
| `id`         | 165                                   | Internal table ID for the measure |
| `measure_id` | 318                                   | Numeric measure identifier        |
| `cms_id`     | CMS139v5                              | CMS measure code and version      |
| `title`      | Falls: Screening for Future Fall Risk | Full measure name                 |

### Query to locate the Falls Screening measure

```sql
SELECT * FROM measure_details WHERE title ILIKE '%fall%';
```

Result:

| id  | measure\_id | cms\_id  | title                                 |
| --- | ----------- | -------- | ------------------------------------- |
| 165 | 318         | CMS139v5 | Falls: Screening for Future Fall Risk |

---

## 📋 Overview

This document provides an analysis and breakdown of the eCQM measure **CMS139v13**, titled **"Falls: Screening for Future Fall Risk"** for the MIPS 2025 reporting year. The measure evaluates whether patients aged 65 years and older have been screened for their risk of future falls during qualifying clinical encounters within the measurement period.

---

## 🎯 Objective

Measure the percentage of patients aged 65 and older who have been screened for future fall risk during the measurement period.

---

## 📊 Measure Details

* **Title:** Falls: Screening for Future Fall Risk
* **eCQM ID:** 139
* **Version:** 13.1.000
* **Type:** Process Measure
* **Scoring:** Proportion
* **Steward:** National Committee for Quality Assurance (NCQA)
* **Developers:** NCQA, American Medical Association (AMA), PCPI® Foundation
* **Measurement Period:** January 1 – December 31, 20XX
* **Population Focus:** Patients aged 65 years and older

---

## 👥 Population Criteria

### Initial Population

Patients who meet **both** criteria:

* 👵 Age ≥ 65 years at the start of the Measurement Period
* 📅 Have at least one **Qualifying Encounter** during the Measurement Period

**Qualifying Encounters include:**

* 🏥 Office Visit
* 🩺 Annual Wellness Visit
* 💻 Telehealth and Virtual Encounters
* 🛡️ Preventive Care Services (initial and established office visits)
* 🏠 Home Healthcare Services
* 🏢 Nursing Facility Visits
* 👂 Audiology Visits
* 🏋️ Physical and Occupational Therapy Evaluations
* ...and others as specified in the value set

---

### Denominator

* Same as the Initial Population.

---

### 🚫 Denominator Exclusions

Exclude patients who received **hospice care** during the Measurement Period, identified by any of the following:

| Criteria                                      | Exclude? |
| --------------------------------------------- | -------- |
| 🏥 Inpatient encounter with hospice discharge | Yes      |
| 🩺 Hospice encounter                          | Yes      |
| ✅ Hospice care assessment = "Yes"             | Yes      |
| 📋 Hospice care ordered or performed          | Yes      |
| 🏥 Hospice diagnosis                          | Yes      |

*If any exclusion applies, the patient is **excluded** from the denominator.*

---

### ✔️ Numerator

Patients from the denominator who had:

* 📝 A **falls screening assessment** (Falls Screening) performed during the measurement period.
* 📅 The assessment date or interval must fall **any day within the measurement period** (not necessarily the encounter date).
* 🔍 Uses `Global."NormalizeInterval"` function to evaluate relevantDatetime or relevantPeriod.

If matched → Numerator = **Met**
Otherwise → Numerator = **Not Met**

---

### ❌ Numerator Exclusions

* None


### ❌ Denominator Exceptions

* None

### ❌ Stratification

* None

---

## 🛠️ Step-by-Step Implementation Guide

1. 👵 **Identify patients aged ≥ 65** at the start of the measurement period.
2. 📅 **Find at least one qualifying encounter** for the patient during the measurement period.
3. 🚫 **Apply denominator exclusions:**

   * Check for hospice care services during the period (encounters, assessments, orders, diagnosis).
   * Exclude patients with hospice care.
4. 📝 **Identify if the patient had a Falls Screening assessment:**

   * Check for relevant assessments documented during the measurement period using normalized intervals.
5. 📈 **Calculate measure score:**

   * Numerator = Patients with Falls Screening assessment.
   * Denominator = Initial population excluding hospice patients.
   * Score = Proportion of numerator / denominator.

---

## 🔍 Step-by-Step Logic Summary

1. 👵 **Check age:** Include only patients aged 65 or older.
2. 🏥 **Find qualifying encounter:** Ensure patient had at least one encounter of specific types.
3. 📦 **Qualifying Encounter Value Set OID:**

   * Office Visit OID = `2.16.840.1.113883.3.464.1003.101.12.1001`
4. 🌐 **Retrieve codes:** Use VSAC JSON API to get all relevant codes.
   API Endpoint:
   `https://datagateway.glaceemr.com/DataGatewayMediSpan/eCQMServices/getECQMInfoById?ids=318&reportingYear=2025`
5. 🔢 **Search for codes:**

   * **CPT Codes:** 99202, 99203, 99204, 99205, 99212, 99213, 99214, 99215
   * **SNOMED CT Codes:** 185463005, 185464004, 185465003, 3391000175108, 439740005

---

## 📝 Notes

* 📆 The measurement period is the full calendar year (Jan 1 - Dec 31).
* 🚫 Hospice patients are excluded from the denominator to ensure accurate quality reporting.
* 🩺 Falls screening can be documented on any day within the measurement period, independent of encounter date.
* ❌ No numerator exclusions or denominator exceptions apply.

---

## 📚 References

* Official specification URL: [CMS139v13 Falls Screening Spec](https://static.glaceemr.com/ECQM/2025/CMS139v13.html)
* VSAC API for value sets:  [VSAC eCQM Info (id=318)](https://datagateway.glaceemr.com/DataGatewayMediSpan/eCQMServices/getECQMInfoById?ids=318&reportingYear=2025)

---
