# 🗓️ Day 26 – June 13, 2025 (Friday)

## 📂 Main Controller: `MUPerformanceRateController`

### 🎯 Purpose

The `MUPerformanceRateController` is responsible for managing endpoints related to MIPS (Merit-based Incentive Payment System) performance and reporting. It provides three key functionalities:

1. **Get Patients Seen**
   **Method:** `getPatientsSeen`
   Retrieves the list of patients seen by a provider within a given time frame.

2. **Generate and Validate QDM**
   **Method:** `generateAndValidateQDM`
   Generates and validates the **Quality Data Model (QDM)** for clinical quality measure reporting. Ensures compliance with required standards.

3. **Calculate MIPS Performance**
   **Method:** `calculateMIPSPerformance`
   Calculates provider performance metrics according to MIPS guidelines. Used for quality scoring and CMS submissions.

---

## 🔍 Task: Analyze the Flow

The objective for today was to **analyze the internal flow** of the following methods in the `MUPerformanceRateController`:

* `getPatientsSeen`
* `generateAndValidateQDM`
* `calculateMIPSPerformance`

This includes understanding:

* Input parameters and validation
* Service layer interactions
* Data transformation processes
* Output structure and responses
* Error handling patterns

---
Sure! Here's a clean and well-structured `README.md` file based on the detailed content you provided. You can save this into a file named `README.md` in your project directory.

---

## 🔁 Method 1: `getPatientsSeen(...)`

### 📌 Signature
```java
Map<Integer, List<Integer>> getPatientsSeen(int mode, int reportingYear, Date startDate, Date endDate)
````

### 🔍 Description

Fetches the list of patient IDs seen by each provider during a specific date range depending on the mode.

### 🧭 Mode Logic

| Mode | Meaning          | Action                                           |
| ---- | ---------------- | ------------------------------------------------ |
| 1    | Today            | Fetch patients seen today                        |
| 2    | Current Month    | Use 1st and last day of the current month        |
| 3    | Reporting Period | Use provider-specific configured reporting dates |
| 4    | Custom Dates     | Use startDate and endDate passed to the method   |

### 🔗 Key Service Used

```java
performanceService.getPatientsSeen(providerId, startDate, endDate, reportingYear)
```

---

## 🧠 Method 2: `generateAndValidateQDM(...)`

### 📌 Signature

```java
boolean generateAndValidateQDM(String accountId, int patientID, int providerId, Integer reportingYear)
```

### 🔍 Description

Generates and validates QDM measures for a given patient and provider in a reporting year.

### 🛠️ Core Steps

* Load provider configs
* Build QDM payload (based on measures & codes)
* Call external QDM Validation Service
* Parse response, save results to DB
* Log audit trails and handle errors

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

Calculates overall MIPS composite scores for each provider using:

* eCQM (Quality) scores
* Promoting Interoperability (PI) scores
* Improvement Activity (IA) scores

### 🧭 Execution Flow

1. Call `getPatientsSeen(...)`
2. For each provider:

   * Calculate eCQM metrics via `measureService.getPerformanceCount()`
   * Update metrics using `performanceService.addToMacraMeasuresRate()`
   * Get PI & IA scores
   * Apply CMS weightage to compute final score
   * Optionally update monthly summary

### 🧮 Sample Weights (CMS Rules)

| Component | Max Points | Weight (%) |
| --------- | ---------- | ---------- |
| eCQM      | 60         | 30         |
| PI        | 100        | 25         |
| IA        | 40         | 15         |

➡️ If Health Equity (HE) is claimed, PI weight shifts to Quality.




