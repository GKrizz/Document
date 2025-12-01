# **Nelson MIPS Smoking Status (N/A Issue) & Patient Analysis**

**Date:** December 1, 2025
**Prepared by:** *Gobala Krishnan*
**Related To:** MIPS Measure – CMS138v13
**Provider:** *Edward Nelson MD*
**Reported By:** *Indira Reddy*
**Escalation From:** *Raji*, *Paddy*

---

# **1. Overview of the Issue**

Indira Reddy reported that the **Smoking Status** for several of Dr. Nelson’s patients shows **“N/A”** inside Glenwood, even though the clinic documented them as *Non-smoker* or *Smoker*.

She requested that the correct **screens and follow-ups** be uploaded.

Later, a second email reported additional **MIPS errors**, including:

* Depression MIPS screen value set loading issue
* Smoking not calculated even after entering screening
* Missing “Not Met” list

This README summarizes the issue, the MIPS measure requirements, the investigation steps, and the status of each reported patient.

---

# **2. Difference Between the Two Emails**

### **Mail 1 – Nelson MIPs – Smoking (only Smoking issue)**

* Several patients show **N/A** for Smoking Measure.
* Smoking is recorded in chart, but **MIPS measure not recognizing it**.
* Expected fix: identify why and correct documentation.

### **Mail 2 – MIPs errors for Dr. Nelson**

Issues include:

1. **Depression MIPS screen error:** “Failed to get Valueset Oids codelist.”
2. **Smoking documented but still showing Not Met**
3. **Not Met list not sent for Dr. Reddy & Dr. Nelson**

---

# **3. Understanding CMS138 – Tobacco Use: Screening & Cessation Intervention**

The measure includes **3 populations (rates):**

### **Population 1 – Screening Performed**

Patient screened for tobacco use at least once.

### **Population 2 – Users who received intervention**

Only applies if the patient is a **Tobacco User**.

### **Population 3 – Screening + Intervention (combined)**

* If Non-user → screening alone satisfies measure
* If User → screening + intervention needed

---

## **3.1 Initial Population Requirements**

Patient must be:

* **Age ≥ 12 years**, and
* Have **≥2 qualifying visits** OR **1 preventive visit** in the measurement period.

### **Qualifying Visits Examples**

* Office Visits → 99212–99215
* Virtual, Telephone encounters
* Psychotherapy, PT/OT, Ophthalmology, etc.

### **Preventive Visits Examples**

* AWV (G0402 / G0438 / G0439)
* Preventive counseling
* Nutrition services

---

## **3.2 Data That Counts as Screening (Numerator 1)**

Screening must be documented as:

**Assessment, Performed: Tobacco Use Screening**
with result coded as:

* **Tobacco Non User**, or
* **Tobacco User**

⚠ Entries in **Social History** or non-coded flowsheets do **NOT** count.

---

## **3.3 Data Needed for Intervention (Numerator 2)**

Only if patient is tobacco user:

* Tobacco cessation counseling
* Pharmacotherapy order
* Active pharmacotherapy within measurement period or 6 months before

---

# **4. Root Cause (Summary of Findings)**

When checking patient encounters, the **first provider** in the list was often *not* Dr. Nelson, so MIPS for that provider shows **N/A**.

When switching to **Edward Nelson MD**, MIPS loads correctly and shows:

* **Not Met** for most patients
* **Met** for one patient

Most **Not Met** cases were because:

* Smoking status was only documented in **History XML** (not MIPS-required field), OR
* Smoking status was **not documented at all**

---

# **5. Reported Patients – Final Audit Status**

Below is the final assessment for all reported patients under **Dr. Nelson**:

### **1. 6531982 – Sandra Boone**

* Clinic says: Non-smoker
* Status: **Not Met**
* Reason: Smoking documented only in **history XML** (11/12/2025)

---

### **2. 6532289 – Terri Brewer**

* Clinic says: Non-smoker
* Status: **Not Met**
* Reason: **Smoking status not documented**

---

### **3. 6537272 – Robin Cox**

* Clinic says: Non-smoker
* Status: **MET**
* Reason: Valid tobacco screening documented

