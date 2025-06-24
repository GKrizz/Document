# 📑 MIPS Spec: CMS139v13 – *Falls: Screening for Future Fall Risk*

---

## 📅 Date: June 24, 2025 (Tuesday)

## 🎯 Purpose

To understand the logic behind the MIPS quality measure **CMS139v13** and practically move a test patient from **❌ NOT MET** to ✅ **MET** status using the **GlaceEMR** system.

---

## ✅ Objective

Learn and validate how a patient qualifies for:

* **Initial Population (IPP)**
* **Denominator**
* **Numerator**
* Final status: **MET** / **NOT MET**

---

## 🧭 Step-by-Step Process

---

### 🔐 Step 1: Login to GlaceEMR

**URL:** [https://sso.glaceemr.com/](https://sso.glaceemr.com/)

---

### 🔍 Step 2: Search & Select CAIMA

* Search for `caima`
* Click on **Anandh**

> ![Search CAIMA](https://github.com/user-attachments/assets/73e712e2-df45-40ae-b19a-4b4202c57956)

---

### 📊 Step 3: Navigate to MIPS Dashboard

* Navigate via: **Report → MIPS Performance Report**

> ![MIPS Report Navigation](https://github.com/user-attachments/assets/2b6d4f5c-6064-4de7-8fc5-7c52ca1fc61f)

---

### 📈 Step 4: Access Dashboard and Quality Measures

> ![Dashboard](https://github.com/user-attachments/assets/99dfbd3f-b35f-43ad-a7ac-27492aa242a3)

* Click the **Quality Measures** tab
* Change the **Doctor's Name** from the dropdown

> ![Change Doctor](https://github.com/user-attachments/assets/9eda44dc-1247-452e-bee1-38e126a9ab8b)

---

### 📌 Step 5: Click on IPP Count

* Locate: **Falls: Screening for Future Fall Risk (CMS139v13) ! High Priority**
* Click the **IPP Count**

> ![IPP Click](https://github.com/user-attachments/assets/f24482dc-6665-4b15-8392-9fead1975596)

---

### 📘 Step 6: Open Help Document

* Inside this section, click the **Help** icon to read measure guidelines.

> ![Help Doc](https://github.com/user-attachments/assets/b50b8514-a3cf-4e2f-bd3a-6ef089959229)

---

## 🧪 Test Patient Scenario

---

### 👤 Step 1: Select a Test Patient

* Choose a patient **aged ≥ 65**
* Ensure **CPT 99203 (Annual Wellness Visit)** is recorded

> ![Patient Record](https://github.com/user-attachments/assets/70b96199-9a01-4c02-a5d7-4cbd1bf39e3a)

---

### 🧾 Step 2: Open MIPS Flowsheet

* Click **MIPS Flowsheet**

> ![Flowsheet](https://github.com/user-attachments/assets/3a7eff66-419c-4114-835c-07c222a31dbc)

* Check for: **Falls: Screening for Future Fall Risk** → Status should show: **❌ NOT MET**

> ![Not Met Status](https://github.com/user-attachments/assets/ddb31ea6-f273-465c-bde7-1516a6f8b1f7)

---

## 🧠 Goal: Move Status to ✅ MET

---

### 📋 CMS139v13 Measure Logic

#### 1️⃣ Initial Population & Denominator Criteria

* **Age ≥ 65** during measurement period
* **Visit CPT**: 99203 (Annual Wellness Visit)

> ![IPP Criteria](https://github.com/user-attachments/assets/e37b17ea-4d1e-4da9-84cc-4364b4eaa507)

---

#### 2️⃣ Numerator Criteria – To Achieve **MET**

* Go to the **Screening** tab
* Look for **Fall Risk Screening**

  * If not found:

> ![Search](https://github.com/user-attachments/assets/0648b5f2-2152-43e4-afaa-5cfce87b48fa)

  * Use **Search**
  * Type and select: **Fall Risk Screening**
    
> ![Select Option](https://github.com/user-attachments/assets/68b145ce-57b6-4b5b-886a-5dd4194e8785)

* Select the **Completed** checkbox
* Click **Save and Close**

> ![Completed and Save](https://github.com/user-attachments/assets/1a72e7f4-21d3-4d7d-85b0-4672e617c08d)

---

## 🔄 Final Step: Confirm the Status

* Return to **MIPS Flowsheet** or **MACRA Tab**
* Confirm status is now ✅ **MET**

> ![Final MET](https://github.com/user-attachments/assets/075bb75d-ff7c-400f-8845-3f8d58188225)

---

## 📚 Measure Logic Summary

| **Component**                | **Criteria**                                                           |
| ---------------------------- | ---------------------------------------------------------------------- |
| **Initial Population (IPP)** | Patient aged **≥ 65** with **CPT 99203** visit during measurement year |
| **Denominator**              | Same as IPP                                                            |
| **Numerator (MET)**          | Fall Risk Screening is **documented and marked Completed**             |
| **NOT MET**                  | Screening **not documented** or **not marked as Completed**            |

---
