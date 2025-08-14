
### **CMS125v13 eCQM Components for Rules**

**1. Initial Population (IP)**

* Female patients
* Age **52–74** at the end of measurement period
* Must have at least **one qualifying adult outpatient encounter** during the measurement period:

  * Office Visit, Annual Wellness Visit, Preventive Care, Home Healthcare, Virtual, Telephone Visits

**2. Denominator**

* Equals Initial Population

**3. Denominator Exclusions (DenEx)**

* Hospice care anytime in measurement period
* Bilateral mastectomy (history or procedure)
* Patients **≥66** with **frailty + advanced illness/dementia meds**
* Patients **≥66** living long term in nursing home
* Patients receiving palliative care during measurement period

**4. Numerator**

* Women with **≥1 mammogram** between **October 1 two years prior to measurement period** and end of measurement period

**5. Numerator Exclusions / Denominator Exceptions / Stratification**

* None

---

### **Drools Rules Structure**

We will have separate rules for:

1. **Initial Population**

   * Check sex, age, and qualifying encounter.

2. **Denominator Exclusions**

   * Hospice
   * Bilateral mastectomy (left/right)
   * Age ≥66 with frailty + advanced illness/dementia meds
   * Age ≥66 in nursing home
   * Palliative care

3. **Numerator**

   * Mammogram in 27-month window

---



 
| Encounter Description                                        | OID                                      |
| ------------------------------------------------------------ | ---------------------------------------- |
| Office Visit                                                 | 2.16.840.1.113883.3.464.1003.101.12.1001 |
| Annual Wellness Visit                                        | 2.16.840.1.113883.3.526.3.1240           |
| Preventive Care Services Established Office Visit, 18 and Up | 2.16.840.1.113883.3.464.1003.101.12.1025 |
| Preventive Care Services Initial Office Visit, 18 and Up     | 2.16.840.1.113883.3.464.1003.101.12.1023 |
| Home Healthcare Services                                     | 2.16.840.1.113883.3.464.1003.101.12.1016 |
| Virtual Encounter                                            | 2.16.840.1.113883.3.464.1003.101.12.1089 |
| Telephone Visits                                             | 2.16.840.1.113883.3.464.1003.101.12.1080 |

 

| Clinical Concept                         | OID / Code System                                              | Description / Usage                                         |
| ---------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------- |
| **Encounter Inpatient**                  | `2.16.840.1.113883.3.666.5.307`                                | Value Set for inpatient encounters                          |
| Discharge Disposition - Home Hospice     | `2.16.840.1.113883.3.117.1.7.1.209`                            | Code for discharged to home for hospice care                |
| Discharge Disposition - Facility Hospice | `2.16.840.1.113883.3.117.1.7.1.207`                            | Code for discharged to healthcare facility for hospice care |
| **Hospice Care Ambulatory**              | `2.16.840.1.113883.3.526.3.1584`                               | Value set for hospice care interventions                    |
| **Hospice Encounter**                    | `2.16.840.1.113883.3.464.1003.1003`                            | Value set for hospice encounters                            |
| **Hospice care \[Minimum Data Set]**     | LOINC Code: `45755-6` (OID `2.16.840.1.113883.3.225.2001.142`) | Assessment code for hospice care                            |
| "Yes" qualifier value for result         | `2.16.840.1.113883.3.225.2001.123`                             | Code indicating "Yes" result qualifier                      |
| **Hospice Diagnosis**                    | `2.16.840.1.113883.3.464.1003.1165`                            | Value set for hospice diagnoses                             |