---

### **4. 6536247 – Dwayne Hansbro**

* Clinic says: Non-smoker
* Status: **Not Met**
* Reason: Smoking documented only in **history XML** (11/03/2025)

---

### **5. 6537264 – Shane Hasty**

* Clinic says: Non-smoker
* Status: **Not Met**
* Reason: Smoking documented only in **history XML** (11/17/2025)

---

### **6. 6537152 – Kenyatta Nevins**

* Clinic says: Non-smoker
* Status: **Not Met**
* Reason: **Smoking status not documented**

---

### **7. 6537297 – Willie Coleman**

* Clinic says: Smoker
* Status: **Not Met**
* Reason: **Smoking status not documented**

---

# **6. Conclusion**

* **Only 1 patient (Cox, Robin)** meets the MIPS measure.
* All remaining patients are **Not Met** because smoking status was either:

  * Not documented, OR
  * Only stored in history XML (not recognized by MIPS)


---
## Population Criteria 1 — (Rate / Population 1 = *Screened for tobacco at least once*)

**Purpose:** identifies patients who should be counted for the *screening* rate.

* **Initial Population**

  * Rule: Patient **age ≥ 12 at start of measurement period**
    **AND** (has **≥ 2 qualifying visits** during the measurement period **OR** has **at least one preventive visit** during the measurement period).
  * Quick check: `age >= 12` and `(count(visits in period) >=2 OR exists(preventive_visit))`.

* **Denominator**

  * Rule: **Equals Initial Population.**
  * So any patient meeting Initial Population is in the denominator.

* **Denominator Exclusions**

  * Rule: **Exclude** if patient had **hospice services** during the measurement period (per the hospice definitions).
  * Check: hospice encounter / hospice diagnosis / hospice assessment / hospice order overlapping measurement period → exclude.

* **Numerator**

  * Rule: **Most recent tobacco use screening during the measurement period** must have a **result** of either **Tobacco User** or **Tobacco Non User** (i.e., not null).
  * Practical: patient must have at least one tobacco screening in period and the **most recent** screening must have a coded result (user/non-user).
  * Quick check: `exists(tobacco_screening during measurement period) AND most_recent.result in {Tobacco User, Tobacco Non User}`

* **Numerator Exclusions / Denominator Exceptions / Stratification**

  * None for Pop 1.

**Common reasons a patient in Pop 1 shows N/A:** no screening recorded during period, screening recorded but result stored as “unknown” or free-text not mapped to valueset, or patient was excluded by hospice.

---

## Population Criteria 2 — (Rate / Population 2 = *Tobacco users who received cessation intervention*)

**Purpose:** assesses whether **patients identified as tobacco users** received an intervention.

* **Initial Population**

  * Same rule as Pop 1: **age ≥ 12** and **≥2 qualifying visits** or **1+ preventive visit**.

* **Denominator**

  * Rule: **Initial Population** **AND** **Most Recent Tobacco Use Screening = Tobacco User** (i.e., the patient’s most recent screening in the measurement period identifies them as a tobacco user).
  * So Pop 2 denominator = the subset of Pop 1 who are *current tobacco users*.

* **Denominator Exclusions**

  * Same hospice exclusions as Pop 1.

* **Numerator**

  * Rule: **Exists** any of:

    * **Tobacco cessation counseling given** (during measurement period or up to 6 months prior),
    * **Tobacco cessation pharmacotherapy ordered** (during measurement period or up to 6 months prior),
    * **Active pharmacotherapy for tobacco cessation** overlapping day-of interval [start of measurement period − 6 months, end of measurement period].
  * Practical check: Look for coded counseling interventions, ICD Z71.6, medication orders for cessation drugs, or active meds recorded within the 6-month lookback window.

* **Numerator Exclusions / Denominator Exceptions / Stratification**

  * None.

**Common reasons a patient in Pop 2 shows N/A (or fails numerator):** patient correctly screened as “Tobacco User” but no counseling or pharmacotherapy documented in the measurement period or the preceding 6 months (or documents exist but not coded to the expected valuesets).

---

## Population Criteria 3 — (Rate / Population 3 = *Combined screening + intervention where appropriate*)

