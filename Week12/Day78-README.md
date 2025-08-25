

# 📘 Day 78 – IM Progress Notes (August 4, 2025)

## 📝 Issue Summary

* **Module**: IM Progress Notes
* **View**: Male – SOAP View
* **Problem**: **PE (Physical Exam) data not showing** in modern view.

---

## 🔍 Investigation Steps

### ✅ 1. Print `htmlContent` (Legacy vs Modern)

* Issue isolated to **modern view**: PE data (IMPENARRATIVE) not rendering correctly.

---

## 🔁 2. API & SQL Debugging

### 📌 SQL Queries Used

#### 🔸 Check `leaf_tab_patient` by Encounter ID

```sql
SELECT 
  leaf_tab_patient_id,
  leaf_tab_patient_tab_library_id,
  leaf_tab_patient_encounter_id,
  leaf_tab_patient_isactive
FROM leaf_tab_patient 
WHERE leaf_tab_patient_encounter_id = 153319;
```

#### 🔸 Confirm Active Entry for `tab_library_id = 484`

```sql
SELECT * 
FROM leaf_tab_patient
WHERE leaf_tab_patient_tab_library_id = 484
  AND leaf_tab_patient_encounter_id = 153319
  AND (leaf_tab_patient_isactive = 't' OR leaf_tab_patient_isactive IS NULL);
```

#### 🔸 Extract XML Content

```sql
SELECT xpath('/leaf', leaf_tab_patient_data::xml)
FROM leaf_tab_patient
WHERE leaf_tab_patient_tab_library_id = 484
  AND leaf_tab_patient_encounter_id = 153319
  AND (leaf_tab_patient_isactive = 't' OR leaf_tab_patient_isactive IS NULL);
```

---

## 📡 Modern View API Interactions

### 🔄 Save PE Tab (IMPENARRATIVE)

**POST Request**

```
POST http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/XMLGeneration/saveXMLTab
```

**Query Params**

```json
{
  "patientId": "376952",
  "chartId": "377219",
  "encounterId": "153319",
  "templateId": "3355",
  "leafId": "57905",
  "libraryId": "1833",
  "tabName": "IMPENARRATIVE",
  "tabLibraryId": "484",
  "problemId": "null"
}
```

**Request Body**

```json
{
  "htmlData": null,
  "tabDesc": null,
  "tabLibraryId": null,
  "xmlData": "<leaf version=\"IM_PE_Narrative(Male-Male)_3.0\"><xml_element3_IMPENARRATIVE heading=\"\">1</xml_element3_IMPENARRATIVE></leaf>!!! !!! !!!",
  "xslData": null
}
```

**Response**

```json
{
  "login": null,
  "success": true,
  "data": "success"
}
```

---

### 📄 Template Configuration for PE Tab

**GET Request**

```
GET http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/ROSElements/templateConf
```

**Query Params**

```json
{
  "patientId": "376952",
  "encounterId": "153319",
  "leafId": "57905",
  "templateId": "3355"
}
```

**Response**

```json
{
  "data": "[{\"tabid\":\"11\",\"isxml\":\"1\",\"tabname\":\"Custom2\",\"leaftabid\":\"484\",\"xmltype\":\"1\",\"taborder\":\"0\",\"tabtype\":\"-1\",\"iscodified\":\"0\",\"leaftabdesc\":\"[#text: IMPENARRATIVE]\"}]"
}
```

---

### 📉 Final API Call – `getsoaptemplate` (Data Not Rendering)

**GET Request**

```
GET http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/incompletecharts/getsoaptemplate
```

**Query Params**

```json
{
  "patientId": "376952",
  "encounterId": "153319",
  "leafId": "-1",
  "soapPatientNotesIsActive": "false",
  "formId": "1833",
  "patientAssessmentsIsActive": "false",
  "patAllergChartId": "0",
  "clientId": "-1",
  "excludeNeeded": "false",
  "chartId": "377219",
  "isNewLeaf": "-1",
  "importTemplateId": "-1",
  "userId": "1",
  "isGWT": "-1",
  "tabId": "484",
  "isExclude": "false",
  "templateId": "3355",
  "callType": "2",
  "tabType": "-1",
  "counter": "0",
  "loginUserId": "2"
}
```

**Response**

```json
{
  "data": "[{\"countVal\":0,\"htmlData\":\"\"}]"
}
```

---

## 📌 Summary

| Item                 | Status             |
| -------------------- | ------------------ |
| XML Saved            | ✅ Success          |
| Tab Configuration    | ✅ Validated        |
| SOAP Template Output | ❌ Empty `htmlData` |

---

## 🛠️ Next Steps

* [ ] Review **XSL rendering logic** for PE tab (Tab ID: 484).
* [ ] Ensure **htmlData** is being generated post XML save.
* [ ] Check for any missing **template mappings** or **XSL associations** for modern SOAP view.

---
