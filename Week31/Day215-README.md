## eCQM 122 v13.0 – HbA1c Flow, QDM Framing & MIPS Issue Analysis

---
## 🔴 What is the Issue?(What Dr. Atluri Reported)

Dr. **Atluri** is reporting that:

> **Some patients have HbA1c < 9**,
> but **MIPS is still showing them as “A1c > 9” (NOT MET)**.

This directly affects:

> **eCQM 122 – Diabetes: Glycemic Status Assessment Greater Than 9%**

---

## 🎯 What the Measure Expects (Ground Truth)

For **patients aged 18–75 with diabetes**:

| Condition   | Measure Outcome                 |
| ----------- | ------------------------------- |
| HbA1c ≤ 9.0 | ❌ NOT in numerator (GOOD / MET) |
| HbA1c > 9.0 | ✅ IN numerator (NOT MET)        |
| No HbA1c    | ✅ IN numerator (NOT MET)        |

---

## 🧠  Official Measure Logic (Simplified)

```
Age 18–75 + Diabetes + Visit
        ↓
No hospice / palliative / frailty exclusions
        ↓
Any HbA1c or GMI this year?
        ↓
NO  → NUMERATOR (Missing)
YES → Use most recent date
        ↓
Lowest result > 9% ?
        ↓
YES → NUMERATOR
NO  → GOOD (Not in numerator)
```

---
## 🧭 Where HbA1c Data Comes From (UI → DB)

**UI Tab**

```
Tab 	: Investication tab --> because tests are performed in this tab (Investigation tab → Lab tests)
```

**Database Table**

```
lab_entries_parameter   
```


---
## Lab Data → Code Configuration → QDM → MIPS (End-to-End Flow)

---

## 1️⃣ Purpose of This Document



It covers:

* Database relationships
* Code configuration mapping
* QDM request construction
* Exact decision logic for **LOINC-based inclusion**
* Real behavior seen in code

---

## 2️⃣ Core Tables & Their Roles

### 🔹 `lab_entries` (Test / Investigation level)

Represents a **lab test order** (parent record).

**Key columns:**

* `lab_entries_testdetail_id` → Primary key
* `lab_entries_test_id` → Links to `lab_description`
* `lab_entries_loinc` → LOINC at test level
* `lab_entries_snomed`, `lab_entries_cpt`, `lab_entries_cvx`
* `lab_entries_test_status`
* `lab_entries_ord_on`, `lab_entries_perf_on`
* `lab_entries_chartid` → Patient

---

### 🔹 `lab_description` (Code Configuration – Tests)

This is the **Code Configuration tab for lab_entries**.

**Purpose:**

* Defines **what the test is**
* Holds **configured LOINC** for the test

**Relation:**

```text
lab_entries.lab_entries_test_id
        ↓
lab_description.lab_description_testid
```

**Key columns:**

* `lab_description_testid`
* `lab_description_test_desc`
* `lab_description_loinc`
* `lab_description_isactive`

---

### 🔹 `lab_entries_parameter` (Result / Parameter level)

Represents **individual lab results** (HbA1c value, MPV, PLT, etc.).

**Key columns:**

* `lab_entries_parameter_testdetailid` → FK to `lab_entries`
* `lab_entries_parameter_value`
* `lab_entries_parameter_code` → LOINC (very important)
* `lab_entries_parameter_code_system`
* `lab_entries_parameter_date`
* `lab_entries_parameter_isactive`

---

### 🔹 `lab_parameters` (Code Configuration – Parameters)

This is the **Code Configuration tab for lab_entries_parameter**.

**Relation:**

```text
lab_entries_parameter.lab_entries_parameter_mapid
        ↓
lab_parameters.lab_parameters_id
```

**Key columns:**

* `lab_parameters_name`
* `lab_parameters_displayname`
* `lab_parameters_units`
* `lab_parameters_type`

---

## 3️⃣ Overall Relationship Diagram

```text
lab_description (Code Config - Test)
        ↑
        |
lab_entries (Test / Investigation)
        ↑
        |
lab_entries_parameter (Result)
        ↓
lab_parameters (Code Config - Parameter)
```

---


##  HbA1c Codes Used by Measure 122

The measure only recognizes these **LOINC codes**:

```
17855-8
17856-6
4548-4
4549-2
96595-4
97506-0 (GMI)
```

---

## 4️⃣ SQL to Understand the Mapping



```
SELECT                                     
  lab_entries_parameter_date,
  lab_entries_parameter_value,
  lab_entries_parameter_code,
  lab_entries_parameter_code_system,
  lab_entries_parameter_name
FROM lab_entries_parameter
WHERE  lab_entries_parameter_isactive = true
  AND lab_entries_parameter_date BETWEEN '2025-01-01' AND '2025-12-31'
  AND (
       lab_entries_parameter_code IN ('17855-8','17856-6','4548-4','4549-2','96595-4')
       OR lab_entries_parameter_code = '97506-0'
      )
ORDER BY lab_entries_parameter_date DESC;
```
```
SELECT
  ld.lab_description_test_desc,
  ld.lab_description_loinc,
  lep.lab_entries_parameter_code,
  lep.lab_entries_parameter_name
FROM lab_entries_parameter lep
JOIN lab_entries le
  ON lep.lab_entries_parameter_testdetailid = le.lab_entries_testdetail_id
JOIN lab_description ld
  ON le.lab_entries_test_id = ld.lab_description_testid
WHERE lep.lab_entries_parameter_code IN
('17855-8','17856-6','4548-4','4549-2','96595-4','97506-0')
AND ld.lab_description_test_desc NOT ILIKE '%A1C%';

```
### 🔹 Test-level mapping (lab_entries → lab_description)

