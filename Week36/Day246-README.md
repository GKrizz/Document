# MIPS – Frequently Asked Questions (FAQ)

**Date:** January 19, 2026
**Day:** 246
**Document Type:** ReadMe
**Module:** MIPS / MACRA

---

## Overview

This document provides commonly reported issues, explanations, and resolutions related to **MIPS (Merit-based Incentive Payment System)** reporting, configuration, and measure calculations within the system.

---

## Frequently Asked Questions

### 1. MIPS report not updating properly

**Possible Causes:**

* MIPS update job not executed
* Server-related issues
  **Resolution:**
* Run the MIPS update job
* Verify server status

---

### 2. Depression Screening score is low despite documentation

**Possible Causes:**

* Missing follow-up documentation for positive screenings
* Incorrect SNOMED / LOINC codes

---

### 3. Help documents are not up to date

**Explanation:**

* CMS releases updated measure specifications annually
* Updates are manually reviewed and applied
  **Note:** There may be a delay between CMS release and system updates

---

### 4. Hemoglobin A1c documented but not reflected in MACRA/MIPS

**Possible Cause:**

* GlaceCDS service not running
  **Resolution:**
* Restart GlaceCDS on all three servers
* Review GlaceCDS logs
  **Result:** Values will reflect correctly after restart

---

### 5. Generate QRDA-I and QRDA-III reports

**Steps:**

1. Navigate to **Reports**
2. Open **MIPS Performance Report**
3. Select **QRDA-I** or **QRDA-III**
4. Download the XML file

---

### 6. Issues in QRDA-I & QRDA-III report generation

**Common Causes:**

* Report generation timeout
* Special characters (symbols) in patient names
  These can interrupt XML generation

---

### 7. Configure PQRS tab

**Action Required:**

* Raise a request with the configuration team to enable and configure PQRS tabs

---

### 8. Generate PCMH reports

* PCMH reports are generated based on CMS measure specifications
* Proper measure configuration is required

---

### 9. High-Risk Medication in the Elderly shows high “Not Met”

**Explanation:**

* This is an **inverse quality measure**
* When numerator conditions indicating potentially inappropriate prescribing are met, patients are counted as **Not Met**
* Lower scores indicate better quality

---

### 10. Tobacco Measure – Criteria 2 count is low

**Reason:**

* Criteria 2 applies only to patients identified as tobacco users
* Fewer tobacco users result in a lower count

---

### 11. Smoking status documented but still “Not Met”

**Explanation:**

* Smoking status alone is insufficient
* Tobacco cessation counseling or treatment must also be documented

---

### 12. Patient information icon missing for some measures

**Reason:**

* Certain measures do not support patient-level drill-down per CMS specifications

---

### 13. Improvement Activities showing incorrect weightage

**Possible Cause:**

* Incorrect configuration or performance year mapping

---

### 14. All measures show “NA”

**Cause:**

* GlaceCDS service is not running
  **Resolution:**
* Restart GlaceCDS

---

### 15. Blood Pressure / Current Medication count mismatch

**Explanation:**

* These are visit-based (episode-based) measures
* Multiple visits for one patient may count as one

---

### 16. Denominator Exception documentation

**Note:**

* Not all measures support denominator exceptions
* Measure specifications must be reviewed before documentation

---

### 17. No score for Fall Screening / Depression Screening from XML

**Explanation:**

* Scores are not calculated directly from XML tabs
* XML data is copied from `leaf_tab_patient` to `patient_assessment`

---

### 18. “Not Met” count is zero but score is showing

**Explanation:**

* Measure is visit-based
* Score is calculated using eligible visits, not patient count

---

### 19. Meaning of “Partially Met”

**Definition:**

* Some measure requirements are met, but not all required criteria

---

### 20. BP and follow-up documented but still “Not Met”

**Possible Cause:**

* Incomplete or incorrectly coded follow-up documentation

---

### 21. HCPCS code documented but measures show “NA”

**Possible Cause:**

* Missing measure configuration or performance year mapping

---

### 22. Specification files throwing errors

**Cause:**

* Incorrect or unsupported specification file versions

---

### 23. Measures not configured properly

**Possible Causes:**

* Incomplete configuration
* Incorrect provider, measure, or performance year mapping

---

### 24. MIPS-related tabs and elements not available

**Possible Causes:**

* Missing configuration
* Insufficient user role permissions

---

### 25. Generate “Not Met” patient list

**Action Required:**

* Create and run a manual database query

---

### 26. Configure CPT II code automation

**Requirements:**

* Provider ID
* Measure ID
* Performance Year
  All mappings must be configured correctly

---

### 27. Update MIPS Performance Report

**Action:**

* Run the MIPS performance update job

---

### 28. Patient on Atorvastatin showing “Not Met”

**Possible Cause:**

* Supporting diagnosis, age criteria, or duration requirements not met

---

### 29. Measures with “No Benchmark” still showing scores

**Explanation:**

* Scores are calculated internally
* CMS QPP does not convert them into official scores

---

### 30. Stratification filter not working

**Cause:**

* Incorrect filter logic

---
