
# 📘 Glace EMR – Patient → Visit → Measures


---

## 1️⃣ Patient & Visit Structure

These tables form the **foundation** for all measures.

| Order | UI Area              | Purpose              | Table                  | Key Columns                                                                                                      | Used For                 |
| ----: | -------------------- | -------------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------ |
|     1 | Patient Registration | Patient demographics | `patient_registration` | `patient_registration_id`, `patient_registration_dob`, `patient_registration_sex`, `patient_registration_active` | Age / gender eligibility |
|     2 | Chart                | Patient container    | `chart`                | `chart_id`, `chart_patientid`                                                                                    | Links encounters         |
|     3 | Encounter            | Visit record         | `encounter`            | `encounter_id`, `encounter_chartid`, `encounter_date`, `encounter_provider_id`                                   | Visit grouping           |

> ℹ️ **Note:** Encounter provides context only.
> Billing, provider attribution, and measure eligibility come from **Superbill**.

---

## 2️⃣ Superbill (Visit & Billing – 🚨 STARTS ALL MEASURES)

**UI Tab:** Superbill
**Purpose:** Defines the visit, provider, and CPT codes used by CMS.

| UI Tab        | Purpose     | Table(s)         | Key Columns                | Measure Impact              |
| ------------- | ----------- | ---------------- | -------------------------- | --------------------------- |
| **Superbill** | CPT / Visit | `service_detail` | `service_detail_patientid` | 🔑 Initial Population (IPP) |
|               |             |                  | `service_detail_dos`       | Measurement period          |
|               |             |                  | `service_detail_sdoctorid` | Provider attribution        |
|               |             | `cpt`            | `cpt_cptcode`              | Eligible encounter          |

🚨 **Golden Rule**
If a visit is **not in `service_detail`**, it **does not exist** for CMS/MIPS.

---

## 3️⃣ Assessment (Diagnosis – ICD-10)

**UI Tab:** Assessment
**Purpose:** Determines diagnosis-based inclusion or exclusion.

| Diagnosis Type     | Table                 | Key Columns                                                                     | Usage                        |
| ------------------ | --------------------- | ------------------------------------------------------------------------------- | ---------------------------- |
| Encounter-level DX | `patient_assessments` | `patient_assessments_dxcode`, `patient_assessments_encounterdate`               | Acute / visit-based measures |
| Chronic DX         | `problem_list`        | `problem_list_dx_code`, `problem_list_onset_date`, `problem_list_resolved_date` | Longitudinal measures        |

📌 **Rule of Thumb**

* Acute conditions → `patient_assessments`
* Chronic conditions → `problem_list`

---

## 4️⃣ Current Medications (Treatment / Numerator Driver)

**UI Tab:** Current Medications
**Purpose:** Determines treatment-based numerator logic.

| UI Tab                  | Table                | Key Columns                                                                                                                    | Measure Use                 |
| ----------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------ | --------------------------- |
| **Current Medications** | `current_medication` | `current_medication_rx_name`, `current_medication_start_date`, `current_medication_patient_id`, `current_medication_is_active` | Antibiotics, High-Risk Meds |

📌 If a medication is **not recorded here**, it is **not counted** for CMS.

---

## 5️⃣ Investigations (Labs / Procedures / Tests)

**UI Tab:** Investigation
**Purpose:** Lab-based numerator evaluation.

### Table Responsibilities

| Layer        | Table                   | What It Stores          | Used by CMS |
| ------------ | ----------------------- | ----------------------- | ----------- |
| Lab order    | `lab_entries`           | Test ordered, date      | ❌ No        |
| ⭐ Lab result | `lab_entries_parameter` | **ACTUAL RESULT VALUE** | ✅ YES       |
| Test master  | `lab_description`       | UI labels / grouping    | ❌ No        |
| Metadata     | `lab_parameters`        | Units / ranges          | ❌ No        |

### Key CMS Lab Columns

| Column                          | Meaning      |
| ------------------------------- | ------------ |
| `lab_entries_parameter_code`    | LOINC        |
| `lab_entries_parameter_value`   | Result value |
| `lab_entries_parameter_date`    | Result date  |
| `lab_entries_parameter_chartid` | Patient      |

🚨 **All CMS lab logic uses `lab_entries_parameter` only.**

---

## 6️⃣ Vitals / Clinical Values

**UI Tab:** Vitals
**Purpose:** Clinical measurements (e.g., blood pressure).

| UI Tab | Process            | Table                       | Used For             |
| ------ | ------------------ | --------------------------- | -------------------- |
| Vitals | BP, height, weight | `patient_clinical_elements` | CMS165 (BP < 140/90) |

---

## 7️⃣ Measure Results (🚨 FINAL OUTPUT – DERIVED)

**UI Tab:** Measure Report
**Purpose:** Final patient-level measure evaluation.

| UI Tab             | Table                              | Key Columns                                                                         |
| ------------------ | ---------------------------------- | ----------------------------------------------------------------------------------- |
| **Measure Report** | `quality_measures_patient_entries` | `ipp`, `denominator`, `numerator`, `denominator_exclusion`, `denominator_exception` |

🚨 **Important**

* This table is **derived output**
* ❌ Never use it as source data
* ✅ Use it for reporting & validation

---

## 8️⃣ Provider & Measure Configuration (MANDATORY)

Measures only run for **configured providers**.

| Purpose                   | Table                               | Key Columns                                   |
| ------------------------- | ----------------------------------- | --------------------------------------------- |
| Provider MIPS eligibility | `macra_provider_configuration`      | `provider_id`, `reporting_year`               |
| Measure mapping           | `quality_measures_provider_mapping` | `provider_id`, `measure_id`, `reporting_year` |

📌 Missing configuration = no measure results.

---



