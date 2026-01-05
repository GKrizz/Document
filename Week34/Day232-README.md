# 1. INTRODUCTION – WHAT MIPS IS AND WHY IT EXISTS

### What is MIPS?

**MIPS (Merit-based Incentive Payment System)** is a program created by **CMS (Centers for Medicare & Medicaid Services)** under the **Quality Payment Program (QPP)**.

Its purpose is to **change how Medicare pays doctors**:

* ❌ Not just based on number of visits
* ✅ Based on **quality, efficiency, and outcomes**

Doctors who perform well:

* Get **higher Medicare payments**

Doctors who perform poorly:

* Get **penalties**

---

## Why CMS Introduced MIPS

MIPS replaced **three older programs**:

* PQRS (Quality reporting)
* Value-Based Modifier (Cost control)
* Meaningful Use (EHR usage)

Instead of three programs → **ONE unified scoring system**

---

# 2. PURPOSE OF MIPS (BUSINESS GOALS)

MIPS exists to achieve **7 major goals**:

### 1. Improve Quality of Care

Doctors are rewarded for:

* Preventive care
* Chronic disease management
* Proper documentation

---

### 2. Shift to Value-Based Care

Payments are tied to:

* How well patients are treated
* Not how many services are billed

---

### 3. Improve Patient Outcomes

Focus areas:

* Screenings
* Vaccinations
* Follow-up care
* Risk management

---

### 4. Reduce Healthcare Costs

Doctors are encouraged to:

* Avoid unnecessary tests
* Reduce hospital readmissions
* Use resources wisely

---

### 5. Measure & Report Performance

Doctors must **prove performance** using data:

* Quality measures
* Improvement activities
* EHR usage

---

### 6. Continuous Improvement

CMS gives **feedback reports** so providers can:

* See weak areas
* Improve year over year

---

### 7. Align Incentives with National Healthcare Goals

Examples:

* Better medication management
* Care coordination
* Health equity

---

# 3. WHO PARTICIPATES IN MIPS (ELIGIBILITY)

MIPS applies to:

* Physicians
* Nurse Practitioners
* Physician Assistants
* Clinical Nurse Specialists
* Group practices

Eligibility depends on:

* Medicare Part B billing volume
* Number of Medicare patients
* CMS thresholds

---

# 4. MIPS PERFORMANCE CATEGORIES (VERY IMPORTANT)

Each eligible clinician is evaluated under **4 categories**.

---

## 1️⃣ Quality

**Replaces PQRS**

Measures:

* Screenings
* Chronic condition care
* Preventive care

Examples:

* Diabetes HbA1c
* Breast cancer screening
* Blood pressure control

---

## 2️⃣ Promoting Interoperability (PI)

**Replaces Meaningful Use**

Measures:

* EHR usage
* Health Information Exchange (HIE)
* E-prescribing
* Patient portal usage

Hardship exceptions may apply.

---

## 3️⃣ Improvement Activities (IA)

Measures:

* Care coordination
* Patient engagement
* Safety improvements
* Access to care

---

## 4️⃣ Cost

Calculated by CMS using:

* Medicare claims
* No provider reporting needed

---

# 5. MIPS SCORING & PAYMENT ADJUSTMENT

* Each category has a **weight**
* Scores are combined → **Final Score (0–100)**

### Payment Impact:

* High score → bonus
* Low score → penalty
* Neutral score → no change

⚠️ Payment impact happens **2 years later**
Example:

* 2024 performance → affects 2026 payment

---

# 6. MIPS PROCESS – STEP BY STEP

## Step 1: Eligibility Check

Confirm provider is eligible for the reporting year.

---

## Step 2: Measure Selection

Choose measures based on:

* Specialty
* Reporting method (EHR / claims / registry)

---

## Step 3: Data Collection

Data comes from:

* EHR documentation
* Claims
* QRDA files
* Registries

---

## Step 4: CMS Evaluation

CMS calculates:

* Category scores
* Final MIPS score

---

## Step 5: Feedback

CMS provides reports to:

* Improve next year’s performance

---

# 7. CORE DATABASE TABLES (FUNCTIONAL UNDERSTANDING)

