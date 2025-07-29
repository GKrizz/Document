Based on the detailed logs and code you've shared, here's a **clear flow of how saving works in the XML tab (like GENERALPLAN)**, and **why your "Plan data not saved" issue** is occurring.

---

## ✅ FLOW: What Happens When You Click **Save / Save & Close**

### 🔁 From `TopBarDesktopView.java`

```java
saveTemplate(Boolean close, boolean isFwd)
```

* Constructs a `ClinicalNoteAttributes` object.
* Calls:

```java
presenter.saveTemplate(clinicalNoteAttributesForSave, true, completedChart, close, isFwd);
```

---

### 🧠 Inside `TopBarActivity.java::saveTemplate()`

```java
saveCurrentTab(); // This is KEY
```

* It detects current tab via:

```java
Place place = getSlottedController().getCurrentPlace(TopBarPlace.Top_Bar);
```

* If current place is `XMLPlace`, then:

```java
eventBus.fireEvent(new TopBarEvent(TopBarEvent.saveTemplateTab, 100));
```

---

### 🧲 `XMLActivity.java` Binds the Event

When XML tab is active:

```java
eventbus.addHandler(TopBarEvent.TYPE, new TopBarEventHandler() {
   public void onFireTopBarEvent(TopBarEvent event) {
       if (event.getMode() == TopBarEvent.saveTemplateTab && event.getTemplateTabId() == 100) {
           view.saveXml(); // --> This is where XML actually starts saving
       }
   }
});
```

---

### 💾 `XMLDesktopView.java::saveXml()`

* Calls:

```java
String leafdata = collectXML(presenter.getTabDesc(), true);
presenter.saveXMLTab(xmlBean); // Calls into XMLActivity
```

---

### 🚀 `XMLActivity.java::saveXMLTab()`

* Calls backend:

```java
xmlClient.saveXmlFile(...) // with all necessary metadata
```

* Backend endpoint: `saveXMLTab?patientId=...&chartId=...`

---

## ❌ ISSUE: Plan Data Not Saved — Root Cause?

From your logs:

```
Error calling drawGENERALPLAN: (ReferenceError) : LoadBasicUI is not defined
```

### 🔎 This is a **critical failure** in JS:

* `drawGENERALPLAN()` invokes `LoadBasicUI.setEventsToElement(...)`
* But `LoadBasicUI` is **not defined** — likely because the JS file containing it is **not injected or loaded**.

---

## 🔧 RESULTING FLOW IMPACT:

| Step                                         | Status                             |
| -------------------------------------------- | ---------------------------------- |
| XML loaded                                   | ✅                                  |
| GENERALPLAN drawn                            | ⚠️ partially failed                |
| JS events (keyup, focus, etc.)               | ❌ Not attached                     |
| `collectXML(...)` picks up updated user data | ❌ Fails silently or gets old value |
| Save API triggered                           | ✅ But with **old or blank data**   |
| Notification says "Saved"                    | ✅ (misleading)                     |
| Actual content not persisted                 | ❌ Confirmed problem                |

---

## ✅ SOLUTION

```js
// LoadBasicUI.setEventsToElement(document.getElementById("referringdoctortext"));
// LoadBasicUI.setEventsToElement(document.getElementById("reftxtarea"));
```

is a **valid workaround** when `LoadBasicUI` is **not loaded or not defined**, and here's **why it's effective**:

---

## ✅ What This Fix Does

Those two lines were trying to attach event handlers using a JS utility (`LoadBasicUI`) that doesn’t exist in the current page context.

### So by commenting them:

* You **avoid a fatal JS runtime error** (`LoadBasicUI is not defined`).
* The rest of the `drawDataInDiv()` function **executes normally**, rendering the UI and setting values.
* The key event handlers (`onkeyup`, `onchange`) already exist inline in HTML:

  ```html
  onkeyup='ReferringDoctor(id,event);makeDirty();'
  onchange='transferdata()'
  ```

  So interaction and data transfer still work correctly **without LoadBasicUI**.

---

## 🧠 Why This Bug Was Breaking Save

