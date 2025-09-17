# Day 110 – September 5, 2025

## ✅ Summary of Work

### 🔧 Code Changes

#### Modified Classes:

* **`GetQDMData.java`**

  * Updated logic for:

    * `getPhysicalExamPerformed()`
    * `getProceduresPerformed()`
* **`QRDAServiceImpl.java`**

  * Enhanced element extraction for `elementName` with null and colon check:

    ```java
    String elementName = "";
    String description = performedPhysicalExam.get(eachPhysicalExam).getDescription();

    if (description != null && description.contains(":")) {
        String[] elementDesc = description.split(":");
        if (elementDesc.length > 1) {
            elementName = elementDesc[1].trim();
        } else {
            elementName = elementDesc[0].trim(); 
        }
    } else {
        elementName = description != null ? description.trim() : "";
    }
    ```

---

## 🧪 CMS145v13 – Coronary Artery Disease (CAD)

### 📌 Measure Details

* **Measure ID**: 7
* **Description**: CAD: Beta-Blocker Therapy – Prior MI or LVEF ≤ 40%
* **Provider ID**: `2436`
* **Provider Name**: Sheila Willis

### ✅ Provider Configuration & Measure Mapping

```sql
-- Insert MIPS Configuration
INSERT INTO macra_provider_configuration (...) VALUES (...);

-- Map Quality Measure
INSERT INTO quality_measures_provider_mapping (...) VALUES (...);
```

---

## 🔎 Data Validation & Patient Records

### 👤 Sample Patient ID: `3276829`

### 📊 JSON Analysis

* **Encounters**: Present (`99213`, `99202`)
* **Diagnosis**: CAD – SNOMED `15990001`
* **Procedures**: ❌ Missing in JSON

```json
"procedureList": [] // Should include 92920 and 33510
```

### 🧾 Relevant APIs

* `getPatientsSeen`
* `calculateMIPSPerformance`
* `generateAndValidateQDM`

---

## 📥 Database Verification

### ✅ Procedures Exist in DB

```sql
SELECT cpt_cptcode
FROM service_detail sd
JOIN cpt c ON sd.service_detail_cptid = c.cpt_id
WHERE sd.service_detail_patientid = 3276829;
```

| Encounter ID | Encounter Created Date | Service Detail DOS | CPT Code |
| ------------ | ---------------------- | ------------------ | -------- |
| 768759       | 2025-05-26 08:00:00    | 2016-09-14         | 33510    |
| 768760       | 2025-05-30 08:00:00    | 2016-09-14         | 33510    |
| 768759       | 2025-05-26 08:00:00    | 2014-11-13         | 92920    |
| 768760       | 2025-05-30 08:00:00    | 2014-11-13         | 92920    |

🛑 **Issue**: Procedure data excluded due to strict date filter logic in `QRDAServiceImpl.importEncounterAndServiceDetails`.

---

## 📍 Files to Check

* `QRDAServiceImpl.java` → `importEncounterAndServiceDetails`
* `QRDAQueryFactory.java` → `importEncounterAndServiceDetails`

---

## 🔁 Date Match Validation

### ✅ Diagnosis, Diagnostic Studies & Encounters Match XML & JSON

| Element   | XML Date   | JSON Timestamp (ms) | JSON Date    | Match |
| --------- | ---------- | ------------------- | ------------ | ----- |
| Diagnosis | 2023-11-06 | 1699270608000       | Nov 6, 2023  | ✔     |
| Encounter | 2025-01-17 | 1737227280000       | Jan 17, 2025 | ✔     |
| EF Study  | 2025-04-18 | 1740150480000       | Apr 18, 2025 | ✔     |

---

## ⚠️ Cypress vs My Report Comparison

| Source    | IPOP | DENOM | NUMER | DENEXCEP |
| --------- | ---- | ----- | ----- | -------- |
| Cypress   | 24   | 17    | 2     | 9        |
| My Report | 17   | 9     | 2     | 7        |

---

## 🛠️ SQL Queries Reference

* Patients under provider `2436`
* Chart & encounter filtering
* Procedure validation based on DOS
* Adding new `lab_entries_richtext` column

---