**Purpose:** the comprehensive rate — screens everyone and, if a user, also requires intervention.

* **Initial Population**

  * Same as above: **age ≥ 12** and **≥2 qualifying visits** or **1+ preventive visit**.

* **Denominator**

  * **Equals Initial Population** (same as Pop 1).

* **Denominator Exclusions**

  * Same hospice exclusions.

* **Numerator**

  * Rule: EITHER:

    * **Most Recent Tobacco Use Screening = Tobacco Non User** (i.e., screened and non-user) — **OR**
    * **Most Recent Tobacco Use Screening = Tobacco User** **AND** (exists counseling **or** pharmacotherapy within measurement period or 6 months prior).
  * In words: counted as numerator if screened and non-user, or if user, received intervention in appropriate window.

* **Numerator Exclusions / Denominator Exceptions / Stratification**

  * None.

**Common reasons a patient in Pop 3 shows N/A:** no tobacco screening recorded; or screening exists but result unknown; or user documented but no intervention documented within the allowed lookback window.

---

## Practical checklist to debug a single patient (why they show **N/A**)

1. **Age & Visit rule**

   * Is patient **≥12** at start of measurement period?
   * Does the patient have **≥2 qualifying visits** in period OR **≥1 preventive visit**?
   * If not → patient **not in Initial Pop** → they are outside measure (N/A).

2. **Hospice exclusion**

   * Any **hospice encounter/diagnosis/assessment/order** overlapping the period? → excluded.

3. **Tobacco screening existence & mapping**

   * Is there **any tobacco screening** during the measurement period?
   * Is the **most recent screening result** coded to the valueset **(Tobacco User / Tobacco Non User)**?
   * If result = **Unknown** or free-text not mapped → treated as **not meeting numerator**.

4. **If most recent = Tobacco User** (so they belong to Pop 2 denom)

   * Check for **Tobacco Cessation Counseling** (intervention performed) during [start − 6 months, end] or **Tobacco Cessation Pharmacotherapy Ordered / Active** in same window.
   * Confirm coding matches the valuesets (counseling code, Z71.6, prescription codes mapped to Tobacco Use Cessation Pharmacotherapy valueset).

5. **Date windows**

   * Confirm the **dates**: counseling/pharmacy must be within **measurement period OR up to 6 months prior** (i.e., normalize intervals correctly).
   * The screening must be **during** the measurement period (and the *most recent* screening in the period is used).

6. **Data normalization / last() logic**

   * The measure uses **Most Recent** screening in the period. If multiple screenings exist, ensure code picks the latest one. An earlier screening outside period doesn’t qualify.

7. **Team-based entry allowance**

   * Intervention may be performed by another provider; make sure counseling/orders by other team members are included in data feed.

---

## One-line summary

* **Pop 1** = everyone eligible (≥12 + visits) → **did they have a tobacco screening in the measurement period with a result (user/non-user)?**
* **Pop 2** = subset of Pop1 whose **most recent screening = Tobacco User** → **did they receive counseling or pharmacotherapy during measurement period or in the prior 6 months?**
* **Pop 3** = everyone eligible → **if non-user then screened = ok; if user then must also have intervention in allowed window**.

---

# Concrete examples

* Patient A: age 45; 2 office visits; screened on 2025-06-10 as **Non-User** → Pop1 met, Pop3 met.
* Patient B: age 60; 1 preventive visit; screened as **User** on 2025-03-01; counseling documented on 2024-12-15 (within 6 months prior) → Pop1 met, Pop2 met, Pop3 met.
* Patient C: age 30; 3 visits; no screening recorded in 2025 → Pop1 not met (denom yes but numerator no), Pop3 not met.
* Patient D: age 70; screened as **User**; no counseling or meds recorded in 6 months prior or during period → Pop2 not met (Not Met), Pop3 not met.

--- 

# Debug / investigation checklist (step-by-step for each reported patient)

