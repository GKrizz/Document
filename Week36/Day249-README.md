# 📄 BMI (CMS69v13) – EAAM Account Investigation

## Account Details

* **Account Name:** EREN AND ATLURI MDS, LLC (EAAM)
* **Providers Involved:**

  * Dr. **Sridhar Atluri, M.D.**
  * Dr. **Fer Eren, M.D.**
* **Measure:**
  **CMS69v13 – Preventive Care and Screening: Body Mass Index (BMI) Screening and Follow-Up Plan**
* **Internal Measure ID:** **128**
* **Years Requested:** **2025 and 2026**

---

## 1️⃣ What the Doctor Asked

Dr. Atluri requested:

> “Please add BMI (CMS 69) and Advance Care Planning (CMS 047) to our quality measure reporting for **2025 and 2026**. This is required for the **EQIP program**.”

---

## 2️⃣ What Support / Product Team Responded

* **Advance Care Planning (CMS 047)**
  ❌ Claims-based → **cannot be configured in MIPS UI**

* **BMI (CMS 69)**

  * BMI is **suppressed in traditional MIPS reporting from 2024 onwards**
  * However:

    * BMI **can be configured**
    * BMI **is implemented clinically**
    * BMI **can be reported via QRDA eCQM**
  * Issue raised:

    * EAAM was earlier on **legacy backend**
    * Because of this, **2026 BMI config was blocked**

Later confirmation:

> EAAM account **IS UPDATED to STABLE BACKEND**

➡️ Therefore **BMI CAN be configured for 2026**

---

## 3️⃣ Main Confusion That Triggered This Investigation

### Question raised internally:

> “If BMI is **not mapped to providers** and **not visible in MIPS**,
> how do we still see **patient BMI data for 2025**?”

---

## 4️⃣ What We Investigated (Step by Step)

### 🔹 Step 1: Check Provider Mapping in MIPS Tables

Checked `quality_measures_provider_mapping`

```sql
SELECT *
FROM quality_measures_provider_mapping
WHERE quality_measures_provider_mapping_measure_id = 128
  AND quality_measures_provider_mapping_reporting_year IN (2025, 2026);
```

✅ **Result:**

* **No rows for 2025 or 2026**
* Confirms **BMI is NOT mapped in MIPS provider mapping**

---

### 🔹 Step 2: Check if BMI Measure Exists Anywhere

Checked which measures exist in provider mapping:

```sql
SELECT DISTINCT
  quality_measures_provider_mapping_measure_id,
  quality_measures_provider_mapping_reporting_year
FROM quality_measures_provider_mapping;
```

❌ **Measure 128 not present**

➡️ Confirms:

> BMI is **not part of MIPS provider-measure mapping**

---

### 🔹 Step 3: Check Clinical Patient Data

Checked **clinical patient entries**:

```sql
SELECT DISTINCT
  quality_measures_patient_entries_provider_id,
  quality_measures_patient_entries_reporting_year
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_measure_id = '128';
```

✅ **Result:**

* Patient data exists from **2017 → 2025**
* For **2025**, providers:

  * Dr. Sridhar Atluri
  * Dr. Fer Eren

---

### 🔹 Step 4: Count Patients for 2025

```sql
SELECT
  pe.quality_measures_patient_entries_provider_id,
  ep.emp_profile_fullname,
  COUNT(*) AS patient_count
FROM quality_measures_patient_entries pe
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = pe.quality_measures_patient_entries_provider_id
WHERE pe.quality_measures_patient_entries_measure_id = '128'
  AND pe.quality_measures_patient_entries_reporting_year = 2025
GROUP BY pe.quality_measures_patient_entries_provider_id, ep.emp_profile_fullname;
```

✅ **Result:**

* **Dr. Fer Eren:** 130 patients
* **Dr. Sridhar Atluri:** 169 patients

---

## 5️⃣ Why Patient Data Exists Even Though MIPS Mapping Is Missing

### ✅ This is the KEY POINT

There are **two different layers**:

### 1. **Clinical / Backend Layer**

* Captures BMI using:

  * Vitals
  * CPT-II logic
  * QDM logic
* Stores data in:

  * `quality_measures_patient_entries`
* Used for:

  * **EQIP**
  * **HEDIS**
  * **QRDA eCQM**

✅ This layer **DOES NOT depend on MIPS UI mapping**

---

### 2. **MIPS Reporting UI Layer**

* Uses:

  * `quality_measures_provider_mapping`
* BMI (CMS 69):

  * **Suppressed from MIPS starting 2024**
  * Hence **not visible / not mapped**

❌ This does **NOT stop clinical capture**

---

## 6️⃣ Confirmation from HEDIS Configuration

Checked HEDIS tables:

### Active Years

