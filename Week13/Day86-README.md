
## ✅ Summary of Main Differences & Changes Needed

| Category                                         | Current Rule Logic (Old)                                                                               | Updated CMS125v13 (New)                                                                                                                    | Required Action                                                                                                                                                                                                                                                      |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Advanced Illness + Frailty**                   | You correctly check for ≥66, 2 Advanced Illness diagnoses, and follow-up Frailty checks (rules 12–19). | ✔ Yes, logic matches the general intent.                                                                                                   | ✅ Mostly accurate — only minor improvements or code cleanup.                                                                                                                                                                                                         |
| **Frailty Encounter OID**                        | `2.16.840.1.113883.3.464.1003.101.12.1088`                                                             | ✔ Same                                                                                                                                     | ✅ No change needed                                                                                                                                                                                                                                                   |
| **Frailty Symptom OID**                          | Used as `Symptom` category in Rule 19                                                                  | ✔ Matches updated OID                                                                                                                      | ✅ OK                                                                                                                                                                                                                                                                 |
| **Frailty Device (Device or Assessment result)** | Handled in Rules 15 & 16                                                                               | ✔ Correct OID: `2.16.840.1.113883.3.464.1003.118.12.1300`                                                                                  | ✅ OK                                                                                                                                                                                                                                                                 |
| **Medical Equipment Used (LOINC 98181-1)**       | Rule 16 uses: `2.16.840.1.113883.3.225.2001.143`                                                       | ✔ Correct                                                                                                                                  | ✅ OK                                                                                                                                                                                                                                                                 |
| **Assessment Result: Yes**                       | Missing in old rules                                                                                   | ✅ New logic for Hospice (LOINC 45755-6 → result “Yes”) or Housing status (LOINC 71802-3 → result “Lives in nursing home”)                  | ❗ Needs enhancement — see next section                                                                                                                                                                                                                               |
| **Palliative Care**                              | ❌ Missing entirely                                                                                     | ✅ New exclusion based on palliative care assessment, diagnosis, encounter, or intervention                                                 | ❗ **Add rules** for:  <br> - FACIT-Pal Questionnaire (LOINC 71007-9) <br> - Palliative Care Diagnosis `2.16.840.1.113883.3.464.1003.1167` <br> - Encounter `2.16.840.1.113883.3.464.1003.101.12.1090` <br> - Intervention `2.16.840.1.113883.3.464.1003.198.12.1135` |
| **Lives in Nursing Home**                        | Rule 20 captures housing assessment with result code check                                             | ✔ LOINC 71802-3 = `2.16.840.1.113883.3.225.2001.144` <br> Result = `2.16.840.1.113883.3.464.1003.113.12.1075`                              | ✅ Covered correctly                                                                                                                                                                                                                                                  |
| **Missing Encounter Types**                      | Some newer encounter types (Virtual, Telephone, Home Healthcare) not covered                           | ✔ New Encounters: <br> - Virtual Encounter (1089) <br> - Telephone Visit (1080) <br> - Home Health (1016)                                  | ❗ **If relevant to IPP inclusion**, update encounter logic                                                                                                                                                                                                           |
| **Dementia Medication**                          | Rule 14 handles this well                                                                              | ✔ Matches OID                                                                                                                              | ✅ OK                                                                                                                                                                                                                                                                 |
| **Hospice Exclusion**                            | ❌ Not included                                                                                         | ✔ Must support hospice via: <br> - Encounters <br> - Diagnosis <br> - Assessment (LOINC 45755-6, result “Yes”) <br> - Discharge to hospice | ❗ **Add rules for hospice** exclusion logic                                                                                                                                                                                                                          |
| **Mastectomy (Bilateral/Unilateral)**            | ❌ Missing entirely in these rules                                                                      | ✔ Needed for exclusion: Dx or Procedure from appropriate OIDs                                                                              | ❗ **Add rules** for right, left, and bilateral mastectomy procedures or diagnoses                                                                                                                                                                                    |

---

## 🔧 Summary of What to Add or Update

### 🔸 1. **Add Denominator Exclusion for Palliative Care**

Create new rule(s) checking any of the following:

* **Assessment (FACIT-Pal)** → `2.16.840.1.113883.3.225.2001.136`
* **Diagnosis** → `2.16.840.1.113883.3.464.1003.1167`
* **Encounter** → `2.16.840.1.113883.3.464.1003.101.12.1090`
* **Intervention** → `2.16.840.1.113883.3.464.1003.198.12.1135`

---

### 🔸 2. **Add Denominator Exclusion for Hospice Care**

Check:

