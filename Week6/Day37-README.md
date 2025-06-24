# 📑 MIPS Spec: CMS139v13 – *Falls: Screening for Future Fall Risk*

## 📅 Date: June 24, 2025 (Tuesday)

## 📍 Purpose

To understand the logic behind the MIPS quality measure **CMS139v13** and practically move a test patient from **NOT MET** to **MET** status using the GlaceEMR system.

---

## ✅ Objective

Learn and test how a patient satisfies:

* Initial Population (IPP)
* Denominator
* Numerator
* MET / NOT MET outcome in the **Falls: Screening for Future Fall Risk (CMS139v13)** measure.

---

## ✅ Step-by-Step Process

### 🔐 Step 1: Login to GlaceEMR

**URL:** [https://sso.glaceemr.com/](https://sso.glaceemr.com/)

\[image]

---

### 🔍 Step 2: Search & Select CAIMA

* Search for `caima`
* 👤Click on **Anandh**
  
![Screenshot from 2025-06-24 15-25-05](https://github.com/user-attachments/assets/73e712e2-df45-40ae-b19a-4b4202c57956)


---

### 📊 Step 3: Navigate to MIPS Dashboard

* From the NavBar, go to:
  **Report → MIPS Performance Report**

![Screenshot from 2025-06-24 15-27-17](https://github.com/user-attachments/assets/2b6d4f5c-6064-4de7-8fc5-7c52ca1fc61f)


---

### 📈 Step 4: Now you're at the Dashboard

![Screenshot from 2025-06-24 15-28-12](https://github.com/user-attachments/assets/99dfbd3f-b35f-43ad-a7ac-27492aa242a3)

* Click on the **Quality Measures** tab
* Change to the **specific doctor's name** from the dropdown

![Screenshot from 2025-06-24 15-28-46](https://github.com/user-attachments/assets/9eda44dc-1247-452e-bee1-38e126a9ab8b)

---

### 📌 Step 5: Click IPP count under:

* Locate **Falls: Screening for Future Fall Risk (CMS139v13) ! High Priority**
* Click the **IPP Count** (Initial Population)

![Screenshot from 2025-06-24 15-29-53](https://github.com/user-attachments/assets/f24482dc-6665-4b15-8392-9fead1975596)

---

### 📘 Step 6: Access Help Document

* Inside the detailed page, click the **Help** icon or button for guidance on this measure.

![Screenshot from 2025-06-24 15-30-53](https://github.com/user-attachments/assets/b50b8514-a3cf-4e2f-bd3a-6ef089959229)

---

## 🧪 Test Patient Scenario (Hands-On)

### 👤 Step 1: Select a Test Patient

* From the patient list, choose a test patient who is aged **65 or older**
* Check if the CPT code (e.g. 99203) for **Annual Wellness Visit** is present

![Screenshot from 2025-06-24 15-32-24](https://github.com/user-attachments/assets/70b96199-9a01-4c02-a5d7-4cbd1bf39e3a)

---

### 🧾 Step 2: Open MIPS Flowsheet

* Click **MIPS Flowsheet**
  ![Screenshot from 2025-06-24 15-55-45](https://github.com/user-attachments/assets/3a7eff66-419c-4114-835c-07c222a31dbc)

* Observe the row **"Falls: Screening for Future Fall Risk"**
* Status should show **❌ NOT MET**

![Screenshot from 2025-06-24 15-56-33](https://github.com/user-attachments/assets/ddb31ea6-f273-465c-bde7-1516a6f8b1f7)

---

## ✅ Goal: Make it **MET**

### 📋 CMS139v13 - Measure Logic

**1. Initial Population & Denominator Criteria:**

* Age: **≥ 65** during the measurement period
* CPT:

  * Annual Wellness Visit – **99203**

![Screenshot from 2025-06-24 16-09-43](https://github.com/user-attachments/assets/e37b17ea-4d1e-4da9-84cc-4364b4eaa507)

---
**2. Numerator Criteria (MET):**

* Under the **Screening tab**, look for **Fall Risk Screening**
* If not visible:
![Screenshot from 2025-06-24 16-19-23](https://github.com/user-attachments/assets/0648b5f2-2152-43e4-afaa-5cfce87b48fa)

  * Use **Search**
  * Type and select: **Fall Risk Screening**
![Screenshot from 2025-06-24 16-19-37](https://github.com/user-attachments/assets/68b145ce-57b6-4b5b-886a-5dd4194e8785)


* Select the **Completed** checkbox
* Click **Save and Close**

![Screenshot from 2025-06-24 16-20-38](https://github.com/user-attachments/assets/1a72e7f4-21d3-4d7d-85b0-4672e617c08d)

---

## 🔄 Final Step: Go back and check

* Go back to **MIPS Flowsheet or in macra tab**
* Confirm that **Falls: Screening for Future Fall Risk** now shows **MET**

![Screenshot from 2025-06-24 16-22-13](https://github.com/user-attachments/assets/075bb75d-ff7c-400f-8845-3f8d58188225)

---

## 📚 Measure Logic Summary

### ✔ Initial Population (IPP)

* **Age ≥ 65** during the measurement period
* **Visit Code (CPT)**: e.g., **99203**

### ✔ Denominator

* Same as IPP, no exclusions unless specified

### ✔ Numerator (MET)

* Documented **Fall Risk Screening** as **Completed** under Screening tab

### ❌ NOT MET

* Screening not documented or not marked as **Completed**

### 📌 Summary

| Status      | Criteria                                                                                            |
| ----------- | --------------------------------------------------------------------------------------------------- |
| **NOT MET** | Age ≥ 65 + Visit present, but **Fall Risk Screening** not documented or not marked as **Completed** |
| **MET**     | Age ≥ 65 + CPT 99203 + **Fall Risk Screening** marked as **Completed** in Screening tab             |






