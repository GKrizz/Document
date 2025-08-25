# Colorectal Cancer Screening (CMS130v13)

## 📌 Overview

This repository implements the **Colorectal Cancer Screening** measure (**CMS130v13**) from the 2025 eCQM specifications.
The measure calculates the **percentage of adults aged 45–75** who received appropriate colorectal cancer screening during the measurement period.

---


## 👥 Population Criteria

### Initial Population

* Patients **46–75 years** old at the end of the measurement period
* At least **one qualifying encounter** during the measurement period

### Denominator

* Same as Initial Population

### Denominator Exclusions

* Patients in **hospice** during the measurement period
* History of **total colectomy** or **colorectal cancer**
* Patients **≥66 years** with:

  * **Frailty** and **advanced illness**
  * **Long-term nursing home residence**
* Patients receiving **palliative care**

### Numerator

Patients with **at least one** of the following screenings:

| Screening Test                 | Timeframe (valid if done)               |
| ------------------------------ | --------------------------------------- |
| FOBT (Fecal Occult Blood Test) | Within measurement period               |
| Stool DNA + FIT                | Measurement period or prior **2 years** |
| Flexible Sigmoidoscopy         | Measurement period or prior **4 years** |
| CT Colonography                | Measurement period or prior **4 years** |
| Colonoscopy                    | Measurement period or prior **9 years** |

---

## 📊 Stratifications

1. Age **46–49**
2. Age **50–75**

---

## ⚠️ Notes

* Do **not** count:

  * Digital rectal exams (DRE)
  * In-office FOBT from DRE samples
* Screenings performed **outside the measurement year** still count if within the valid lookback period.

---

## 🛠️ Implementation Tips

* Requires **claims, EHR, or clinical data** with procedure codes (CPT, HCPCS, LOINC, SNOMED).
* Ensure **encounter codes** align with measure specifications.
* Apply **lookback logic** correctly (2, 4, 9 years depending on test type).

---

## 📚 References

* [eCQI Resource Center – CMS130v13](https://ecqi.healthit.gov/ecqm/ep/2025/cms130v13)
* [Measure Specification PDF](https://ecqi.healthit.gov/sites/default/files/ecqm/measures/CMS130v13.html)

---