```sql
SELECT
  le.lab_entries_testdetail_id,
  le.lab_entries_chartid,
  ld.lab_description_test_desc,
  ld.lab_description_loinc,
  le.lab_entries_loinc
FROM lab_entries le
JOIN lab_description ld
  ON le.lab_entries_test_id = ld.lab_description_testid
WHERE ld.lab_description_isactive = true;
```

---

### 🔹 Parameter-level mapping (lab_entries_parameter → lab_parameters)

```sql
SELECT
  lep.lab_entries_parameter_name,
  lep.lab_entries_parameter_value,
  lep.lab_entries_parameter_code,
  lp.lab_parameters_units
FROM lab_entries_parameter lep
JOIN lab_parameters lp
  ON lep.lab_entries_parameter_mapid = lp.lab_parameters_id
WHERE lep.lab_entries_parameter_isactive = true;
```


---

## 5️⃣ Where QDM Generation Starts

### API Entry Point

```java
@RequestMapping("/generateAndValidateQDM")
```

### High-level Flow

1. Identify provider & reporting year
2. Fetch **measure list**
3. Fetch **measure code list** (LOINC, SNOMED, CPT, etc.)
4. Build **QDM Request**
5. POST to **ECQM Validation Server**
6. Save measure results

---

## 6️⃣ How Lab Data Is Collected for QDM

### Method Used

```java
getInvestigationQDM(...)
```

This pulls data from:

* `lab_entries` → InvestigationQDM
* `lab_entries_parameter` → ParameterDetails
* Merges both into a **single Investigation list**

Only labs with **at least one code** (LOINC / SNOMED / CPT / CVX) are considered.

---

## 7️⃣ Conversion to QDM LabTest Objects

### Method

```java
getLabTestQDMForInvestigation(...)
```

This method decides:

* ❌ Ignore lab
* ✅ Include lab
* 🚫 Include as **Not Done (Negation)**

---

## 8️⃣ 🔑 CRITICAL DECISION LOGIC (MOST IMPORTANT)

### Inclusion Condition (Normal Labs)

```java
codeList.contains("," + eachObj.getCode() + ",")
```

⚠️ This is the **hard filter**.

---

### What is `codeList`?

* Comes from **measure definition**
* Contains only codes allowed by the **eCQM**
* Example:

```text
17855-8,17856-6,4549-2
```

---

## 9️⃣ Example: LOINC = `11111`

### Case ❌ Not in Measure CodeList

```text
codeList = 17855-8,4549-2
LOINC = 11111
```

```java
",17855-8,4549-2,".contains(",11111,") → false
```

➡️ **Lab is ignored**
➡️ **Not sent to QDM**
➡️ **MIPS does NOT see it**

---

### Case ✅ In Measure CodeList

```text
codeList = 17855-8,11111,4549-2
```

```java
",17855-8,11111,4549-2,".contains(",11111,") → true
```

➡️ **Lab included**
➡️ **Sent to QDM**
➡️ **Used in measure logic**

---

## 🔥 IMPORTANT: This Is By Design

> **Database presence ≠ Measure eligibility**

Only codes defined in the **measure’s value set** are evaluated.

---

## 1️⃣0️⃣ Special Case: NOT DONE Labs

```java
if (eachObj.getStatus() == -1)
```

In this case:

* CodeList check is skipped
* Lab is sent as **Negation**
* Used for **“Not Done” logic in measures**

---

## 1️⃣1️⃣ How Code System Is Determined

| Source Value             | Code System |
| ------------------------ | ----------- |
| `2.16.840.1.113883.6.96` | SNOMED      |
| `2.16.840.1.113883.6.1`  | LOINC       |
| `54`                     | SNOMED      |
| `51` / `LOINC`           | LOINC       |

---

## 1️⃣2️⃣ Common Real-World Issues Explained

### ❌ “HbA1c showing under wrong test name”

Cause:

* Parameter LOINC exists
* Test-level description mismatched
* QDM uses **parameter code**, not test name

---

### ❌ “Latest A1c is ignored”

Cause:

* Latest A1c LOINC **not in measure code list**

---

### ❌ “Value exists in DB but MIPS says NOT MET”

Cause:

* Code mismatch
* Wrong LOINC mapping
* Or parameter not numeric

---

## 1️⃣3️⃣ Final Mental Model (One Line)

> **QDM does not trust UI, DB, or test names.
> It trusts ONLY measure-defined code lists.**

---