| Table                                      | What it Stores                        |
| ------------------------------------------ | ------------------------------------- |
| `quality_measures_patient_entries`         | Patient-level status for each measure |
| `quality_measures_provider_mapping`        | Measures configured per provider/year |
| `macra_measures_rate`                      | Numerator/denominator counts          |
| `macra_provider_configuration`             | Provider MIPS setup                   |
| `mips_scores`                              | Final scores                          |
| `mips_weightage`                           | Category weights                      |
| `quality_measures_patient_entries_history` | Audit history                         |
| `macra_configuration`                      | Year-specific MIPS rules              |
| `ia_measures`                              | Improvement Activities                |
| `pi_exclusion`                             | PI exclusions                         |
| `pi_exclusion_details`                     | Applied exclusions                    |

---

# 8. CODE STRUCTURE (WHAT EACH LAYER DOES)

### Legacy (JSP + Actions)

* UI rendering
* Older workflow
* Patient flowsheets
* Performance reports

---

### Spring Layer

* REST APIs
* Business logic
* Database access
* QRDA generation
* Scoring calculations

---

### CDS Integration

* Measure validation
* ECQM rule execution
* Patient eligibility logic

---

# 9. MIPS DATA IMPORTING (YEARLY MAINTENANCE)

Every year CMS:

* Adds measures
* Removes measures
* Updates versions
* Changes benchmarks

Your system must **import all this data**.

---

## Key Import Tables

* `quality_measures_2024`
* `ecqm_specifications_2024`
* `additional_valuesets_2024`
* `mips_benchmark_2024`
* Claims & Registry tables

---

## Why This Is Critical

❌ Wrong import → wrong score
❌ Wrong benchmark → penalty risk
❌ Missing value set → measure fails

---

# 10. QUALITY MEASURES IMPORT – LOGIC

1. Copy previous year
2. Remove CMS-removed measures
3. Update CMS version numbers
4. Update GUIDs & XML metadata
5. Update criteria JSON
6. Add new measures
7. Update submission methods

---

# 11. VALUE SETS & ECQM SPECIFICATIONS

### ecqm_specifications

Contains:

* Value sets
* QDM categories
* Code mappings

Data source:

* VSAC
* CMS Excel files
* Measure XML

---

### additional_valuesets

Used when:

* CMS does NOT supply value sets
* Single-code logic needed
* Contraindications needed

⚠️ Manual validation required

---

# 12. BENCHMARKS & IMPROVEMENT ACTIVITIES

Benchmarks:

* Used to convert performance → score

Improvement Activities:

* Determines IA score

Both downloaded yearly from CMS.

---

# 13. FINAL API STEPS (MANDATORY)

After all imports:

### Update ECQM

```
updateECQMSpecification?reportingYear=2024
```

### Update PQRS

```
updatePQRSSpecification?reportingYear=2024
```

These APIs:

* Populate runtime tables
* Activate measures for calculation

---

# 14. PROVIDER MEASURE CONFIGURATION (UI FLOW)

1. Configure → QPP Configuration
2. Select provider
3. Set reporting period
4. Choose EHR submission
5. Add measures
6. Save
7. Verify codes & labs
8. Run MIPS batch job

---

# 15. MIPS BATCH JOB (CALCULATION ENGINE)

Job:

* Collects patients
* Validates measures
* Calculates counts
* Stores scores

Modes decide:

* Which patients are processed
* What time period

---

# 16. MIPS DASHBOARD & REPORTS

### Dashboard

* Individual score
* Group (TIN) score

---

### Exports

* PDF → summary
* Excel → patient-level detail
* JSON → QPP submission

---

# 17. VALIDATION FLOW (TECHNICALL CORE)

1. Load patients
2. Generate QDM
3. Send to GlaceCDS
4. Validate measures
5. Insert results
6. Calculate numerator/denominator
7. Generate reports

---

# 18. MIPS FLOWSHEET & DOCUMENTATION

* Shows per-patient measure status
* “Not Met” → documentation allowed
* After documentation → revalidation

---

# 19. VISIT EXCLUSION

Allows:

* Excluding certain visit types
* Recalculating reports
* Exporting adjusted results

---

# 20. COMMON FAQ (WHY COUNTS DON’T MATCH)

Most issues happen due to:

* Incorrect documentation location
* Missing TOC
* Wrong timing
* Measure-specific logic

---

# 21. QRDA & CYPRESS VALIDATION

* QRDA I → patient level
* QRDA III → aggregate
* Cypress validates CMS compliance

Used before:

* QPP submission
* Certification

---

# FINAL SIMPLE SUMMARY

Think of MIPS as:

> **A yearly pipeline that converts patient documentation → quality scores → Medicare payment changes**

This documentation explains:

* What CMS expects
* How EMR processes data
* How providers are scored
* How reports are generated
* How validation ensures correctness

---
