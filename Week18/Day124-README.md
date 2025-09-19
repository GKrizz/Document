# CMS56v13 – Functional Status Assessment for Total Hip Replacement

**Measure ID:** 376
**Employee Name:** Keith Figueroa
**Employee ID:** 2440
📅 **Date:** September 19, 2025 (Day 124)

---

## 🔄 Summary of Changes

### 1. 📆 **Date Range Update**

* **Objective:** Update the date window used to identify qualifying procedures.

* **Previous Logic:**

  ```java
  DateUtils.addDays(request.getMeasurementPeriodStart(), -61)
  ```

  (Corresponds to **November 1** of the year prior)

* **Updated Logic:**

  ```java
  DateUtils.addDays(request.getMeasurementPeriodStart(), -153)
  ```

  (Now corresponds to **August 1** of the year prior)

* **Affected Rule:**
  `rule "Measure 376 - Denominator Exclusionv13 - 11"` in `CMS56v13.drl`

---

### 2. 🧾 **Terminology Update – Value Set**

* **Value Set OID:** `2.16.840.1.113883.3.464.1003.101.12.1089`

* **Change:**

  * **Before:** `"Online Assessments"`
  * **After:** `"Virtual Encounter"`

* **Reason:** To align with the updated terminology while retaining the same OID.

---

## 🧠 Denominator Exclusion Logic

**Purpose:**
Exclude patients from the denominator **if the patient died within 300 days of the Total Hip Arthroplasty (THA) procedure**.

**CQL Concept:**

```cql
exists ( ["Patient Characteristic Expired": "Dead (finding)"] Deceased
  with "Total Hip Arthroplasty Procedure" THAProcedure
    such that date from Deceased.expiredDatetime during
    day of Interval[
      date from start of Global."NormalizeInterval"(THAProcedure.relevantDatetime, THAProcedure.relevantPeriod),
      date from start of Global."NormalizeInterval"(THAProcedure.relevantDatetime, THAProcedure.relevantPeriod) + 300 days
    ]
)
```

---

### 🔍 Rule Breakdown: `Denominator Exclusionv13 - 11`

```java
rule "Measure 376 - Denominator Exclusionv13 - 11"
agenda-group "ecqm_denominator_exclusion"
when
  request: Request($patient: patient)
  Prerequisite($measureList: measureList)
  measure: EMeasure(measure.getId() == 376 && measure.getCmsId().equals("CMS56v13")) from $measureList
  response: Response($measureStatus: measureStatus.get("376"))
  eval($measureStatus.getDenominator() == 1 && $measureStatus.getDenominatorExclusion() == 0)

  procedure: Procedure() from $patient.getProcedureList()
  eval(QDMUtilities.checkCodeAvailablity(measure, QDMCategory.PROCEDURE, "2.16.840.1.113883.3.464.1003.198.12.1006", procedure.getCode(), procedure.getCodeSystemOID()))
  eval(procedure.getStatus() == Status.PERFORMED)
  eval(IntervalUtilities.isDuring(procedure.getStartDate(), DateUtils.addDays(request.getMeasurementPeriodStart(), -426), DateUtils.addDays(request.getMeasurementPeriodStart(), -62)))
  eval(IntervalUtilities.getIntervalByMonths(procedure.getStartDate(), request.getMeasurementPeriodStart()) < 14)

then
  System.out.println("=================== Denominator Exclusionv13 - 11 =======================");
  if ($patient.getDod() != null) {
    if (IntervalUtilities.isDuring($patient.getDod(), procedure.getStartDate(), DateUtils.addDays(procedure.getStartDate(), 300))) {
      System.out.println(">>> Denominator Exclusionv13 - 11 triggered for patient: " + $patient.getPatientId() + ", DOD: " + $patient.getDod());
      $measureStatus.setDenominatorExclusion(1);
    }
  }
  System.out.println("==========================================");
  modify(response) { getMeasureStatus().put("376", $measureStatus) }
end
```

**Logic Summary:**

* THA procedure must exist and be performed.
* Procedure date must fall between:

  * `MeasurementPeriodStart - 426 days`
  * `MeasurementPeriodStart - 62 days`
* Patient must have died within **300 days** post-THA procedure.
* Measurement period date constants were adjusted as part of this update.

---

## 🧾 Database Changes

### 1. **Provider MIPS Configuration**

```sql
INSERT INTO macra_provider_configuration (
  macra_provider_configuration_provider_id,
  macra_provider_configuration_reporting_year,
  macra_provider_configuration_reporting_start,
  macra_provider_configuration_reporting_end,
  macra_provider_configuration_reporting_method,
  macra_provider_configuration_report_type,
  macra_provider_configuration_hardship_exemption,
  macra_provider_configuration_aci_end,
  macra_provider_configuration_aci_start
) VALUES (
  2440, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);
```

### 2. **Quality Measure Mapping**

```sql
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2440, '2025', '376'
);
```

---



## ✅ Final Output & Validation

### ✔️ QRDA III: **PASSED**

<img width="787" height="608" alt="QRDA III Passed" src="https://github.com/user-attachments/assets/7a56a080-80bf-425d-9b63-7fb0429d8a7e" />

---

### ✔️ QRDA I: **PASSED**

<img width="751" height="570" alt="QRDA I Passed" src="https://github.com/user-attachments/assets/e8bf6793-969e-4ec7-8c78-ea6367d5e4da" />

---
