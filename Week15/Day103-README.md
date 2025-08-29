# CMS154v13 – Appropriate Treatment for Upper Respiratory Infection (URI)

**Reporting Year:** 2025
**Provider:** Betty Poole (ID: 2431)

---

## 🔄 Specification Changes (2024 → 2025)

### 1. Qualifying Encounters

* **Removed (2025):**

  * Initial Hospital Observation Care
  * Observation
  * Online Assessments

* **Renamed:**

  * Value set `Payer` → `Payer Type`

---

### 2. Diagnosis Logic

* **2024:**
  Exclusion was only `"Competing Conditions for Respiratory Conditions"`

* **2025:**
  Expanded to include unions:

  | Condition                                       | OID                                        |
  | ----------------------------------------------- | ------------------------------------------ |
  | Competing Conditions for Respiratory Conditions | `2.16.840.1.113883.3.464.1003.102.12.1017` |
  | Acute Pharyngitis                               | `2.16.840.1.113883.3.464.1003.102.12.1011` |
  | Acute Tonsillitis                               | `2.16.840.1.113883.3.464.1003.102.12.1012` |

---

### 3. Supplemental Data Elements (SDE)

* **2024:** `Payer`
* **2025:** `Payer Type` *(Terminology aligned with standards)*

---

## ⚙️ Setup

### 1. Provider Configuration

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
  2431, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);
```

### 2. Map Quality Measure

```sql
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2431, '2025', '65'
);
```

---

## 🛠️ Key Issues & Fixes

### ❌ Issue 1: Patients Missing from DENEX

* Patients: `3275977`, `3275970`
* **Cause:** `IntervalUtilities.getIntervalByMillis(...)` strict millisecond comparison excluded valid cases
* **Fix:** Commented millisecond check and replaced with day-based check:

```java
eval(IntervalUtilities.getIntervalByDays(occuranceA_encounter,
    (diagnosis.getStartDate() == null ? diagnosis.getRecordedDate() : diagnosis.getStartDate())) <= 3
 && IntervalUtilities.getIntervalByDays(occuranceA_encounter,
    (diagnosis.getStartDate() == null ? diagnosis.getRecordedDate() : diagnosis.getStartDate())) >= 0)
```

---

### ❌ Issue 2: Extra Patients in Numerator

* Patients: `3275960`, `3275975`
* **Cause:** Numerator rule didn’t properly exclude antibiotic orders within 3 days of URI encounter
* **Fix:** Added explicit suppression rule:

```java
rule "Measure 65 - Numeratorv13 - 1"
    agenda-group "ecqm_numerator"
when
    request: Request($patient: patient)
    ...
    medicationOrder: MedicationOrder() from $patient.getMedicationOrderList()
    eval(QDMUtilities.checkCodeAvailablity(measure, QDMCategory.MEDICATION,
         "2.16.840.1.113883.3.464.1003.1190",
         medicationOrder.getCode(), medicationOrder.getCodeSystemOID()))
    eval(IntervalUtilities.getIntervalByDays(occuranceA_encounter, medicationOrder.getStartDate()) <= 3
     && IntervalUtilities.getIntervalByDays(occuranceA_encounter, medicationOrder.getStartDate()) >= 0)
then
    $measureStatus.setNumerator(0);
    modify(response) { getMeasureStatus().put("65", $measureStatus) }
end
```

---

### ❌ Issue 3: Medication Order Dates Not Parsed

* **Problem:** `orderDateNodeList.item(i).hasChildNodes()` check failed because nodes had attributes but no child nodes.
* **Fix:** Removed `hasChildNodes()` checks. Example:

```java
if (orderDateNodeList.item(i) != null
    && orderDateNodeList.item(i).getAttributes().getNamedItem("value").getNodeValue() != null) {
    medication.setOrderDate(getDateObject(orderDateNodeList.item(i).getAttributes().getNamedItem("value").getNodeValue()));
}
```

---

## ✅ Final Results

| Metric | Expected | Actual | Result  |
| ------ | -------- | ------ | ------- |
| IPP    | 21       | 21     | ✅ Match |
| DENOM  | 21       | 21     | ✅ Match |
| NUMER  | 10       | 10     | ✅ Match |
| DENEX  | 9        | 9      | ✅ Match |

---

## 🔗 Useful Endpoints

* **Get Patients Seen**

  ```
  /api/emr/glacemonitor/mipsperformance/getPatientsSeen?dbname=glace&accountID=glace&mode=3&reportingYear=2025
  ```

* **Calculate MIPS Performance**

  ```
  /api/emr/glacemonitor/mipsperformance/calculateMIPSPerformance?reportingYear=2025&accountID=glace&isMonthlyReport=false&dbname=glace
  ```

* **Generate & Validate QDM for Patient**

  ```
  /api/emr/glacemonitor/mipsperformance/generateAndValidateQDM?dbname=glace&accountId=glace&patientID=<PATIENT_ID>&providerId=2431&reportingYear=2025
  ```

---

## 📝 QRDA Validation

### QRDA III: Passed

![QRDA III Screenshot](https://github.com/user-attachments/assets/d9070ed1-3622-44f8-9391-9a98a7f3ecca)

![QRDA III Screenshot](https://github.com/user-attachments/assets/f94df518-a035-4ffb-844a-0424e7e39dbf)

---

### QRDA I:

*Pending screenshots or notes here*

---
