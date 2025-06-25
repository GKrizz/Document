# 🩺 CMS138v13: Tobacco Use – Screening and Cessation Intervention

## 🎯 **What This Measure Tracks**

Percentage of patients **aged 12 years and older** who:

1. Were **screened** for tobacco use at least once **during the measurement period**, **AND**
2. If identified as a **tobacco user**, received a **cessation intervention** (counseling and/or pharmacotherapy) **either during** the measurement period **or in the 6 months prior**.

---

## 🧩 Measure Population Logic

### ✅ Initial Population (IPP)

To be included:

* **Age ≥ 12** at the start of the measurement period
* **AND** (At least **2 qualifying visits** OR **1 preventive visit**) during the measurement period

> **Visit Types Accepted**:

* Office Visits
* Preventive Visits
* Virtual / Telehealth
* Behavioral / Rehab Therapies
* Psych / Counseling
* Nutrition
* Other E\&M-type encounters

---

### ✅ Denominator

Depends on the population criteria:

#### 📌 **Population Criteria 1 (Screening Check)**

* Same as IPP (no changes)

#### 📌 **Population Criteria 2 (Cessation Intervention Check)**

* IPP patients whose **most recent tobacco screening indicates they are users**

#### 📌 **Population Criteria 3 (Combo Check)**

* Same as IPP (no filtering) - Used to assess full compliance with both screening and intervention.

  * *Used for final MIPS dashboard reporting — both screening and intervention satisfied*

---

### ❌ Denominator Exclusions

* Patient has **hospice services** (active, ordered, diagnosed, or discharged to hospice)

---

## 📈 Numerator Conditions

### ✅ Numerator 1 — **Screening Performed**

**Met if:**

* A tobacco use screening was performed **during the measurement period**,

  * **AND** result was either:
    * “Tobacco User”
    * “Tobacco Non User”
      
> ⚠️ **"Unknown" or missing results do NOT meet criteria**

---

### ✅ Numerator 2 — **Cessation Intervention**

(Applies to tobacco users only)

**Met if:**
* If patient was screened and identified as a **tobacco user**, then ONE of the following must be present:

  * **Tobacco cessation counseling**
  * **Pharmacotherapy ordered**
  * **Pharmacotherapy active** (within the **6 months prior to or during** the measurement period)

---

### ✅ Numerator 3 — **Screening + Correct Action**

**Met if:**
* If non-user → just the screening is enough
* If user → screening **+** one of the interventions above

  * Counseling, prescription, or active medication

---

## 📁 Official Documentation & Technical Resources

* 🔗 [CMS138v13 2025 eCQM Specification Page](https://static.glaceemr.com/ECQM/2025/CMS138v13.html)
* 🔍 [Get eCQM Info by ID (CMS138) – API Endpoint](https://datagateway.glaceemr.com/DataGatewayMediSpan/eCQMServices/getECQMInfoById?ids=226&reportingYear=2025)
* 📄 [CMS138v6 Sample PDF Help Document (Prior Version)](https://static.glaceemr.com/MIPS/CQMDocuments/CMS138v6.pdf) — *Still useful for conceptual overview despite version changes*

---
