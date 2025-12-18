# ✅ CMS156 – QUICK DECISION FLOW (Per Patient)

## 1️⃣ Age + Visit Check

* ✔ **Age ≥ 65** on **Dec 31** of the measurement year?
* ✔ Has **≥ 1 qualifying encounter** during the measurement period?

**If NO →** ❌ **N/A (Not in Initial Population)**
**If YES →** Continue ⬇️

---

## 2️⃣ Hospice / Palliative Check

* ❓ Any **hospice OR palliative care** during **any part** of the year?

**If YES →** ❌ **EXCLUDED (N/A)**
**If NO →** Continue ⬇️

---

## 3️⃣ Did THIS Provider Order High-Risk Meds?

* ❓ Did the **reporting provider** order **any high-risk medication** during the measurement period?

📌 Orders from **other providers do NOT count**

**If NO →** ✅ **Denominator only (Not in numerator)**
**If YES →** Continue ⬇️

---

## 4️⃣ At Least TWO Orders from SAME Drug Class?

* ❓ Are there **≥ 2 orders**
* ✔ From the **SAME high-risk drug class**
* ✔ Ordered on **DIFFERENT DAYS**
* ✔ During the measurement period?

**If NO →** ✅ **Not in numerator**
**If YES →** Continue ⬇️

---

## 5️⃣ Check NUMERATOR 1 (General High-Risk Use)

Patient meets **Numerator 1** if **ANY ONE** is true:

✔ Two high-risk meds from same class on different days
✔ OR summed **days supply > 90 days**
✔ OR **average daily dose exceeds threshold**
  • Digoxin > **0.125 mg/day**
  • Doxepin > **6 mg/day**

➡ **YES → Numerator 1 = TRUE**

---

## 6️⃣ Check NUMERATOR 2 (Diagnosis-Sensitive Drugs)

Only applies to **Antipsychotics** and **Benzodiazepines**

### 🔴 Antipsychotics

* ✔ ≥ 2 antipsychotic orders on different days
* ❌ NO diagnosis of:

  * Schizophrenia
  * Bipolar disorder
* Diagnosis window:
  📅 **Jan 1 of PRIOR year → Index Prescription Start Date**

➡ If true → **Numerator 2 = TRUE**

---

### 🔴 Benzodiazepines

* ✔ ≥ 2 benzodiazepine orders on different days
* ❌ NO diagnosis of:

  * Seizure disorder
  * REM sleep behavior disorder
  * Benzodiazepine withdrawal
  * Alcohol withdrawal
  * Severe generalized anxiety disorder
* Diagnosis window:
  📅 **Jan 1 of PRIOR year → Index Prescription Start Date**

➡ If true → **Numerator 2 = TRUE**

---

## 7️⃣ Final Measure Results (What Gets Counted)

### 🔹 Rate 1

* **Numerator 1 only**
* General high-risk medication use

---

### 🔹 Rate 2

* **Numerator 2 only**
* Antipsychotics / Benzodiazepines **WITHOUT appropriate diagnoses**

---

### 🔹 TOTAL RATE (Reported Score)

* **Numerator 1 OR Numerator 2**
* **Deduplicated per patient**

📌 Lower score = **better quality**

---

# 🔍 ONE-LINE LOGIC (FAST CHECK)

```
Age ≥65 + Visit
   ↓
No Hospice / Palliative
   ↓
Provider ordered ≥2 high-risk meds (same class, different days)?
   ↓
YES → Numerator 1
   ↓
If Antipsychotic / Benzo:
   Check allowed diagnoses
   ↓
No diagnosis → Numerator 2
```

---

# 🟡 Why a Patient Shows N/A vs Not Counted

### 🟡 **N/A**

* Age < 65
* No qualifying encounter
* Hospice / palliative care

### ⚪ **In Denominator but NOT in Numerator**

* Only one high-risk med ordered
* Meds from different classes
* Orders by another provider only
* Appropriate diagnosis present (for Rate 2 drugs)

---

# 🧠 VERY IMPORTANT RULES (Easy to Miss)

✔ Orders must be on **different days**
✔ Same-day duplicates ❌ don’t count
✔ Refills can count if logic treats them as separate orders
✔ Diagnosis lookback = **prior year → IPSD**
✔ This is a **patient-based measure** (not visit-based)

---

# 🏁 ONE-LINE SUMMARY

> CMS156 flags patients ≥65 who were ordered **two or more high-risk medications from the same class**, especially **antipsychotics or benzodiazepines without appropriate diagnoses**. Lower is better.

---
