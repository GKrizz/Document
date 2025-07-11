# Day 53 – July 10, 2025 – Thursday

---

## 🔄 Flow of `/getXMLFile`

**Request:**

```http
GET http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/XMLGeneration/getXMLFile
```

**Query Parameters:**

* patientId: 376638
* chartId: 376973
* encounterId: 152839
* templateId: 3355
* formId: 1833
* tabId: 10
* accountId: glace

**Description:**
Fetches XML data for a given patient encounter and template to populate desktop UI.

---

## 🔄 Flow of `/addShortcut`

**Request:**

```http
GET http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/XMLGeneration/addShortcut
```

**Query Parameters:**

* elementId: `xml_element2_PREOPHEADER` (Unique element ID)
* data: `Test111` (Shortcut content)
* tabId: `2622` (Tab identifier)
* dbname: `glace` (used internally, not in controller)

**Process Flow:**

1. Controller method `addShortcut()` receives params.
2. Calls service method `xmlGenerationService.addShortcut()` with elementId, data, tabId.
3. Creates a `SoapElementDatalist` entity with these values.
4. Saves entity using `soapElementDatalistRepository.saveAndFlush()`.
5. Inserts data into `soap_element_datalist` table.
6. Returns success/failure response (EMRResponseBean).

**Database Query to verify:**

```sql
SELECT * FROM soap_element_datalist WHERE soap_element_datalist_tabid = 2622;
```

---

## 🔄 Flow of `/fetchShortCut`

**Request:**

```http
POST http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/Shortcuts.Action/fetchShortcut
```

**Query Parameters:**

* tabId: `2622`
* elementId: `xml_element2_PREOPHEADER`

**Process Flow:**

* Controller method `fetchShortCut()` calls service.
* Service calls repository to fetch shortcuts from `general_shortcut` table.
* Returns matching shortcut string.

**Repository JPQL:**

```sql
SELECT * FROM general_shortcut WHERE general_shortcut_map_group_id = 2622;
```

---

## 🔄 Flow of `/saveXMLTab`

**Process:**

1. Incoming XML data string is split using `"!!!"` delimiter into:

   * Core XML data
   * Update query
   * Insert query
   * Delete query
2. Calls methods:

   * `processUpdateQuery(...)`
   * `processDeleteQuery(...)`
3. Executes custom SQL embedded if marker `#!` is found.
4. Checks `tabLibraryIsxml` flag from `tab_library` table:

   * If `tabType == 2`, treat as patient-level tab:

     * Check if data exists via `getDataCount(patientId, tabLibraryId)`.
     * Update or insert into `leaf_tab_patient`.
   * Else, treat as encounter-level tab:

     * Check data via `getEncounterDataCount(encounterId, tabLibraryId)`.
     * Update or insert accordingly.

**SQL to explore `leaf_tab_patient` data:**

* All patient records:

  ```sql
  SELECT * FROM leaf_tab_patient WHERE leaf_tab_patient_patient_id = 376638;
  ```

* Data by encounter + tab:

  ```sql
  SELECT * FROM leaf_tab_patient
  WHERE leaf_tab_patient_patient_id = 376638
    AND leaf_tab_patient_encounter_id = 152839
    AND leaf_tab_patient_tab_library_id = 2622
    AND leaf_tab_patient_isactive = TRUE;
  ```

* View saved XML data:

  ```sql
  SELECT leaf_tab_patient_data
  FROM leaf_tab_patient
  WHERE leaf_tab_patient_tab_library_id = 2622
    AND leaf_tab_patient_isactive = TRUE;
  ```

---

## 🐞 Issue: Lab Results Not Showing in Referral

**Reported By:** DO
**Patient Account No:** 002353
**Problem:** Lab results are missing in referral documents.
**Screenshots:** Attached (not included here)

**Investigation:**

Legacy and Spring queries differ — Spring uses `NOT IN` clause.

**Query running in legacy system:**

```sql
SELECT
  -1 AS col_0_0_,
  labentries0_.lab_entries_testdetail_id AS col_1_0_,
  labentries0_.lab_entries_test_desc AS col_2_0_,
  -1 AS col_3_0_,
  to_char(timezone('EDT', labentries0_.lab_entries_perf_on), 'MM/dd/yyyy') AS col_4_0_
FROM lab_entries labentries0_
WHERE labentries0_.lab_entries_test_status >= 3
  AND labentries0_.lab_entries_test_status NOT IN (7, 8)
  AND labentries0_.lab_entries_testcategory_type <> 3
  AND labentries0_.lab_entries_testdetail_id NOT IN (
    SELECT hl7unmappe1_.hl7_unmappedresults_testdetail_id
    FROM hl7_unmappedresults hl7unmappe1_
    WHERE hl7unmappe1_.hl7_unmappedresults_patientid = 376638
  )
  AND labentries0_.lab_entries_chartid = 376973;
```

---