1. Verify **age** and **visit counts** (which visits counted as qualifying? check visit mapping).
2. Verify **hospice** status.
3. Find **most recent Tobacco Use Screening** during measurement period: confirm date, where it’s stored, and that the **result** is coded as `Tobacco User` or `Tobacco Non User`.
4. If screening exists but system shows **N/A**, check whether the screening used the correct **LOINC/SNOMED** code or was entered as free text.
5. If user and shows **Not Met**, check for **Tobacco Cessation Counseling** code or **pharmacotherapy order/active med** in measurement period OR 6 months prior.
6. If multiple patients show the same failure pattern, check **valueset OID loading** / codelist fetching and mapping in the eCQM engine.
7. Record exact field names and codes you find so Tech Support can fix mappings if needed.
---


# ✅ **FINAL CHECKLIST (Use this for every patient)**

**Step 1 — Age + Visit Requirement**
✔ Age ≥ 12
✔ At least **2 qualifying visits** OR **1 preventive visit** during the measurement period
→ If not satisfied → **Patient not included** (measure shows N/A)

---

**Step 2 — Hospice Status**
✔ If patient was in hospice at any time during period
→ **Exclude from denominator** (measure should not evaluate them)

---

**Step 3 — Tobacco Use Screening**
✔ Find **Most Recent Tobacco Use Screening** **during the measurement period**

* Check where it is documented (Assessment tab, flowsheet, history, etc.)
* Must be in **Tobacco Use Screening** valueset
* Must have a coded result:

  * **Tobacco Non User** OR
  * **Tobacco User**

❗If missing, incorrect, or free text → System shows **N/A**

---

**Step 4 — Code Mapping Verification**
✔ Check whether the screening field uses correct LOINC/SNOMED from valueset
✔ If documented in social history or custom field → It **will not count**
✔ If multiple screens, ensure **latest** is coded correctly
✔ If system still shows N/A → possible **valueset OID mapping failure**

---

**Step 5 — If Patient is a Tobacco User**
✔ Check for Tobacco Cessation Intervention:

* Tobacco cessation counseling (coded)
* OR Tobacco cessation pharmacotherapy
* OR Active medication for cessation

✔ Check dates → valid if during measurement period **OR within 6 months before**

If no intervention →

* **Pop2 = Not Met**
* **Pop3 = Not Met**

---

**Step 6 — Identify System-Level Issues**
✔ If multiple patients show N/A →

* Possible “Failed to get Valueset OIDs” issue
* Codelist may not have loaded
* Mapping from Glace EMR flowsheet to eCQM engine may be broken

Record:

* Field name
* Data element
* Code expected
* Code used

→ Tech Support can fix mapping.

---

# 🔍 **FINAL DECISION FLOW (fast evaluation)**

Use this **flowchart logic** per patient:

### 1️⃣ Age ≥12 + 2 visits (or 1 preventive)?

* No → **Not included** (N/A)
* Yes → Continue

### 2️⃣ Hospice?

* Yes → Excluded
* No → Continue

### 3️⃣ Most Recent Screening (this year)?

* No → Pop1 = No; Pop3 = No
* Yes → Continue

### 4️⃣ Screening Result?

* **Non-user** → Pop1 = Yes; Pop3 = Yes
* **User** → Continue to interventions

### 5️⃣ Intervention for users (counseling or medication)?

* Yes → Pop2 = Yes; Pop3 = Yes
* No → Pop2 = No; Pop3 = No

---

# Quick decision flow you can use for each patient

1. Check age ≥12 and visit requirement (≥2 qualifying visits OR ≥1 preventive visit). If not → **Not included**.
2. Check hospice status during period → if yes → **Excluded**.
3. Look for the **most recent Tobacco Use Screening** during measurement period:

   * If **no screening** → Pop1 numerator = **No**; Pop3 numerator = **No**.
   * If screening result = **Tobacco Non-User** → Pop1 = **Yes**, Pop3 = **Yes** (Pop2 not applicable).
   * If screening result = **Tobacco User** → Pop1 = **Yes**, check interventions:

     * If counseling or pharmacotherapy exists (during period OR 6 months prior) → Pop2 = **Yes**, Pop3 = **Yes**.
     * If no counseling or pharmacotherapy → Pop2 = **No**, Pop3 = **No**.
---