* **Encounter with hospice value set** → `2.16.840.1.113883.3.464.1003.1003`
* **Diagnosis** → `2.16.840.1.113883.3.464.1003.1165`
* **Assessment: LOINC 45755-6 (Hospice Care MDS)** with result = `Yes` → `2.16.840.1.113883.3.225.2001.123`
* **Discharge status codes:**

  * Home for Hospice → `2.16.840.1.113883.3.117.1.7.1.209`
  * Facility for Hospice → `2.16.840.1.113883.3.117.1.7.1.207`

---

### 🔸 3. **Add Denominator Exclusion for Mastectomy**

Check for:

#### Right/Left Unilateral Mastectomy (Dx or Procedure):

* **Right Dx:** `2.16.840.1.113883.3.464.1003.198.12.1070`
* **Left Dx:** `2.16.840.1.113883.3.464.1003.198.12.1069`
* **Unspecified laterality Dx:** `2.16.840.1.113883.3.464.1003.198.12.1071`
* **Right Procedure:** `2.16.840.1.113883.3.464.1003.198.12.1134`
* **Left Procedure:** `2.16.840.1.113883.3.464.1003.198.12.1133`

#### Bilateral Mastectomy:

* **Diagnosis**: `2.16.840.1.113883.3.464.1003.198.12.1068`
* **Procedure**: `2.16.840.1.113883.3.464.1003.198.12.1005`

---

### 🔸 4. **(Optional) Expand Encounter Types** (If used in IPP / Denominator logic)

Include codes for:

| Encounter            | OID                                        |
| -------------------- | ------------------------------------------ |
| Virtual Encounter    | `2.16.840.1.113883.3.464.1003.101.12.1089` |
| Telephone Visit      | `2.16.840.1.113883.3.464.1003.101.12.1080` |
| Home Health Services | `2.16.840.1.113883.3.464.1003.101.12.1016` |

---
Thanks — now you’ve provided the **CQL-like logic (from the spec)** for:

* ✅ `Is Age 66 or Older with Advanced Illness and Frailty`
* ✅ `Has Criteria Indicating Frailty`
* ✅ `Has Advanced Illness in Year Before or During Measurement Period`
* ✅ `Has Dementia Medications in Year Before or During Measurement Period`
* ✅ `Is Age 66 or Older Living Long Term in a Nursing Home`

You’re asking to **verify whether your existing Drools rules cover these conditions correctly**.

---

## ✅ Let's Validate Each Spec Against Your Rules

---

### 1. ✅ **AIFrailLTCF.Is Age 66 or Older with Advanced Illness and Frailty**

**Spec Logic:**

```cql
(Age >= 66)
AND Has Criteria Indicating Frailty
AND (
   Has Advanced Illness in Year Before or During MP
   OR Has Dementia Medications in Year Before or During MP
)
```

**Your Rules:**

* Rule 12: Checks age ≥ 66
* Rule 12: Requires **2 encounters with advanced illness dx**
* Rule 14: Checks for **dementia medication**
* Rules 15–19: Frailty checks (device, encounter, dx, assessment, symptom)

✅ **Verdict:** **Yes**, your rules collectively meet this logic:

* Age check present
* Advanced illness covered via encounter-based rules
* Dementia meds checked
* Frailty handled separately (in RuleState==2 section)

📌 **Minor Suggestion:** The logic implies that **either** Advanced Illness or Dementia Meds is enough when frailty exists. Your Rule 12 demands **two separate advanced illness encounters**, which may be stricter. If CQL implies just **1 advanced illness diagnosis**, consider relaxing your logic.

---

### 2. ✅ **AIFrailLTCF.Has Criteria Indicating Frailty**

**Spec Logic:**

* Any of:

  * Device Order (frailty) during measurement period
  * Assessment result = "Frailty Device"
  * Frailty diagnosis
  * Frailty encounter
  * Frailty symptom

**Your Rules:**

* Rule 15 → Device Order
* Rule 16 → Assessment result = Frailty Device
* Rule 17 → Frailty Diagnosis
* Rule 18 → Frailty Encounter
* Rule 19 → Frailty Symptom

✅ **Verdict:** Fully covered

---

### 3. ✅ **AIFrailLTCF.Has Advanced Illness in Year Before or During Measurement Period**

**Spec Logic:**

* Any **diagnosis** in advanced illness value set
* Dx must start within MP or 1 year before

**Your Rule 12:**

* Uses **encounters with advanced illness diagnosis**
* Filters encounter start date within 24 months (2 years), which is actually **more lenient**

📌 **Verdict:** **Covered**, but *your window is wider* (2 years instead of 1 year). You may want to adjust `getIntervalByMonths(...) <= 12` to strictly match the spec.

---

### 4. ✅ **AIFrailLTCF.Has Dementia Medications in Year Before or During Measurement Period**

**Spec Logic:**

* Active dementia meds
* Start/End overlaps with \[MP start - 1 year, MP end]

**Your Rule 14:**

