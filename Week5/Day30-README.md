# Method 3: `calculateMIPSPerformance()`

## Purpose

Calculates MIPS (Merit-Based Incentive Payment System) performance for providers based on a given reporting year and account. The calculation can be performed either for the full year or on a monthly basis, controlled by the `isMonthlyReport` flag.

---

## Overview of Steps

### 1. Initialize Variables

---

### 2. Fetch Patients Seen

* Use `getPatientsSeen(...)` method to retrieve patients seen by each provider.
* **Modes:**

  * Mode 3: Full year data
  * Mode 2: Monthly data

---

### 3. Initialize Shared Variables

These are used later for fetching eCQM info or saving performance data.

```java
String sharedPath = sharedFolderBean.getSharedFolderPath();
String gatewayURL = measureService.getHubServerUrl();
```

---

### 4. Loop Through Each Provider

#### a. Get Provider Info

* Use `getCompleteProviderInfo(providerId, year)` which returns:

  * MACRA provider configuration data (e.g., reporting start/end dates, method, mail ID)
  * Mapped quality measures (excluding Improvement Activities (IA) measures)
* Data Source: `macra_provider_configuration` table
* Steps inside:

  1. Retrieve MACRA provider configuration
  2. Retrieve mapped quality measures (non-IA) and store in the first `MacraProviderQDM` entry

#### b. Calculate eCQM Performance

* **Step 1:** Call `getPerformanceCount()`

  * Aggregates data from `quality_measures_patient_entries` table
  * Metrics: IPP, Denominator, Numerator, Denominator Exclusion, Numerator Exclusion, Denominator Exception
* **Step 2:** Calculate Performance & Reporting Rates

  ```java
  denomCount = denominatorCount - denominatorExclusionCount - denominatorExceptionCount;
  numCount = numeratorCount - numeratorExclusionCount;
  performanceRate = (numCount / denomCount) * 100;
  reportingRate = (numCount / denomCount) * 100;
  ```
* **Step 3:** Scoring Logic

  * Converts reportingRate to a score out of 10
  * Applies weighting (×2, ×4, etc.) for ACI measures
* **Step 4:** CMS ID and Title Retrieval via `getCMSIdAndTitle(...)`

  * Fetches from cached JSON or external REST API
* **Step 5:** Submission Method Retrieval via `bringSubmissionMethod(year)`
* **Step 6:** eMeasure Details via `getMeasureBeanDetails(...)`

  * Calls `getMeasureInfo(gateway_url, year, measureId, sharedPath, accountId)`
* **Step 7:** Points Calculation via `getMeasurePoints(...)`

  * Checks for topped-out measures (max 7 points)
  * Defaults to 3 points if benchmark unavailable

#### c. Monthly Summary (Only on Last Day of Month)

* Call `addToMIPSMonthlySummary(...)`
* Saves aggregated data to `mips_monthly_summary` table

#### d. If Reporting Year <= 2018, Calculate Old PI Measures

* Use `getPerformanceCount(...)` with JPA CriteriaBuilder filters on `quality_measures_patient_entries`
* Aggregate metrics using SUM()
* Calculate performanceRate and reportingRate with adjusted denominator
```java
  ✅ Performance Rate
        performanceRate = ((numerator - numeratorExclusions) / adjustedDenominator) * 100
  ✅ Reporting Rate
        reportingRate = ((numerator - numeratorExclusions) / adjustedDenominator) * 100

adjustedDenominator = denominator - exclusions - exceptions
  ```
* Map scores to points accordingly

#### e. Check for Hardship Exception (HE Claimed)

* Fetch ACI measures performance with `getPerformanceCount(...)`
* Save or update records in `macra_measures_rate` table (overall reporting period) using `addToMacraMeasuresRate(...)`
* On last day of month, save data in `mips_monthly_summary` via `addToMIPSMonthlySummary(...)`

#### f. If Year >= 2020 and Method is eCQM

* Conditions:

  * Reporting year 2020 or later
  * Provider’s reporting method = QRDA (code 2)
* Use `getMacraScores(...)` which calculates:

  1. **eCQM Quality Category** (max 60 points)

     * Top 6 valid eCQMs with criteria == 1
     * At least 1 Outcome or High-Priority measure required
  2. **PI (Promoting Interoperability)** (max 100 points)

     * ACI\_EP\_1, ACI\_HIE\_1, ACI\_HIE\_2 mapped to PI\_\*
     * Handles exclusions with `getPIExclusionStatus(...)`
     * Zero performance → zero points → triggers totalScoreCond
     * Checks attestation status; if not attested, PI points = 0
     * Public Health Exchange Attestation requires 2/5 successes; otherwise PI = 0
  3. **IA (Improvement Activities)** (max 40 points)

     * Straightforward value between 0–40
* Final weightage calculation:

  * With Hardship Exception:

    ```
    totalEcqmPoints = (ecqmPoints / 60) * (qualityWt + piWt)
    ```
  * Without Hardship Exception:

    ```
    totalEcqmPoints = (ecqmPoints / 60) * qualityWt;
    piPoints = (piPoints / 100) * piWt;
    ```
  * IA score always:

    ```
    (iaScore / 40) * iaWt
    ```
* Save final scores to `mips_scores` table via `addToMipsScores()`

---

## Process Flow Diagram

```
Start
  ↓
Fetch Patients by Provider
  ↓
For Each Provider
  ├── Fetch Config + Measures
  ├── Get Performance Counts (ECQM/PI)
  ├── Update Performance
  ├── If last day of month → Save Monthly Summary
  ├── If year <= 2018 → Use ACI_TRANS_* measures
  ├── If HE not claimed → Use ACI_* measures
  └── If year >= 2020 and Method = eCQM → Score + Save
  ↓
Return true if all OK, false if exception
```
---

## Table Descriptions

| Table                               | Description                                                         |
| ----------------------------------- | ------------------------------------------------------------------- |
| `macra_provider_configuration`      | Stores provider-specific MIPS reporting setup and method details.   |
| `quality_measures_provider_mapping` | Maps selected quality measures to each provider and reporting year. |
| `quality_measures_patient_entries`  | Tracks patient-wise quality measure data per provider and year.     |
| `mips_monthly_summary`              | Stores monthly summary data for MIPS performance metrics.           |
| `macra_measures_rate`               | Holds calculated measure performance rates across providers.        |
| `mips_scores`                       | Stores the final calculated MIPS scores (Quality, PI, IA, Total).   |

---
