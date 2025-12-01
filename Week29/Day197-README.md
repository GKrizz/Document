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
