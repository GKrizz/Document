

## 🔍 IPP Criteria Evaluation

### ✅ **1. Age ≥ 13 at start of measurement period**

You haven't defined your Measurement Period in the XML, but we can assume it's a typical calendar year — e.g., `2025-01-01` to `2025-12-31`.

The patient's earliest activity is on `2024-10-27`, and the most recent is `2025-11-28`.

Assuming the patient's birthdate makes them ≥13, this criterion **passes**.

✅ **IPP age requirement is met**

---

### ✅ **2. First SUD Episode During Measurement Period**

#### ✅ Step 1: Do you have a **Substance Use Disorder diagnosis** during a qualifying encounter within the measurement period?

You do. For example:

#### 📌 Encounter:

```xml
<code code="185463005" codeSystem="2.16.840.1.113883.6.96" codeSystemName="SNOMEDCT"/>
<effectiveTime>
  <low value="20250828160000"/>
</effectiveTime>
```

#### 📌 Diagnosis:

```xml
<value code="5602001" codeSystem="2.16.840.1.113883.6.96"/>
```

✅ This is in the **Substance Use Disorder** value set (2.16.840.1.113883.3.464.1003.106.12.1001)

➡️ **RelevantPeriod of encounter**: `2025-08-28`
➡️ **Diagnosis start date (prevalencePeriod.low)**: `2025-08-28`

⏳ It happens during measurement period ✅
📍 Diagnosis occurs within the same encounter's relevant period ✅
📅 Diagnosis starts **more than 47 days before end of measurement period** ✅

✅ So this is a **valid First SUD Episode**
Let’s call this `FirstSUD = 2025-08-28`

---

### ❌ **3. No History of SUD Diagnosis or Treatment in the 60 Days Before First SUD Episode**

This is **where the patient fails**.

Per spec, the patient **must not have** any of the following **in the 60 days before `FirstSUD = 2025-08-28`**:

#### Check these categories:

| Type                                        | Value Set Name                       | Value Set OID                              | Matching Code in XML? |
| ------------------------------------------- | ------------------------------------ | ------------------------------------------ | --------------------- |
| Intervention, Performed                     | **Substance Use Disorder Treatment** | `2.16.840.1.113883.3.464.1003.106.12.1005` | ✅ `24165007` (SNOMED) |
| Intervention, Performed                     | **Psych Visit Psychotherapy**        | `2.16.840.1.113883.3.526.3.1496`           | ✅ `90837`             |
| Procedure, Performed                        | Long/Short Acting Medication Admin   | `...1003.1156` / `...1003.1157`            | ❌ None seen           |
| Encounter with SUD diagnosis within 60 days | Derived                              | ✅ Yes, exists                              |                       |
| Medication Orders                           | SUD Meds                             | ❌ Not present                              |                       |

Let’s analyze this closely.

---

## 🚨 Failing Condition: **History of SUD Diagnosis or Treatment Exists**

Your XML includes:

### 🚫 Substance Use Disorder Treatment BEFORE First SUD

```xml
<entry>
  <act classCode="ACT" moodCode="EVN">
    <code code="24165007" codeSystem="2.16.840.1.113883.6.96"/>
    <effectiveTime>
      <low value="20250901160000"/>
    </effectiveTime>
  </act>
</entry>
```

That’s **Sept 1, 2025**, and your **First SUD is Aug 28, 2025** — so:

📅 Time between: **4 days after** the First SUD Episode.

✅ That's fine.

But then...

### 🚫 Psychotherapy Encounter Before First SUD

```xml
<code code="90837" codeSystem="2.16.840.1.113883.6.12"/>
<effectiveTime>
  <low value="2025-07-28"/>
</effectiveTime>
```

❌ **This is 31 days BEFORE** the First SUD Episode (`2025-08-28`), so it counts as **History of SUD Treatment**

Even if there were no diagnosis directly associated, the `Psychotherapy` code is in the `History of SUD Treatment` clause by virtue of appearing in the relevant value set.

---

## ❌ Conclusion: Patient fails IPP due to prior treatment

To summarize:

| Checkpoint                                         | Status                                            |
| -------------------------------------------------- | ------------------------------------------------- |
| Age ≥ 13                                           | ✅ Pass                                            |
| First SUD Episode exists                           | ✅ Pass                                            |
| SUD diagnosis + qualifying encounter               | ✅ Pass                                            |
| Diagnosis date ≥ 47 days before MP end             | ✅ Pass                                            |
| **No SUD diagnosis or treatment in 60 days prior** | ❌ **FAIL** (has 90837 psych visit 31 days before) |

---

## ✅ How to Fix (For Testing Purposes)

If your goal is to **force this patient into the IPP** for testing:

### Option 1: Remove the Psychotherapy Visit

Delete or comment out the entry with:

```xml
<code code="90837" ... />
<effectiveTime><low value="20250728"... />
```

### Option 2: Move Psychotherapy Date **after** FirstSUD

Change `20250728` to e.g. `20250901`, which is **after First SUD (2025-08-28)**

---

## ✅ Once Fixed — Patient Should Be in IPP

After that, reprocess the patient. The JSON response should show:

```json
"ipp": 1
```

Then you can proceed to **denominator and numerator**.
