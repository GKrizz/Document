
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


