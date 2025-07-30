# DoctorIncident - Data not saved

---

## Overview

This document explains how to retrieve and handle data from the `DoctorIncident` leaf, stored in the `leaf_tab_patient` table as XML data.

* **Leaf Name:** `DoctorIncident`
* **Filename:** `DoctorIncident.xml`
* **Version:** `DoctorIncident_1.0`

This leaf contains a mix of **static elements** (e.g., doctor name, section titles) and **dynamic elements** (e.g., patient info, comments) stored in XML.

---

## HTTP Request Context

Example HTTP GET request used to access leaf data:

```
GET http://localhost:8080/GlaceMaster/jsp/chart/leafmodel/TabDetail.Action
Parameters:
  encounterId = 153271
  patientId = 376714
  chartId = 377063
  leafId = 57836
  formId = 2779
  problemId = -1
  tabId = 695
  fromSoap = Soap
  parentTabId = 11
  loadpreviousdata = -1
  isNewLeaf = 0
  soapAddTemplateIds = ""
Remote address: 127.0.0.1:8080
```

---

## Database Table

* **Table:** `leaf_tab_patient`
* **Relevant Columns:**

  * `leaf_tab_patient_patient_id` (patient id)
  * `leaf_tab_patient_encounter_id` (encounter id)
  * `leaf_tab_patient_tab_library_id` (tab/library id)
  * `leaf_tab_patient_isactive` (active flag)
  * `leaf_tab_patient_data` (XML data)

---

## Sample Query: Fetch Raw XML Data

```sql
SELECT leaf_tab_patient_data
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376714
  AND leaf_tab_patient_encounter_id = 153271
  AND leaf_tab_patient_tab_library_id = 695
  AND leaf_tab_patient_isactive = 't';
```

---

## Extracting XML Elements Using XPath

Because the leaf XML contains multiple elements, you can extract individual pieces of data using XPath in PostgreSQL.

**Note:** Static elements (`xml_element1`, `xml_element2`) may not be saved in the XML stored in the DB; they often come from the original template.

---

### Example: Extract all relevant elements

```sql
SELECT
  (xpath('/leaf/xml_element1/text()', leaf_tab_patient_data::xml))[1]::text AS xml_element1,
  (xpath('/leaf/xml_element2/text()', leaf_tab_patient_data::xml))[1]::text AS xml_element2,
  (xpath('/leaf/xml_element3/text()', leaf_tab_patient_data::xml))[1]::text AS xml_element3,
  (xpath('/leaf/xml_element4/text()', leaf_tab_patient_data::xml))[1]::text AS name,
  (xpath('/leaf/xml_element5/text()', leaf_tab_patient_data::xml))[1]::text AS account_no,
  (xpath('/leaf/xml_element6/text()', leaf_tab_patient_data::xml))[1]::text AS dob,
  (xpath('/leaf/xml_element7/text()', leaf_tab_patient_data::xml))[1]::text AS age,
  (xpath('/leaf/xml_element8/text()', leaf_tab_patient_data::xml))[1]::text AS father_name,
  (xpath('/leaf/xml_element9/text()', leaf_tab_patient_data::xml))[1]::text AS mother_name,
  (xpath('/leaf/xml_element10/text()', leaf_tab_patient_data::xml))[1]::text AS primary_ins,
  (xpath('/leaf/xml_element11/text()', leaf_tab_patient_data::xml))[1]::text AS secondary_ins,
  (xpath('/leaf/xml_element13/text()', leaf_tab_patient_data::xml))[1]::text AS comments
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376714
  AND leaf_tab_patient_encounter_id = 153271
  AND leaf_tab_patient_tab_library_id = 695
  AND leaf_tab_patient_isactive = 't';
```

---


## XML Element Summary

| Element Name   | Description                  | Data Type | Source       |
| -------------- | ---------------------------- | --------- | ------------ |
| xml\_element1  | Static: Doctor name          | Static    | Template XML |
| xml\_element2  | Static: Section title        | Static    | Template XML |
| xml\_element3  | Divider/styling element      | Static    | Template XML |
| xml\_element4  | Patient Name                 | Dynamic   | DB XML       |
| xml\_element5  | Account Number               | Dynamic   | DB XML       |
| xml\_element6  | Date of Birth                | Dynamic   | DB XML       |
| xml\_element7  | Age                          | Dynamic   | DB XML       |
| xml\_element8  | Father’s Name                | Dynamic   | DB XML       |
| xml\_element9  | Mother’s Name                | Dynamic   | DB XML       |
| xml\_element10 | Primary Insurance            | Dynamic   | DB XML       |
| xml\_element11 | Secondary Insurance          | Dynamic   | DB XML       |
| xml\_element13 | Comments (textarea)          | Dynamic   | DB XML       |
| xml\_element14 | Scratch Area (OCX component) | Dynamic   | DB XML       |

---

