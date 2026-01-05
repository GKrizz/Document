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


# 🧠 BIG PICTURE: What is MIPS (in one line)

**MIPS = a scoring system by CMS that decides whether a doctor gets MORE money, LESS money, or NO change in Medicare payments based on performance.**

Everything in this document exists to answer **one question**:

> “Did this provider deliver quality care in this year?”

---

# 1️⃣ MIPS FUNDAMENTALS (CONCEPT LEVEL)

### Why MIPS exists

CMS moved from:
❌ *“More patients = more money”*
to
✅ *“Better care = more money”*

So CMS measures doctors on **4 categories**:

| Category                            | What it checks                                          |
| ----------------------------------- | ------------------------------------------------------- |
| **Quality**                         | Did the doctor follow clinical best practices?          |
| **Promoting Interoperability (PI)** | Did they use EHR properly?                              |
| **Improvement Activities (IA)**     | Did they improve care processes?                        |
| **Cost**                            | Did they avoid unnecessary costs? (CMS calculates this) |

Each category gives points → **Final Score (0–100)** → **Payment adjustment (after 2 years)**

---

# 2️⃣ HOW GLENWOOD IMPLEMENTS MIPS (SYSTEM VIEW)

Think of Glenwood MIPS as **6 layers**:

```
CMS Rules
   ↓
Yearly Measure Data (ECQI / CMS)
   ↓
Database Tables
   ↓
Provider Configuration
   ↓
MIPS Job (Batch)
   ↓
Reports + Validation + Export
```

---

# 3️⃣ DATABASE TABLES (VERY IMPORTANT 🔥)

These tables are the **backbone**.

### A. Patient-level truth tables

| Table                                        | Meaning                                              |
| -------------------------------------------- | ---------------------------------------------------- |
| **quality_measures_patient_entries**         | Final status (Met / Not Met) per patient per measure |
| **quality_measures_patient_entries_history** | Audit/history of changes                             |

👉 *This answers:*
“Why is THIS patient NOT MET for THIS measure?”

---

### B. Provider configuration tables

| Table                                 | Purpose                              |
| ------------------------------------- | ------------------------------------ |
| **quality_measures_provider_mapping** | Which doctor reports which measures  |
| **macra_provider_configuration**      | Reporting period + submission method |
| **macra_configuration**               | Year-level MIPS rules                |

👉 *This answers:*
“What did the provider promise CMS to report?”

---

### C. Scoring tables

| Table                   | Purpose                             |
| ----------------------- | ----------------------------------- |
| **macra_measures_rate** | Numerator / denominator per measure |
| **mips_scores**         | Final score (for PDF & dashboard)   |
| **mips_weightage**      | Category weights per year           |

👉 *This answers:*
“How many points does the doctor get?”

---

### D. Special cases

| Table                                   | Why                                |
| --------------------------------------- | ---------------------------------- |
| **pi_exclusion / pi_exclusion_details** | PI hardship                        |
| **ia_measures**                         | Improvement Activity documentation |
| **mips_notification**                   | Threshold alerts                   |

---

# 4️⃣ YEARLY DATA IMPORT (MOST CONFUSING PART — NOW SIMPLE)

Every **January**, CMS changes rules.

So Glenwood must **IMPORT CMS DATA**.

### Core idea:

> “We copy last year → update CMS changes → remove retired measures → add new ones”

---

## 🔹 quality_measures_2024

This table defines **WHAT measures exist**.

Steps:

1. Copy last year table
2. Rename benchmark column
3. Delete **removed CMS measures**
4. Update **CMS versions** (v11 → v12 etc.)
5. Update:

   * UUIDs
   * Version GUIDs
   * Criteria JSON
   * Stratification IDs
6. Add **new v1 measures**

👉 This table = **Master definition of quality measures**

---

## 🔹 submission method table

**quality_measures_submission_method_2024**

Defines:

> “Can this measure be submitted via EHR / Claims / Registry?”

Mostly:

* Copy last year
* Remove retired measures
* Add new measures as `EHR`

---

## 🔹 ECQM specifications

**ecqm_specifications_2024**

This maps:

> Measure → Value sets → QDM category

Used during **GlaceCDS validation**.

---

## 🔹 Additional valuesets

Manual, risky table 😅
Used when CMS data is **incomplete**.

Example:

* Statin contraindications
* Single-code measures

---

## 🔹 Benchmarks & IA

| Table                                | Source          |
| ------------------------------------ | --------------- |
| **mips_benchmark_2024**              | CMS benchmarks  |
| **mips_improvement_activities_2024** | IA CSV from CMS |

---

## 🔹 Registry & Claims

Used for **non-EHR submissions**, mostly CMS validation.

---

## 🔹 Final APIs (VERY IMPORTANT)

After import, you **MUST run**:

```text
updateECQMSpecification
updatePQRSSpecification
```

Otherwise:
❌ UI looks fine
❌ Job runs
❌ BUT data is WRONG

---

# 5️⃣ PROVIDER CONFIGURATION (FUNCTIONAL FLOW)

### Where?

```
Configure → General Practice → Meaningful Use → QPP Configuration
```

### What happens?

1. Select **Year**
2. Select **Doctor**
3. Choose **Reporting period**
4. Submission method = EHR
5. Add **Quality Measures**
6. Save

This creates rows in:

* `macra_provider_configuration`
* `quality_measures_provider_mapping`

---

# 6️⃣ MIPS JOB (HEART OF THE SYSTEM ❤️)

Batch Job:

```
MIPSPerformanceJob
```

### Modes (IMPORTANT)

| Mode | Meaning               |
| ---- | --------------------- |
| 1    | Only changed patients |
| 2    | Current month         |
| 3    | FULL YEAR (most used) |
| 4    | Date range            |

### What the job does:

1. Load patients
2. Collect codes (CPT, LOINC, SNOMED, CVX)
3. Build QDM request
4. Call **GlaceCDS**
5. Insert patient status
6. Calculate numerator/denominator
7. Update scores

---

# 7️⃣ REPORTS & UI (WHAT USERS SEE)

### Dashboard

* Provider score
* Category scores
* Group (TIN-based) score

### Quality tab

* Numerator
* Denominator
* Exclusions
* Rate
* Points

### Patient drill-down

* Click number → patient list
* Click info → measure reason

---

# 8️⃣ MIPS FLOWSHEET (DEBUGGING TOOL 🔍)

This is your **truth screen**.

Used to:

* See real-time patient measure status
* Document NOT MET measures
* Trigger re-validation

Flowsheet → Pencil icon → Document → Save → Revalidate via CDS

---

# 9️⃣ VALIDATION FLOW (VERY IMPORTANT FOR DEV)

```
Patient Data
   ↓
getEPStatusByPatient API
   ↓
GlaceCDS validateECQM
   ↓
QualityMeasuresPatientEntries
```

Only **some measures allow manual documentation** (BMI, Flu, Tobacco, Depression, etc.)

---

# 🔟 EXPORTS (FINAL OUTPUT)

| Format       | Used for           |
| ------------ | ------------------ |
| PDF          | Provider review    |
| Excel        | Internal analysis  |
| QPP JSON     | CMS submission     |
| QRDA I / III | Cypress validation |

---

# ✅ HOW YOU SHOULD STUDY THIS (RECOMMENDED)

### Phase 1 – Concept

* Understand **4 categories**
* Understand **score → payment**

### Phase 2 – Data

* Learn **quality_measures_20xx**
* Learn **patient_entries**

### Phase 3 – Job

* Understand **MIPSPerformanceJob**
* Understand **GlaceCDS**

### Phase 4 – UI & Debug

* Flowsheet
* Patient drill-down
* Edit screen

---