```sql
SELECT *
FROM hedis_configuration;
```

✅ **2025 and 2026 are ACTIVE**

---

### Provider–Measure Mapping (HEDIS)

```sql
SELECT *
FROM hedis_measure_provider_configuration
WHERE hedis_measure_provider_configuration_measure_id = 128;
```

✅ BMI is configured for:

* Providers **1, 11, 12**
* Years **2025 and 2026**

➡️ Confirms BMI is **intentionally enabled for EQIP / HEDIS**

---

## 7️⃣ What Report We Finally Generated

### Provider-Level Summary (2025)

| Provider             | Total Patients | Eligible | Follow-Up Completed |
| -------------------- | -------------- | -------- | ------------------- |
| Fer Eren, M.D.       | 130            | 118      | 109                 |
| Sridhar Atluri, M.D. | 169            | 163      | 149                 |

📌 Generated using `quality_measures_patient_entries`

---

### Patient-Level Detailed Report

* Patient ID
* CMS69v13
* IPP / Denominator / Numerator
* Recorded Date
* Provider

➡️ Suitable for:

* EQIP submission
* Attachment-based requests
* Audit / validation

---

## 3️⃣ Systems Involved (Very Important)

### 🔹 Clinical / Backend Layer (Source of Truth)

| Component                 | Status        |
| ------------------------- | ------------- |
| BMI Logic                 | ✅ Implemented |
| CPT-II / Clinical Capture | ✅ Active      |
| Patient Entries           | ✅ Available   |
| QRDA eCQM                 | ✅ Supported   |

📌 **Table:** `quality_measures_patient_entries`

---

### 🔹 Reporting / Configuration Layer

| Layer            | Notes                          |
| ---------------- | ------------------------------ |
| MIPS UI          | BMI suppressed post-2024       |
| HEDIS / EQIP     | BMI configured                 |
| Provider Mapping | Exists via HEDIS configuration |

📌 **Table:** `hedis_measure_provider_configuration`

---

## 4️⃣ Tables & Their Meaning

### 🗂️ 1. `hedis_configuration`

Controls which **reporting years are active**

```sql
SELECT *
FROM hedis_configuration;
```

✅ 2025 and 2026 are **active**

---

### 🗂️ 2. `hedis_measure_provider_configuration`

Defines **which provider is configured for which measure/year**

```sql
SELECT *
FROM hedis_measure_provider_configuration
WHERE hedis_measure_provider_configuration_measure_id = 128;
```

✅ Confirms **BMI (128)** is configured for **2025 & 2026**

---

### 🗂️ 3. `quality_measures_patient_entries`

Stores **actual patient-level BMI data**

```sql
SELECT DISTINCT
  quality_measures_patient_entries_provider_id,
  quality_measures_patient_entries_reporting_year
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_measure_id = '128';
```

✅ Confirms **BMI data exists for 2025**

---

## 5️⃣ Provider-Level Summary Query (Use This First)

```sql
SELECT
  pe.quality_measures_patient_entries_reporting_year AS reporting_year,
  ep.emp_profile_fullname                            AS provider_name,
  COUNT(*)                                           AS total_patients,
  SUM(pe.quality_measures_patient_entries_denominator) AS eligible_patients,
  SUM(pe.quality_measures_patient_entries_numerator)   AS followup_completed
FROM quality_measures_patient_entries pe
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = pe.quality_measures_patient_entries_provider_id
WHERE pe.quality_measures_patient_entries_measure_id = '128'
  AND pe.quality_measures_patient_entries_reporting_year = 2025
GROUP BY reporting_year, provider_name
ORDER BY provider_name;
```

📌 **Use this query for management-level reporting**

---

## 6️⃣ Patient-Level Detailed Report Query

```sql
SELECT
  pe.quality_measures_patient_entries_reporting_year AS reporting_year,
  ep.emp_profile_fullname                            AS provider_name,
  pe.quality_measures_patient_entries_patient_id     AS patient_id,
  pe.quality_measures_patient_entries_cmsid          AS cms_id,
  pe.quality_measures_patient_entries_ipp            AS ipp,
  pe.quality_measures_patient_entries_denominator    AS denominator,
  pe.quality_measures_patient_entries_numerator      AS numerator,
  pe.quality_measures_patient_entries_updated_on     AS recorded_date
FROM quality_measures_patient_entries pe
LEFT JOIN emp_profile ep
  ON ep.emp_profile_empid = pe.quality_measures_patient_entries_provider_id
WHERE pe.quality_measures_patient_entries_measure_id = '128'
  AND pe.quality_measures_patient_entries_reporting_year = 2025
ORDER BY provider_name, recorded_date;
```

📌 **Use this for audit / EQIP / attachment-based requests**

---





