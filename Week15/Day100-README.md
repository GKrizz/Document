# **Daily Progress Report**

### Day 100 — August 26, 2025 (Tuesday)

---

### ✅ **CMS125v13 Measure Test – Passed**

* **Measure:** Breast Cancer Screening
* **CMS ID:** CMS125v13
* **Measurement Period:** 01/01/2025 – 12/31/2025
* **Provider:** Steve Collier
* **NPI:** 1390894560
* **TIN:** 273979098
* **CCN:** 616687

---

## **Project:** Cypress QRDA-III Testing with Glace EHR System


---

## 1. **Summary of Work Completed**

* Worked on backend Java classes handling QRDA import and export:

  * `QRDAController.java` — focused on `/handleQRDAImport` method.
  * `QRDAServiceImpl.java`
  * `ExportQRDAServiceImpl.java`
  * `MeasureCalcServiceImpl.java`

* Extracted and analyzed **investigationQDM** data for patient Ida (`patientID: 3275873`), identifying key lab entries with procedure codes indicating unilateral mastectomy (right and left).

* Validated API Hub responses showing measure status updates and appropriate denominator exclusions for CMS125v13.

* Accessed **MIPS Performance Report** UI to review and compare provider data (Steve Collier) against Cypress expected results.

* Investigated deviations between Glace and Cypress reports for key patients Ida and Theresa using the QDM Validation API.

* Ran SQL queries to verify patient lab data and confirm patient-provider mappings in the PostgreSQL database.

* Prepared for QRDA submission by downloading and validating QRDA-III and QRDA-I XML files with Cypress results.

---

## 2. **Detailed Findings**

### 2.1 Backend Code Updates

* Focus on improving QRDA import handling to ensure accurate processing of patient clinical and investigation data.
* Backend validation confirmed that procedure codes for mastectomy are correctly recognized for denominator exclusion.

### 2.2 Patient QDM Data Analysis

* Investigation QDM Data snippet for Ida:

  | Test Detail ID | Code    | Description                 | Status | Performed Date          |
  | -------------- | ------- | --------------------------- | ------ | ----------------------- |
  | 230029         | 0HTT0ZZ | Unilateral Mastectomy Right | 3      | 2024-12-21T15:30:00 UTC |
  | 230030         | 0HTU0ZZ | Unilateral Mastectomy Left  | 3      | 2024-12-21T15:30:00 UTC |

* Clinical QDM data was empty for this patient, confirming no additional clinical measures affecting this evaluation.

### 2.3 MIPS Performance Report Review

* Verified Glace MIPS Performance Report UI matched Cypress expected results for provider Steve Collier.
* No significant deviations found for most measures; further detailed deviation analysis ongoing.

### 2.4 Deviation Investigation & API Validation

* Used QDM Validation API for patient-specific detailed measure validation.
* Confirmed correct JSON structure and data consistency compared to Cypress QRDA-I XML files.

### 2.5 Database Verification

* Confirmed presence of relevant lab entries in `lab_entries` table.
* Verified patient registrations associated with provider 2428, confirming correct data associations.

---

## 3. **Screenshots**

* **QRDA-III Downloaded File:**
  ![QRDA-III Downloaded](https://github.com/user-attachments/assets/1a67e0f6-b7ff-48cc-90ba-96fd55721c33)

* **QRDA-III Cypress Validation Result:**
  ![QRDA-III Passing](https://github.com/user-attachments/assets/d9f49790-ba18-40ff-835d-b5a77757619a)

* **QRDA-I Cypress Validation Result:**
  ![QRDA-I Passing](https://github.com/user-attachments/assets/06bf1610-fd34-4935-8795-7013a563978e)

---





### **Validation Status**

| Validation Type | Status   | Errors | Warnings | Execution Time           |
| --------------- | -------- | ------ | -------- | ------------------------ |
| **C1**          | ✅ Passed | --     | --       | August 26, 2025 – 1:28pm |
| **C2 + C3**     | ✅ Passed | --     | --       | August 26, 2025 – 9:45am |

---
