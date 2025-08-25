# Breast Cancer Screening eCQM (Electronic Clinical Quality Measure)

**Measurement Specification & Value Set Reference**

---

## Table of Contents

* [Population Criteria](#population-criteria)
* [Definitions](#definitions)
* [Functions](#functions)
* [Terminology](#terminology)
* [Data Criteria (QDM Data Elements)](#data-criteria)
* [Supplemental Data Elements](#supplemental-data-elements)
* [Risk Adjustment Variables](#risk-adjustment-variables)

---

## Population Criteria

### Initial Population

Patients who:

* Are **Female**
* Are between **52 and 74 years of age** at the end of the measurement period
* Have at least one **qualifying adult outpatient encounter** during the measurement period

### Denominator

* Same as Initial Population

### Denominator Exclusions

Exclude patients who:

* Are in **hospice**
* Have **bilateral mastectomy**, or **left and right mastectomies**
* Are **66+ years old** with **advanced illness and frailty**
* Are **66+ years old** and live in a **nursing home long-term**
* Have received **palliative care** during the measurement period

### Numerator

Patients who received a **mammogram** between:

* **October 1, two years prior to the measurement period**, and
* **End of the measurement period**

### Numerator Exclusions

* None

### Denominator Exceptions

* None

### Stratification

* None

---

## Definitions

### Qualifying Encounters

| Encounter Description                                        | OID                                      |
| ------------------------------------------------------------ | ---------------------------------------- |
| Office Visit                                                 | 2.16.840.1.113883.3.464.1003.101.12.1001 |
| Annual Wellness Visit                                        | 2.16.840.1.113883.3.526.3.1240           |
| Preventive Care Services Established Office Visit, 18 and Up | 2.16.840.1.113883.3.464.1003.101.12.1025 |
| Preventive Care Services Initial Office Visit, 18 and Up     | 2.16.840.1.113883.3.464.1003.101.12.1023 |
| Home Healthcare Services                                     | 2.16.840.1.113883.3.464.1003.101.12.1016 |
| Virtual Encounter                                            | 2.16.840.1.113883.3.464.1003.101.12.1089 |
| Telephone Visits                                             | 2.16.840.1.113883.3.464.1003.101.12.1080 |

---

## Encounter Types (MISSING in source)

* Encounter Inpatient
  `2.16.840.1.113883.3.666.5.307`

* Frailty Encounter
  `2.16.840.1.113883.3.464.1003.101.12.1088`

* Hospice Encounter
  `2.16.840.1.113883.3.464.1003.1003`

* Palliative Care Encounter
  `2.16.840.1.113883.3.464.1003.101.12.1090`

---

## Denominator Exclusion Value Sets

### Mastectomy Diagnoses and Procedures

* **Right Mastectomy Diagnosis**

  * Status Post Right Mastectomy: `2.16.840.1.113883.3.464.1003.198.12.1070`
  * Unilateral Mastectomy (Unspecified Laterality): `2.16.840.1.113883.3.464.1003.198.12.1071`

* **Right Mastectomy Procedure**

  * Unilateral Mastectomy Right: `2.16.840.1.113883.3.464.1003.198.12.1134`

* **Left Mastectomy Diagnosis**

  * Status Post Left Mastectomy: `2.16.840.1.113883.3.464.1003.198.12.1069`
  * Unilateral Mastectomy (Unspecified Laterality): `2.16.840.1.113883.3.464.1003.198.12.1071`

* **Left Mastectomy Procedure**

  * Unilateral Mastectomy Left: `2.16.840.1.113883.3.464.1003.198.12.1133`

* **Bilateral Mastectomy Diagnosis**

  * History of Bilateral Mastectomy: `2.16.840.1.113883.3.464.1003.198.12.1068`

* **Bilateral Mastectomy Procedure**

  * Bilateral Mastectomy: `2.16.840.1.113883.3.464.1003.198.12.1005`

---

## Special Conditions & Codes

### Hospice

* Hospice Care Ambulatory: `2.16.840.1.113883.3.526.3.1584`
* Hospice care \[Minimum Data Set] (LOINC: 45755-6): `2.16.840.1.113883.3.225.2001.142`

  * Yes (qualifier): `2.16.840.1.113883.3.225.2001.123`
* Hospice Diagnosis: `2.16.840.1.113883.3.464.1003.1165`

### Frailty & Advanced Illness

* Frailty Device: `2.16.840.1.113883.3.464.1003.118.12.1300`
* Medical equipment used (LOINC: 98181-1): `2.16.840.1.113883.3.225.2001.143`
* Frailty Diagnosis: `2.16.840.1.113883.3.464.1003.113.12.1074`
* Frailty Symptom: `2.16.840.1.113883.3.464.1003.113.12.1075`
* Advanced Illness: `2.16.840.1.113883.3.464.1003.110.12.1082`
* Dementia Medications: `2.16.840.1.113883.3.464.1003.196.12.1510`

### Long-Term Nursing Home Status

* Housing Status (LOINC: 71802-3): `2.16.840.1.113883.3.225.2001.144`
* Lives in Nursing Home (SNOMED: 160734000): `2.16.840.1.113883.3.464.1003.113.12.1075`

### Palliative Care

* FACIT-Pal Questionnaire (LOINC: 71007-9): `2.16.840.1.113883.3.225.2001.136`
* Palliative Care Diagnosis: `2.16.840.1.113883.3.464.1003.1167`
* Palliative Care Intervention: `2.16.840.1.113883.3.464.1003.198.12.1135`

---

## Numerator Value Set

* Mammography: `2.16.840.1.113883.3.464.1003.108.12.1018`

---

## Functions

### `Global.NormalizeInterval(pointInTime DateTime, period Interval<DateTime>)`

Normalizes a point-in-time or a period into a standard interval.

---
