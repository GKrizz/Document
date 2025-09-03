# CMS149v13 - Dementia: Cognitive Assessment

**Date:** September 1, 2025 (Day 106, Monday)
**Provider:** Cassandra Stone (Employee ID: 2432)

---

## 📋 Measures in Queue

| Measure ID | Measure Name                         |
| ---------- | ------------------------------------ |
| CMS149v13  | Dementia: Cognitive Assessment       |
| CMS159v13  | Depression Remission at 12 Months    |
| CMS128v13  | Antidepressant Medication Management |

---

## 🔗 Measure References

* **CMS149v13 2024 Spec:** [CMS149v12 - 2024](https://static.glaceemr.com/ECQM/2024/CMS149v12.html)
* **CMS149v13 2025 Spec:** [CMS149v13 - 2025](https://static.glaceemr.com/ECQM/2025/CMS149v13.html)
* **eCQM Info API:**
  [Glace Data Gateway - Measure 281 (2025)](https://datagateway.glaceemr.com/DataGatewayMediSpan/eCQMServices/getECQMInfoById?ids=281&reportingYear=2025)

---

## ⚙️ MIPS Provider Configuration

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
  2432, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);
```

---

## 🗺 Quality Measure Mapping

```sql
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2432, '2025', '281'
);
```

---

## 📊 Performance Data Snapshot

| Source    | IPOP | DENOM | NUMER | DENEXCEP |
| --------- | ---- | ----- | ----- | -------- |
| Cypress   | 8    | 8     | 2     | 2        |
| My report | 8    | 8     | 4     | 0        |

---

## 👥 Patient-Level Data

| Patient Name    | First Name | Last Name | NUMER | DENEXCEP | Patient ID |
| --------------- | ---------- | --------- | ----- | -------- | ---------- |
| Cynthia Moran   | Cynthia    | Moran     | Fail  | Pass     | 3276193    |
| Darlene Ramirez | Darlene    | Ramirez   | Fail  | Pass     | 3276194    |

---

## 🛠 API for QDM Validation (Single Patient)

Replace placeholders as needed:

```
http://192.168.2.241:9090/glaceemr_backend/api/emr/glacemonitor/mipsperformance/generateAndValidateQDM?dbname=glace&accountId=glace&patientID=3276167&providerId=2432&reportingYear=2025
```

---

## 🗄 Risk Assessment Queries

### Retrieve risk assessment details for a patient:

```sql
SELECT 
    risk_assessment_id,
    risk_assessment_notdone_type,
    risk_assessment_notdone_description,
    risk_assessment_notdone_code,
    risk_assessment_notdone_code_system
FROM 
    risk_assessment
WHERE 
    risk_assessment_patient_id = 3276128;
```

### Check and update risk assessment status for patients:

```sql
SELECT * FROM risk_assessment WHERE risk_assessment_patient_id = 3276193;

UPDATE risk_assessment SET risk_assessment_status=3 WHERE risk_assessment_id=5125;

SELECT risk_assessment_status FROM risk_assessment WHERE risk_assessment_patient_id = 3276193;

SELECT * FROM risk_assessment WHERE risk_assessment_patient_id = 3276194;

UPDATE risk_assessment SET risk_assessment_status=3 WHERE risk_assessment_id=5126;
```

---

## ⚙️ Careplan Intervention Queries & Updates

### Query interventions for a patient with date range filter

```sql
SELECT *
FROM careplan_intervention
WHERE careplan_intervention_patient_id = 3276213
  AND (
    careplan_intervention_ordered_on BETWEEN '2015-12-31' AND '2025-12-31'
    OR careplan_intervention_performed_on BETWEEN '2015-12-31' AND '2025-12-31'
  );
```

### Update careplan intervention with problem code and code system

```sql
UPDATE careplan_intervention 
SET 
  careplan_intervention_problem_code = '113024001',
  careplan_intervention_problem_code_system = '2.16.840.1.113883.6.96'
WHERE careplan_intervention_id = 8620;
```

> **Note:** Update multiple IDs by using `IN (id1, id2, ...)` if needed.

---
## ✅ QRDA Validation Status

* QRDA III: Passed

 <img width="1288" height="577" alt="image" src="https://github.com/user-attachments/assets/c30a6008-e0ba-4829-a180-b6f4678924bb" />

* QRDA I: Passed
<img width="1289" height="497" alt="image" src="https://github.com/user-attachments/assets/280c6dbf-b27d-4254-be38-dd05d87adf3b" />
