
# 📘 CMS146 (Measure ID 66) 

**Date:** *Today*
**Patient ID:** `2942`
**Provider ID:** `1`
**Reporting Year:** `2025`

---

## 🎯 Objective

To evaluate **CMS146 – Appropriate Testing for Pharyngitis** and validate why the patient is:

* **IPP = 1**
* **Denominator = 1**
* **Numerator = 0**

---

## 🧑‍⚕️ Patient Eligibility

### Age Check (≥ 3 years)

```sql
SELECT
  patient_registration_id,
  EXTRACT(YEAR FROM AGE('2025-01-01', patient_registration_dob)) AS age
FROM patient_registration
WHERE patient_registration_id = 2942;
```

**Result:**

```
Age = 4
```

✅ Patient meets age criteria for CMS146.

📷 **UI Evidence:**
![Patient Age](https://github.com/user-attachments/assets/d7c903a9-a221-4396-82db-e264527668d6)

---

## 🏥 Qualifying Encounters (IPP Logic)

### CPT Codes Used

* **99341**
* **99342**

### Service Details

```sql
SELECT
  sd.service_detail_id,
  sd.service_detail_dos,
  c.cpt_cptcode
FROM service_detail sd
JOIN cpt c ON c.cpt_id = sd.service_detail_cptid
WHERE sd.service_detail_patientid = 2942
  AND sd.service_detail_sdoctorid = 1
  AND sd.service_detail_dos BETWEEN '2025-01-01' AND '2025-12-28';
```

**Result:**

```
2025-12-11 → 99341
2025-12-11 → 99342
```

✅ Valid outpatient encounters → **IPP = 1**

📷 **UI Evidence:**
![Encounter CPTs](https://github.com/user-attachments/assets/2f528722-eeef-4167-9b81-0497826896c2)
![Service Details](https://github.com/user-attachments/assets/7cb9fac8-9eab-49e2-b977-6ab6a939da4e)

---

## 🤒 Diagnosis (Pharyngitis)

### ICD-10 Codes

* **J02.0** – Streptococcal pharyngitis
* **J02.8** – Other acute pharyngitis

```sql
SELECT
  patient_assessments_dxcode,
  patient_assessments_encounterdate
FROM patient_assessments
WHERE patient_assessments_patientid = 2942;
```

**Result:**

```
J02.0, J02.8 on 2025-12-11
```

✅ Diagnosis qualifies for CMS146 denominator.

📷 **UI Evidence:**
![Diagnosis](https://github.com/user-attachments/assets/b4ad3119-a72d-4659-949a-834cd267c06b)

---

## 💊 Antibiotic Prescription

### Current Medication

```sql
SELECT
  current_medication_rx_name,
  current_medication_start_date
FROM current_medication
WHERE current_medication_patient_id = 2942
  AND current_medication_is_active = true;
```

**Result:**

```
Minocycline HCl ER
Start Date: 2025-12-12
```

✅ Antibiotic within **±3 days** of encounter
→ **Denominator = 1**

📷 **UI Evidence:**
![Medication](https://github.com/user-attachments/assets/41df47b3-18cc-4928-a728-057d179aabcb)

---

## 📊 Measure Calculation Status (DB)

```sql
SELECT
  quality_measures_patient_entries_ipp,
  quality_measures_patient_entries_denominator,
  quality_measures_patient_entries_numerator
FROM quality_measures_patient_entries
WHERE quality_measures_patient_entries_patient_id = 2942
  AND quality_measures_patient_entries_measure_id = '66'
  AND quality_measures_patient_entries_reporting_year = 2025;
```

### Current Values

| Component             | Value |
| --------------------- | ----- |
| IPP                   | 1     |
| Denominator           | 1     |
| Numerator             | 0     |
| Denominator Exclusion | 0     |
| Denominator Exception | 0     |

📷 **DB Evidence:**
![Measure Status](https://github.com/user-attachments/assets/38ee1751-1aa5-48c6-8ba6-195b0d75a671)

---

## 🧪 Investigation (Why Numerator = 0)

### CMS146 Numerator Rule

> A **Group A Streptococcus test** must be performed
> **Within 3 days before or after** the encounter date.

### Findings

* **No qualifying strep test** found in:

  * `lab_entries`
  * `lab_entries_parameter`
* Investigation tab shows **no Group A Strep lab mapped**

📷 **MACRA / Investigation Tab:**
![MACRA Tab](https://github.com/user-attachments/assets/1d7185be-9792-4a87-8c35-4812c9b2561b)

---

## 🚫 Root Cause Summary

| Requirement            | Status        |
| ---------------------- | ------------- |
| Age ≥ 3                | ✅             |
| Eligible Encounter     | ✅             |
| Pharyngitis Diagnosis  | ✅             |
| Antibiotic Prescribed  | ✅             |
| **Group A Strep Test** | ❌ **Missing** |

➡ Because **no qualifying strep lab** exists in DB,
**Numerator remains 0**

---

## ✅ Final Conclusion

* Patient is **correctly included** in:

  * **IPP**
  * **Denominator**
* **Numerator = 0 is correct** for current data
* To make **Numerator = 1**, a **Group A Streptococcus test** must be:

  * Ordered/performed
  * Stored in `lab_entries` (and/or `lab_entries_parameter`)
  * Within **±3 days** of `2025-12-11`

---


