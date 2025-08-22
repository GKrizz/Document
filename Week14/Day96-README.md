# 📘 MIPS Provider Configuration & CMS2v14 Validation (Reference)

This document provides reference steps, SQL scripts, API details, and test payloads for **MIPS provider configuration** and **CMS2v14 (Depression Screening and Follow-Up Plan) validation** in **GlaceEMR**.

---

## 🔹 Portal & Config URLs

* **Cypress URL:**
  `http://cypress.glaceemr.com:444/`
  **Username:** `sam@glenwoodsystems.com`
  **Password:** `Glenwood100*`

* **Employee Config Page:**
  `http://localhost:8080/Glace/jsp/configure/EmployeeList.jsp`

---

## 🔹 SQL Setup

### 1. Configure Provider for MIPS

```sql
insert into macra_provider_configuration (
  macra_provider_configuration_provider_id,
  macra_provider_configuration_reporting_year,
  macra_provider_configuration_reporting_start,
  macra_provider_configuration_reporting_end,
  macra_provider_configuration_reporting_method,
  macra_provider_configuration_report_type,
  macra_provider_configuration_hardship_exemption,
  macra_provider_configuration_aci_end,
  macra_provider_configuration_aci_start
) values (
  2417, 2025, '2025-01-01', '2025-12-31',
  2, 2, 'f', '2025-12-31', '2025-01-01'
);
```

### 2. Map Provider to Measure (CMS2v14 → ID: 134)

```sql
insert into quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) values (
  2417, '2025', '134'
);
```

⚠️ **Note:** In the test request, `providerId=2419` is used. Make sure provider IDs match between configuration and API call.

---

## 🔹 API Endpoints

### Generate & Validate QDM

```
http://192.168.2.45:7080/glaceemr_backend/api/emr/glacemonitor/mipsperformance/generateAndValidateQDM?dbname=glace&accountId=glace&patientID=3274625&providerId=2419&reportingYear=2025
```

### Hub Service

```
http://192.168.2.45:8080/glacecds/ECQMServices/validateECQM
```

---

## 🔹 Request JSON (Test Patient)

```json
{
  "accountId": "glace",
  "reportingYear": 2025,
  "startDate": 1735669800000,
  "endDate": 1767119400000,
  "measureIds": [134],
  "patient": {
    "patientId": 3274625,
    "firstName": "",
    "lastName": "",
    "dob": 1293301800000,
    "dod": null,
    "gender": "F",
    "race": "Black or African American",
    "ethnicity": "",
    "encounterList": [
      {
        "qdmId": "760870",
        "code": "99203",
        "codeSystemOID": "2.16.840.1.113883.6.12",
        "startDate": 1740796200000,
        "endDate": 1740799800000,
        "visitType": 1
      }
    ],
    "riskAssessmentList": [
      {
        "qdmId": "4978",
        "code": "73831-0",
        "codeSystemOID": "2.16.840.1.113883.6.1",
        "startDate": 1740796200000,
        "endDate": 1740796200000,
        "status": 2,
        "resultCode": "428181000124104",
        "resultCodeSystemOID": "2.16.840.1.113883.6.96"
      }
    ],
    "interventionList": [
      {
        "qdmId": "7908",
        "code": "108313002",
        "codeSystemOID": "2.16.840.1.113883.6.96",
        "description": "Follow Up for Adolescent Depression",
        "startDate": 1740798000000,
        "endDate": 1740798000000,
        "status": 2
      }
    ]
  }
}
```

---

## 🔹 Response JSON (Validation Result)

```json
{
  "accountId": "glace",
  "patientId": 3274625,
  "measureStatus": {
    "134": {
      "measureId": 134,
      "cmsId": "CMS2v14",
      "criteria": 1,
      "ipp": 1,
      "denominator": 1,
      "denominatorExclusion": 0,
      "numerator": 1,
      "numeratorExclusion": 0,
      "denominatorException": 0,
      "measurePopulation": 0,
      "measurePopulationExclusion": 0,
      "measureObservation": 0.0,
      "ruleState": 1
    }
  }
}
```

---

## 🔹 Interpretation of Result

* **IPP (Initial Population):** ✅ 1 → Patient qualifies (encounter + age/gender criteria).
* **Denominator:** ✅ 1 → Depression screening performed.
* **Numerator:** ✅ 1 → Follow-up plan documented.
* **Exclusions/Exceptions:** 🚫 None.

👉 Patient **meets CMS2v14 measure requirements**.

---