When `LoadBasicUI` is undefined:

* The script throws an exception at the very end of `drawDataInDiv()`
* Which causes the referring doctor input and notes **not to render fully**
* So when `collectXML()` runs later, it **finds incomplete DOM** → empty data gets saved

Your fix prevents this JS crash, allowing the DOM to finish rendering and input fields to populate → now `collectXML()` correctly captures the values.

---

## 🔒 When Is It Safe to Leave This Fix In?

✅ Leave those two lines commented out **permanently** IF:

* `LoadBasicUI.setEventsToElement()` is never defined in your current JS bundle
* You don’t depend on any extra focus/styling/validation logic that `LoadBasicUI` might add

If you're **not seeing any behavior regressions**, it’s fine to leave them out.

---

## 🔁 Summary Flow

```text
saveTemplate() → TopBarActivity → saveCurrentTab() 
→ fires TopBarEvent with tabId 100 
→ XMLActivity handles it and calls view.saveXml() 
→ XMLDesktopView → collectXML() → saveXMLTab() → saveXmlFile() 
→ JS failure (LoadBasicUI missing) → incomplete DOM events → no real data saved.
```

---

## 🔍 PSQL --> Query Breakdown

### ✅ 1. **Raw XML Data Retrieval**

```sql
SELECT leaf_tab_patient_data 
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376798
  AND leaf_tab_patient_encounter_id = 153255
  AND leaf_tab_patient_tab_library_id = 210
  AND leaf_tab_patient_isactive = 't';
```

* **Purpose**: Returns the raw XML stored for this patient and encounter.
* **Use case**: Inspect structure before applying `xpath()`.

---

### ✅ 2. **Top-Level Node Extraction**

```sql
SELECT xpath('/leaf', leaf_tab_patient_data::xml)
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376798
  AND leaf_tab_patient_encounter_id = 153255
  AND leaf_tab_patient_tab_library_id = 210
  AND leaf_tab_patient_isactive = 't';
```

* **Purpose**: Extracts the top-level `<leaf>` node from the XML.
* **Use case**: Verifies if root node is `<leaf>` and accessible.

---

### ✅ 3. **Plan Data Extraction from Specific Node**

```sql
SELECT xpath('/leaf/xml_element2_GENERALPLAN/text()', leaf_tab_patient_data::xml)
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376798
  AND leaf_tab_patient_encounter_id = 153255
  AND leaf_tab_patient_tab_library_id = 210
  AND leaf_tab_patient_isactive = 't';
```

* **Purpose**: Extracts the text value inside `<xml_element2_GENERALPLAN>` under `<leaf>`.
* **Output**: Returns a PostgreSQL array with the extracted text (or empty if not found).

---

### ✅ 4. **Raw XML for Different Encounter**

```sql
SELECT leaf_tab_patient_data
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376671
  AND leaf_tab_patient_encounter_id = 153264
  AND leaf_tab_patient_tab_library_id = 210
  AND leaf_tab_patient_isactive = 't';
```

* **Purpose**: Same as Query 1, for a different encounter.
* **Use case**: View raw XML to identify nodes like `xml_element2_GENERALPLAN`.

---

### ✅ 5. **Extract Plan and Aftercare Instructions Simultaneously**

```sql
SELECT 
  xpath('//xml_element2_GENERALPLAN/text()', leaf_tab_patient_data::xml) AS plan,
  xpath('//xml_element4_GENERALPLAN/text()', leaf_tab_patient_data::xml) AS aftercare_instructions
FROM leaf_tab_patient
WHERE leaf_tab_patient_patient_id = 376835
  AND leaf_tab_patient_encounter_id = 152987
  AND leaf_tab_patient_tab_library_id = 210
  AND leaf_tab_patient_isactive = 't';
```

* **Purpose**: Extracts both `<xml_element2_GENERALPLAN>` and `<xml_element4_GENERALPLAN>` text content.
* `//` = anywhere in the document (not just directly under `<leaf>`)
* **Output**:

  * `plan`: contains the plan text
  * `aftercare_instructions`: contains aftercare info

---



