# 📘 README

## MIPS Measure Documentation – CMS122v13 & CMS138v13

**Day 218 – December 22, 2025 (Monday)**

---

## 1️⃣ Measure Overview

### **CMS122v13**

**Measure Name:**
**Diabetes: Glycemic Status Assessment Greater Than 9%**

**Measure Type:**
🔁 **Inverse Measure**

* **Good control (< 9%) → NOT MET**
* **Poor control (≥ 9%) → MET**
* **HbA1c not done → MET**

---

## 2️⃣ CMS122v13 – Measure Logic Summary

### 🔹 HbA1c Result Interpretation

| HbA1c Status   | Measure Result |
| -------------- | -------------- |
| HbA1c < 9%     | ❌ NOT MET      |
| HbA1c ≥ 9%     | ✅ MET          |
| HbA1c NOT DONE | ✅ MET          |

**Interpretation:**

* **Good control = NOT MET**
* **Poor or missing control = MET**

---

## 3️⃣ CMS122v13 – UI Tabs & Database Tables Mapping

| Measure Requirement  | UI Tab               | Database Table               |
| -------------------- | -------------------- | ---------------------------- |
| Eligible encounter   | Encounter            | `encounter`                  |
| CPT support          | Superbill            | `service_detail`             |
| Diabetes diagnosis   | Assessment           | `service_detail` (dx fields) |
| HbA1c test performed | Investigation / Labs | ⭐ `lab_entries_parameter`    |
| Provider attribution | Encounter            | `encounter`, `emp_profile`   |

---

## 4️⃣ CMS122v13 – Functional Breakdown

### 1. **Eligible Encounter (Initial Population & Denominator)**

* **UI Tab:** Encounter / Superbill
* **Table:** `encounter`, `service_detail`
* **Purpose:**

  * Confirms completed visit
  * Links provider and visit date

---

### 2. **CPT Codes**

* **UI Tab:** Superbill
* **Table:** `service_detail`
* **Purpose:**

  * Confirms eligible visit type
  * Supports denominator qualification

⚠️ *CPT codes alone do NOT decide MET / NOT MET.*

---

### 3. **Diabetes Diagnosis**

* **UI Tab:** Assessment
* **Table:** `service_detail` (DX fields)
* **Purpose:**

  * Identifies diabetic patients

---

### 4. **HbA1c Test (Critical Component)**

* **UI Tab:** Investigation / Labs
* **Table:** ⭐ `lab_entries_parameter`
* **Purpose:**

  * Determines if HbA1c was performed
  * Reads numeric HbA1c value

✅ **This table drives MET / NOT MET logic**

---

## 5️⃣ CMS138v13 – Measure Overview

### **Measure Name**

**Preventive Care and Screening: Tobacco Use – Screening and Cessation Intervention**

---

## 6️⃣ CMS138v13 – Screening Workflow

### 1. **Eligible Encounter**

* **UI Tab:** Encounter / Superbill
* **Tables:** `encounter`, `service_detail`

---

### 2. **Tobacco Use Screening**

* **UI Tab:**
  **History → Social History**

  * Smoking

    * Smoker
    * Non-smoker

* **Tables:**

  * `patient_clinical_findings`
  * `risk_assessment` (when applicable)

**Stored Data Includes:**

* Tobacco use question
* Smoker / Non-smoker responses

---

## 7️⃣ CMS138v13 – Numerator Logic (MET Conditions)

### ✅ **Numerator Option 1**

**Non-Smoker documented**

* **UI Tab:** History
* **Table:** `patient_clinical_findings`
* **Result:** ✔ MET

---

### ✅ **Numerator Option 2**

**Smoker + Counseling performed**

#### UI Tabs

* History → Smoker
* MUP / Plan Instructions → Tobacco Abuse Counseling

#### Tables

* `patient_clinical_findings` → Smoking status
* `risk_assessment` → Counseling intervention

✔ **MET only if BOTH exist**

---

## 8️⃣ CMS138v13 – UI Tabs & Database Tables Mapping

| Measure Requirement        | UI Tab                   | Database Table                |
| -------------------------- | ------------------------ | ----------------------------- |
| Eligible encounter         | Encounter / Superbill    | `encounter`, `service_detail` |
| Tobacco screening          | History → Social History | `patient_clinical_findings`   |
| Smoker / Non-smoker status | History                  | `patient_clinical_findings`   |
| Tobacco counseling         | Plan Instructions (MUP)  | `risk_assessment`             |
| Provider attribution       | Encounter                | `encounter`, `emp_profile`    |

---
