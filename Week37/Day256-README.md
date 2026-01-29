
# ✅ WHAT TO DO NEXT (POST–DATA PREPARATION CHECKLIST)

You are now in the **FINAL PHASE**: **System update → Job run → Validation → Sign-off**

---

## 🔹 STEP 1: FINAL DATABASE SAFETY CHECK (2026)

You already did most of this, just **lock it**:

### 1️⃣ Confirm ZERO NULL / EMPTY (FINAL)

```sql
SELECT COUNT(*) 
FROM ecqm_specifications_2026
WHERE qdm_category IS NULL OR TRIM(qdm_category)='';
```

✔ Must be `0` (you already confirmed)

### 2️⃣ Confirm NO Scientific Notation Corruption

```sql
SELECT COUNT(*)
FROM ecqm_specifications_2026
WHERE code ~ '[0-9]+E\\+[0-9]+'
   OR valueset_oid ~ '[0-9]+E\\+[0-9]+';
```

✔ Must be `0`

➡️ **If both are clean → proceed**

---

## 🔹 STEP 2: RUN DATAGATEWAY UPDATE APIs (MANDATORY)

This step **pushes DB data into runtime tables**.

### 1️⃣ Update eCQM Specifications

```
https://datagateway.glaceemr.com/DataGateway/eCQMServices/updateECQMSpecification?reportingYear=2026
```

✔ Updates:

* `quality_measure_specification`
* `quality_measure_benchmark_2026`

📌 **Verify after run**

```sql
SELECT COUNT(*) FROM quality_measure_specification WHERE reporting_year=2026;
```

---

### 2️⃣ Update PQRS Specifications

```
https://datagateway.glaceemr.com/DataGateway/PQRSServices/updatePQRSSpecification?reportingYear=2026
```

✔ Updates:

* `quality_measure_pqrs_specification`

📌 Verify no missing CMS IDs

---

## 🔹 STEP 3: CONFIGURE MEASURES FOR PROVIDERS (UI)

Go to:

```
Configure → General Practice Settings → Meaningful Use → QPP Configuration
```

### For EACH Provider:

1. Select **Reporting Year = 2026**
2. Reporting Period:

   * Full year (or custom if required)
3. Submission Method:

   * **EHR**
4. Add required **Quality Measures**
5. Save

📩 **Send mail to**:

* **Viji**
* **Jagapathi**

➡️ Ask them to verify:

* Macra elements
* Lab code mappings

⛔ **Do NOT proceed until they confirm**

---

## 🔹 STEP 4: RUN MIPS PERFORMANCE JOB (CRITICAL)

### Job URL

```
https://emrbatch.glaceemr.com/GlaceBatch/jobs/MIPSPerformanceJob
```

### Parameters (example)

```
mode=3
reportingYear=2026
quartzId=CMS2026_<acctid>
accid=<acctid>
isMonthlyReport=false
```

📌 **Rules**

* `mode=3` → Full reporting year
* `quartzId` → unique every run
* DO NOT change other params

### Verify Job Completion

✔ Exit Code → **Completed**
✔ No failures

---

## 🔹 STEP 5: VALIDATE IN UI (VERY IMPORTANT)

### 1️⃣ MIPS Performance Report

```
Reports → MIPS Performance Report
```

Check:

* Last Updated Date = **Today**
* Measures showing **non-zero counts**

### 2️⃣ Drill Down

* Click **Quality Measures**
* Click **patient counts**
* Verify patients load

⚠️ If any **numerator = 0 unexpectedly** → notify MIPS team

---

## 🔹 STEP 6: EXPORT & VALIDATE REPORTS

From **MIPS Performance Report**:

### Download:

* ✅ **PDF**
* ✅ **Excel**
* ✅ **Quality JSON**
* ✅ **PI JSON**
* ✅ **QPP JSON**

### Validate:

* CMS IDs
* QDM categories
* Measure counts
* Stratification (if applicable)

---

## 🔹 STEP 7: CYPRESS VALIDATION (OPTIONAL BUT RECOMMENDED)

Login:

```
http://cypress.glaceemr.com:444
```

✔ Upload:

* QRDA I
* QRDA III

✔ Confirm:

* No structural errors
* Measure logic passes

---

## 🔹 STEP 8: FINAL SIGN-OFF CHECKLIST ✅

You can officially say **DONE** when:

✔ All 49 measures imported
✔ No NULL / empty QDM
✔ No scientific notation
✔ Datagateway APIs executed
✔ MIPS Job completed
✔ Reports validated
✔ Cypress (if required) clean

---

## 🟢 WHAT YOU SHOULD DO **RIGHT NOW**

👉 **Next immediate action**:
**Run Datagateway APIs for 2026**
Then proceed to **MIPS Job**

---
