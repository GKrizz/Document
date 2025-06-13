# MIPS Performance Flow (`MUPerformanceRateController`)

## 🏁 Overview

This module powers the **Merit-Based Incentive Payment System (MIPS)** reporting for providers in a healthcare system. It includes logic to:

* Identify patients seen by each provider
* Generate & validate QDM measures
* Calculate MIPS performance scores

---

## 📂 Main Controller: `MUPerformanceRateController`

### 🎯 Purpose

Provides API endpoints to:

1. Get patients seen during a time frame (`getPatientsSeen`)
2. Generate and validate Quality Data Model (QDM) for reporting (`generateAndValidateQDM`)
3. Calculate provider MIPS performance metrics (`calculateMIPSPerformance`)

---

## 🔁 Method 1: `getPatientsSeen(...)`

### 📌 Signature

```java
Map<Integer, List<Integer>> getPatientsSeen(int mode, int reportingYear, Date startDate, Date endDate)
```

### 🔍 Description

Fetches the **list of patient IDs seen by each provider** during a specific date range depending on the `mode`.

### 🧭 Mode Logic

| Mode | Meaning                        | Action                                                  |
| ---- | ------------------------------ | ------------------------------------------------------- |
| 1    | Today                          | Fetch patients for **all providers** seen **today**     |
| 2    | Current Month                  | Use 1st and last day of the current month as date range |
| 3    | Reporting Period (from config) | Use provider-specific configured reporting dates        |
| 4    | Custom Dates                   | Use `startDate` and `endDate` passed to the method      |

### 🔗 Key Service Used

```java
performanceService.getPatientsSeen(providerId, startDate, endDate, reportingYear)
```

*This service executes a JPA query to fetch patient IDs seen via DOS and encounter tables.*

---

## 🧠 Method 2: `generateAndValidateQDM(...)`

### 📌 Signature

```java
boolean generateAndValidateQDM(String accountId, int patientID, int providerId, Integer reportingYear)
```

### 🔍 Description

Generates and validates **QDM measures** for a given patient and provider in a reporting year.

### 🛠️ Core Steps

1. Load provider configs (e.g., individual vs group reporting)
2. Build QDM payload (based on measures & codes)
3. Call external **QDM Validation Service**
4. Parse response, save results to DB
5. Log audit trails and handle errors

### 🔗 Key Services & Utils

* `EMeasureUtils.buildQDMRequest()`
* `HttpConnectionUtils.postData(...)`
* `measureService.saveMeasureDetails(...)`

---

## 📊 Method 3: `calculateMIPSPerformance(...)`

### 📌 Signature

```java
boolean calculateMIPSPerformance(int reportingYear, String accountID, boolean isMonthlyReport)
```

### 🔍 Description

Calculates overall **MIPS composite scores** for each provider by combining:

* eCQM (Quality) scores
* Promoting Interoperability (PI) scores
* Improvement Activity (IA) scores

### 🧭 Execution Flow

1. Call `getPatientsSeen(...)` → get provider-to-patients map
2. For each provider:

   * Calculate eCQM metrics (`measureService.getPerformanceCount`)
   * Update metrics (`performanceService.addToMacraMeasuresRate`)
   * Get PI & IA scores
   * Apply CMS weightage to compute final score
   * Optionally update **monthly summary**

### 🧮 Weights (Sample CMS Rules)

| Component | Max Points | Weight (%) |
| --------- | ---------- | ---------- |
| eCQM      | 60         | 30         |
| PI        | 100        | 25         |
| IA        | 40         | 15         |

> ✅ If Health Equity (HE) is claimed, PI weight shifts to Quality.

---

## 📦 Key Supporting Services

| Service                 | Responsibility                                  |
| ----------------------- | ----------------------------------------------- |
| `performanceService`    | Patient mapping, score calculation, summaries   |
| `measureService`        | Measure metadata, performance counts, IA points |
| `providerConfService`   | Provider-specific reporting configuration       |
| `HttpConnectionUtils`   | Sends QDM payloads to validation service        |
| `auditTrailSaveService` | Logs audit events                               |

---

## 🧩 Useful Entities

| Entity                | Purpose                         |
| --------------------- | ------------------------------- |
| `PatientRegistration` | Stores core patient data        |
| `MacraProviderQDM`    | Stores provider config for MIPS |
| `MacraMeasuresRate`   | Stores performance metrics      |
| `MipsMonthlySummary`  | Monthly summaries per provider  |

