# Flow of Working — Saving Data on Tab Click (e.g., Plan Tab)

---

### 1. **User Opens Plan Tab**

* The application loads the Plan tab UI.
* If existing data is present for this patient + encounter + tab, it loads XML from the backend (`leaf_tab_patient.leaf_tab_patient_data`) and renders it on the tab.

---

### 2. **User Edits Plan Tab Data**

* The user makes changes inside the Plan tab form.
* The form data is internally kept as XML, which matches the expected schema (like `<leaf version="Plan_1.0">...</leaf>`).

---

### 3. **User Clicks Save (or Navigates Away)**

* **Frontend triggers save**:

  * Either by clicking a Save button,
  * Or automatically on tab navigation or some event (like `onStop` in the view or a JS message),
* The function `saveData()` is called,
* This calls `saveXml()`.

---

### 4. **Frontend Serializes XML Data**

* Inside `saveXml()`, the current form/tab data is serialized into an XML string using `collectXML(presenter.getTabDesc(), true)`.
* This XML string contains all the user-entered information in the correct format.

---

### 5. **Frontend Prepares Request**

* The XML string is wrapped into an `XMLBean` object (`xmlBean.setXmlData(leafdata)`).
* `saveXMLTab(XMLBean)` method is called with this data.

---

### 6. **Frontend Calls Backend API**

* The frontend calls the REST endpoint:

```
POST /XMLGeneration/saveXMLTab?patientId=...&chartId=...&encounterId=...&templateId=...&leafId=...&libraryId=...&tabName=...&tabLibraryId=...&problemId=...
```

* It sends the XML data inside the request body as part of the `XMLBean`.

---

### 7. **Backend Receives Request**

* The method `saveXmlFile()` is invoked with all parameters + XML data.
* This calls the core function `saveXMLTab()`.

---

### 8. **Backend Processes Data**

* The XML string is sanitized and parsed.
* Backend checks if a record exists in the `leaf_tab_patient` table for this patient, encounter, and tab.
* If exists, **UPDATE** the existing row's `leaf_tab_patient_data` and update audit info.
* If not, **INSERT** a new row with all data.

---

### 9. **Database Update**

* The `leaf_tab_patient` table ends up with the latest saved XML data in `leaf_tab_patient_data`.
* Audit columns like `h489008` (modified by user) and `h489009` (modified time) are updated.
* The record is marked active (`leaf_tab_patient_isactive = true`).

---

### 10. **Backend Responds**

* Returns `"success"` on successful save.
* Or throws an error on failure.

---

### 11. **Frontend Receives Response**

* If success:

  * Shows a success notification "Data saved".
* If failure:

  * Shows error notification "Failed to save data".

---

### 12. **User Feedback**

* User sees the notification confirming the save.
* Data remains saved and can be reloaded anytime from the backend.

---

# Summary Diagram

```plaintext
User edits tab → saveData() → saveXml() → serialize XML → call API with XML
         ↓                               ↑
Backend receives request → saveXMLTab() → DB update leaf_tab_patient
         ↓                               ↑
Returns success/failure ← frontend shows notification
```

---

### **Key Points:**

* Data is saved per patient, encounter, and tab (`leaf_tab_patient_patient_id`, `leaf_tab_patient_encounter_id`, `leaf_tab_patient_tab_library_id`).
* XML format is essential for data storage and retrieval.
* Audit columns track who and when the data was modified.
* The process is triggered either by explicit save or navigation events.

---



## Frontend Save Flow (Based on your code)

### 1. **User edits data in a tab**

* The tab UI holds XML data for that tab (e.g., Plan tab).

---

### 2. **User triggers Save**

* This happens either:

  * Explicitly, by clicking a Save button (calls `saveData()`),
  * Or implicitly on navigation away from the tab (`onStop()` calls `saveXml()`),
  * Or via external JS message `window.postMessage('Save')` triggering `saveData()`.

---

### 3. **Saving XML data**

* `saveData()` calls `saveXml()`:

```java
public void saveData(){
    saveXml();
}

public void saveXml() {
    String leafdata = collectXML(presenter.getTabDesc(), true);
    XMLBean xmlBean = new XMLBean();
    xmlBean.setXmlData(leafdata);  // leafdata is the XML string representing tab content
    presenter.saveXMLTab(xmlBean);
}
```

* `collectXML()` presumably serializes the current tab form data into the XML string format your backend expects.

---

### 4. **Calling backend API**

* `presenter.saveXMLTab(XMLBean fulldata)` calls:

```java
public void saveXMLTab(XMLBean fulldata) {
    xmlClient.saveXmlFile(
        getChartVariablesBean().getPatientId(),
        getChartVariablesBean().getChartId(),
        getChartVariablesBean().getEncounterId(),
        getChartVariablesBean().getTemplateId(),
        getChartVariablesBean().getLeafId(),
        getChartVariablesBean().getFormId(),
        getTabDesc(),
        getTabLibId(),
        getActionServletURL().getProblemId(),
        fulldata,
        new JsonCallback() {
            @Override
            public void onSuccess(Method method, JSONValue response) {
                NotificationAlerts.notificationSuccess("Data saved");
            }
            @Override
            public void onFailure(Method method, Throwable exception) {
                NotificationAlerts.notificationError("Failed to save data");
            }
        }
    );
}
```

* This calls the backend endpoint

```
/XMLGeneration/saveXMLTab?patientId=...&chartId=...&encounterId=...&templateId=...&leafId=...&libraryId=...&tabName=...&tabLibraryId=...&problemId=...
```

* passing the XML data in the request body (inside `XMLBean.xmlData`).

---

### 5. **Backend `saveXmlFile` method**

* Receives all those params + the XML data,
* Calls your Java method `saveXMLTab` which:

  * Parses and sanitizes the XML,
  * Checks if an entry already exists for patient/tab/encounter,
  * Updates or inserts the data into `leaf_tab_patient.leaf_tab_patient_data`,
  * Updates audit columns,
  * Returns `"success"`.

---

### 6. **Frontend success/failure handling**

* On successful save, shows notification "Data saved".
* On failure, shows "Failed to save data".

---

## Summary:

| Action                          | Method/Function                              |
| ------------------------------- | -------------------------------------------- |
| Collect XML from tab            | `collectXML(presenter.getTabDesc(), true)`   |
| Create XMLBean with XML data    | `XMLBean.setXmlData(leafdata)`               |
| Call backend save API           | `xmlClient.saveXmlFile(...)`                 |
| Backend receives and saves data | `saveXmlFile(...)` → `saveXMLTab(...)`       |
| Notify frontend on success/fail | `JsonCallback.onSuccess()` or `.onFailure()` |

---

### **So when you click the Plan tab and save:**

* The tab XML data is serialized,
* Sent with patient/encounter/tab info to backend,
* Backend saves or updates the `leaf_tab_patient` row with XML in `leaf_tab_patient_data`,
* Frontend gets success notification.

---