```java
eval(IntervalUtilities.isOverlapping(medication.getStartDate(), medication.getEndDate(), DateUtils.addDays(request.getMeasurementPeriodStart(), -365), request.getMeasurementPeriodEnd()))
```

✅ **Verdict:** Logic is correct and matches CQL perfectly

---

### 5. ✅ **AIFrailLTCF.Is Age 66 or Older Living Long Term in a Nursing Home**

**Spec Logic:**

* Age ≥ 66
* Most recent housing assessment before MP end
* Result = "Lives in nursing home (finding)"

**Your Rule 20:**

* Checks age ≥ 66
* `Assessment` with housing status code `2.16.840.1.113883.3.225.2001.144`
* Result matches `2.16.840.1.113883.3.464.1003.113.12.1075` (which is correct)
* Ends before or on MP end

✅ **Verdict:** Your implementation **matches** the CQL logic.

---

## ✅ Overall Conclusion

Your current Drools rules **match the intent and detail** of the **AIFrailLTCF.* conditions*\* in the updated CQL spec **very well**. ✅

---

### ⚠️ Final Notes / Minor Suggestions

1. **Advanced Illness Time Window**

   * You currently allow **2 years**, but spec says **1 year** before or during MP.
   * 🛠 Consider tightening to `<=12 months` to match.

2. **Relax encounter count for Advanced Illness**

   * Your Rule 12 seems to require **2 advanced illness diagnoses via encounters** (encounter1 and encounter2).
   * CQL suggests **only one** is needed (per person), so your logic might be **too strict**.

3. **Use `last()` logic for Nursing Home assessment**

   * Rule 20 doesn’t implement explicit “last” housing assessment.
   * If you want to mirror CQL, you could sort assessments by date and pick the most recent.

---
Thanks — this is a comprehensive comparison request. You’re asking:

> **Do the existing Drools rules (v12) for `CMS125` denominator exclusions match the updated CQL-based spec (v13+)?**
> You're particularly focusing on:

* `AIFrailLTCF.Is Age 66 or Older with Advanced Illness and Frailty`
* `AIFrailLTCF.Is Age 66 or Older Living Long Term in a Nursing Home`

---

## ✅ TL;DR Summary

| Spec Condition                                 | **Current Rule Coverage**                               | **Matches Spec?**            | **Required Change?**                                   |
| ---------------------------------------------- | ------------------------------------------------------- | ---------------------------- | ------------------------------------------------------ |
| `Is Age 66+ with Advanced Illness and Frailty` | Rules 12 (adv. illness), 14 (dementia), 15–19 (frailty) | ❌ **Partially**              | ✅ Yes – fix logic and ordering                         |
| `Is Age 66+ Living Long-Term in Nursing Home`  | Rule 20                                                 | ⚠️ **Mostly**, but not exact | ⚠️ Yes – needs “last” logic & better result code match |

---

### 📌 Let’s break this down in detail:

---

## 🧠 1. `AIFrailLTCF.Is Age 66 or Older with Advanced Illness and Frailty`

### ✅ CQL Spec Summary:

```
Age ≥ 66
AND Has Criteria Indicating Frailty
AND (
   Has Advanced Illness Dx in Year Before or During MP
   OR Has Dementia Medication in Year Before or During MP
)
```

### ❌ Issues in Current Rule Set:

You **do not** have a **single rule** that evaluates **all 3 conditions together**:

* Age ≥ 66
* Frailty (Rule 15–19 only fire **after** RuleState == 2)
* Dementia (Rule 14)
* Advanced illness (Rules 12, 13)

### ❌ Logical Disconnect

* Your Rule 12 sets `RuleState == 2` **only if two advanced illness encounters exist**
  👉 But CQL only needs **1 diagnosis**
* Frailty rules (15–19) only run **after** RuleState is 2
  👉 This means if a patient only has dementia (not 2 encounters), frailty logic **never runs**
* Dementia (Rule 14) sets RuleState 2, but again, no frailty check runs unless RuleState == 2

### ✅ What’s Missing?

✔️ A wrapper rule that says:

> If Age ≥ 66
> AND (advanced illness dx **OR** dementia meds)
> AND frailty condition (from any of 15–19)

🔥 You need to **combine all three into a composite rule** for exclusion to match spec.

---

## ✅ Suggested Fix

Add a new rule like:

```java
rule "Denominator Exclusion - Age 66+ with Advanced Illness and Frailty"
	when
		request:Request($patient:patient)
		response:Response($measureStatus:measureStatus.get("112"))
		Prerequisite($measureList:measureList)
		measure:EMeasure(...) from $measureList
		eval($measureStatus.getDenominator()==1 && $measureStatus.getDenominatorExclusion()==0)

		// Age ≥ 66
		eval(IntervalUtilities.getIntervalByYears($patient.getDob(),request.getMeasurementPeriodEnd())>=66)

		// Dementia meds OR advanced illness diagnosis
		(
			// Dementia
			medication:ActiveMedication() from $patient.getActiveMedicationsList()
			eval(QDMUtilities.checkCodeAvailablity(measure,QDMCategory.MEDICATION,"2.16.840.1.113883.3.464.1003.196.12.1510",medication.getCode(),medication.getCodeSystemOID()))
			eval(IntervalUtilities.isOverlapping(medication.getStartDate(), medication.getEndDate(), DateUtils.addDays(request.getMeasurementPeriodStart(), -365), request.getMeasurementPeriodEnd()))
		)
		or
		(
			// Advanced Illness Diagnosis
			diagnosis:Diagnosis() from $patient.getDxList()
			eval(QDMUtilities.checkCodeAvailablity(measure,QDMCategory.ATTRIBUTE,"2.16.840.1.113883.3.464.1003.110.12.1082", diagnosis.getCode(), diagnosis.getCodeSystemOID()))
			eval(IntervalUtilities.getIntervalByMonths(diagnosis.getStartDate(), request.getMeasurementPeriodEnd()) <= 12)
		)

		// Frailty condition - reuse any of rules 15–19 or simplify check here
		exists(SomeFrailtyFact)
	then
		$measureStatus.setDenominatorExclusion(1);
		modify(response) {getMeasureStatus().put("112",$measureStatus)}
end
```

You can consolidate frailty logic with a helper method or keep rules 15–19 but ensure they're evaluated **within** this conditional path.

---

## 🧠 2. `Is Age 66 or Older Living Long Term in a Nursing Home`

### ✅ Spec Logic:

```cql
Age ≥ 66
AND Last HousingStatus assessment (ending on or before MP end)
    → result ~ "Lives in nursing home (finding)"
```

### ⚠️ Issues in Current Rule 20:

```java
eval(QDMUtilities.checkCodeAvailablity(measure,QDMCategory.ASSESSMENT,"2.16.840.1.113883.3.225.2001.144",...))
eval(QDMUtilities.checkCodeAvailablity(measure,"Symptom", "2.16.840.1.113883.3.464.1003.113.12.1075",...))
```

* ✅ Correct housing status code
* ❌ But: you're not using `last()` logic — you're matching **any** assessment with that code
* ❌ Also: you're checking the result code using `"Symptom"` QDM category – likely incorrect

### ✅ Fixes Needed:

1. **Get the most recent assessment (last one)** ending ≤ MP end
2. Match result to `"Lives in nursing home (finding)"` concept (`2.16.840.1.113883.3.464.1003.113.12.1075`) using correct QDM type (likely `QDMCategory.FINDING`)

---

### ✅ Suggested Fix (Pseudocode):

```java
List<Assessment> housingAssessments = patient.getRiskAssessmentList().stream()
	.filter(assessment -> 
		QDMUtilities.checkCodeAvailablity(measure, QDMCategory.ASSESSMENT, "2.16.840.1.113883.3.225.2001.144", ...) &&
		assessment.getEndDate() != null &&
		!assessment.getEndDate().after(request.getMeasurementPeriodEnd())
	)
	.sorted(Comparator.comparing(Assessment::getEndDate).reversed()) // latest first
	.collect(Collectors.toList());

if (!housingAssessments.isEmpty() && 
	QDMUtilities.checkCodeAvailablity(measure, QDMCategory.FINDING, "2.16.840.1.113883.3.464.1003.113.12.1075", housingAssessments.get(0).getResultCode(), ...)) {
		// set exclusion
}
```

This matches the CQL semantics of **`Last()` assessment where result \~ Lives in nursing home**.

---

## ✅ Final Summary of Required Changes

| Area                                      | Issue                                             | Resolution                                                        |
| ----------------------------------------- | ------------------------------------------------- | ----------------------------------------------------------------- |
| `Age 66+ with advanced illness & frailty` | ❌ No composite rule combining the 3 conditions    | ✅ Add composite rule as shown                                     |
| Frailty logic                             | Only runs if `RuleState == 2`                     | ✅ Integrate directly into composite rule or make it pre-condition |
| Advanced illness                          | Rule 12 requires 2 encounters (too strict)        | ✅ Accept 1 diagnosis in 1 year (per spec)                         |
| Dementia Meds                             | OK in Rule 14                                     | ✅ No change                                                       |
| Nursing Home                              | Rule 20 lacks `last()` assessment logic           | ✅ Refactor to get last assessment before MP end                   |
| Result Code QDM                           | Misuse of `"Symptom"` category for housing status | ✅ Use correct category (`FINDING`)                                |

---



Would you like help writing the missing rules for hospice, palliative care, or mastectomy? I can draft them quickly for you.
