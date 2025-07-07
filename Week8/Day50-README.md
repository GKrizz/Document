
## 🔁 `TabDetailAction.performAction()` – Step-by-Step Execution

---

### 🔹 1. **Extract Parameters from Request**

```java
int tabId = Integer.parseInt(HUtil.Nz(request.getParameter("tabId"), "-1"));
...
String isNewLeaf = HUtil.Nz(request.getParameter("isNewLeaf"), "-1");
```

* **Purpose**: Extract values like `tabId`, `patientId`, `formId`, etc. from the **HTTP request**.
* `HUtil.Nz(...)` ensures null-safe parsing.
* `isNewLeaf`: indicates if a new tab instance is being created.

---

### 🔹 2. **Set Request Attribute for SOAP Templates**

```java
request.setAttribute("soapAddTemplateIds", soapAddTemplateIds);
```

* Makes the `soapAddTemplateIds` value available to downstream JSPs or components.

---

### 🔹 3. **Session Handling and DB Setup**

```java
HttpSession session = request.getSession();
...
DataBaseUtils dbUtils = new DataBaseUtils(connectString);
```

* Fetches session values: `userId`, `loginId`, and `connectString`.
* Initializes `dbUtils` for database operations using the connection string.

---

### 🔹 4. **Get Episode ID from Encounter**

```java
episodeId = Integer.parseInt(HUtil.Nz(dbUtils.newTableLookUp(...), "-1"));
```

* Gets the **episode ID** for the encounter from the `encounter` table.

---

### 🔹 5. **Look Up Template ID**

```java
int templateId = Integer.parseInt(HUtil.Nz(dbUtils.newTableLookUp(...), "-1"));
```

* Finds the template ID linked to the `formId` from `leaf_library`.

---

### 🔹 6. **Get User’s Name from emp\_profile (Optional)**

```java
select emp_profile_lname from emp_profile where emp_profile_loginid = ...
```

* Logs who accessed the screen by extracting last name from `emp_profile`.

---

### 🔹 7. **Get Form Type & Leaf Completion Status**

```java
formType = Integer.parseInt(HUtil.Nz(dbUtils.newTableLookUp(...)));
...
isCompleted = Boolean.parseBoolean(...);
```

* `formType = 3`: dynamic/problem-based leaf.
* If `leafId == -1` and `problemId != -1`, it fetches latest active `leaf_patient` record for that problem.
* Sets `isCompleted = true/false` based on leaf completion status.

---

### 🔹 8. **Create Patient Model**

```java
CompletePatientDetailModel patientModel = CompletePatientDetailModel.getAgeGenderInstance(...);
```

* Retrieves patient metadata (e.g., age, gender) for rendering logic.

---

### 🔹 9. **Create Tab Bean (tab metadata)**

```java
TabDetailBean tabBean = TabDetailBean.getNewInstance(tabId, dbUtils, patientModel);
```

* `tabBean` holds metadata like:

  * `isXML`
  * `codifiedLeafType`
  * `tabName`
  * `xslURL`, etc.

---

### 🔹 10. **If Tab is Not XML → Use ActionHandler**

```java
if(tabBean.getIsXML()==0){
    ...
    return action.getNextPage();
}
```

* If tab is **not XML**, it is handled through an **ActionHandler** (older legacy tabs like prescription).
* Skips rest of the flow.

---

### 🔹 11. **Setup TabDetailModel**

```java
TabDetailModel tabDetailModel = new TabDetailModel();
...
tabDetailModel.setRealPath(...);
```

* A helper class to get tab content, possibly XML, clinical data, etc.
* Configured with current encounter, patient, chart, and real path for file access.

---

### 🔹 12. **Add Some Metadata to Request**

```java
request.setAttribute("iscomplete", isCompleted);
request.setAttribute("episodeId", episodeId);
request.setAttribute("isNewLeaf", isNewLeaf);
```

* Metadata used in the JSP or rendering engine.

---

### 🔹 13. **Codified Leaf Type Handling**

```java
int codifiedLeafType = tabBean.getCodifiedLeafType();
request.setAttribute("dualSave", codifiedLeafType);
```

* This **controls which screen/view is returned**.
* Example:

  * `codifiedLeafType = 1`: Clinical elements
  * `codifiedLeafType = 5`: Examination
  * `codifiedLeafType = 0`: Default XML tab (Custom1 tab in your case)

---

### 🔹 14. **Load Previous Encounter Data (if enabled)**

```java
if(loadpreviousdata == 1){
    prevEncounterId = Integer.parseInt(tabDetailModel.getPreviousEncounterId(...));
}
```

* Optional: If the user requests to load data from the **previous visit**, it finds that encounter ID.

---

### 🔹 15. **Codified View Switch (View Dispatcher)**

```java
if(codifiedLeafType==1){
    // Load clinical elements dynamically
    ...
    return "ClinicalElementView.Screen";
}
...
if(codifiedLeafType==11){
    // Load Discharge data
    ...
    return "DischargePatient.Screen";
}
```

* Handles all `codifiedLeafType` values.
* Each one returns a different JSP or "screen" name.
* Skips these if `codifiedLeafType == 0` (which is your case for `Custom1`).

---

### 🔹 16. **Default Case (Non-Codified XML Tab)**

```java
if(openId == -1)
    tabDetail = tabDetailModel.getTabDetails(...);
else
    tabDetail = tabDetailModel.getTabDetailsForPortal(...);
```

* For normal UI, loads tab details via `getTabDetails()`.

  * This loads:

    * Patient-specific XML from `leaf_tab_patient`
    * Or default from XML template (`PreOpHeader.xml`)
    * Populates with patient data (via `XMLGenerator`)
* For patient portal (openId ≠ -1), a different loader is used.

---

### 🔹 17. **Final Setup and Return**

```java
request.setAttribute("tabDetail", tabDetail);
request.setAttribute("iscomplete", isCompleted);
request.setAttribute("UserName", fullName);
return "TabDetail.Screen";
```

* Sets the final **tab content** into request attributes.
* Returns the **generic XML-based tab renderer**: `"TabDetail.Screen"`

---

## 🔁 Flow Summary

```plaintext
START
 ↓
Read request parameters (tabId, formId, patientId, etc.)
 ↓
Initialize DB and session objects
 ↓
Look up episodeId, templateId, formType, etc.
 ↓
Check tabBean: is it XML or codified?
   ↓ No
   → Use ActionHandler
   ↓ Yes
   → codifiedLeafType?
       → 1 to 11 → specific clinical screens
       → 0 (Custom1) → use getTabDetails()
 ↓
Fetch tabDetail XML from DB or file
 ↓
Populate it via XMLGenerator
 ↓
Set tabDetail in request
 ↓
RETURN "TabDetail.Screen"
```

---

## ✅ Key Takeaways

* **Custom1 (tabId 932)** goes through the **default XML tab path** (`codifiedLeafType == 0`).
* The XML comes from `leaf_tab_patient` or file (`PreOpHeader.xml`).
* Final screen returned is: `"TabDetail.Screen"`
* Rendering happens via: `TabDetail.jsp` + `XSL` + `XML`



---

### 🔧 **Method Signature**

```java
public static CompletePatientDetailModel getAgeGenderInstance(int xPatientId, DataBaseUtils dbUtils) throws Exception
```

* **Static Method**: Can be called without instantiating the class.
* **Parameters**:

  * `xPatientId`: The ID of the patient to look up.
  * `dbUtils`: Utility object used to perform database operations.

---

### 🛠️ **Step-by-Step Code Explanation**

---

### 1. **Declare Variables**

```java
StringBuffer qryStr = new StringBuffer();
CompletePatientDetailModel mymodel = new CompletePatientDetailModel();
```

* `qryStr`: SQL query builder for cleaner string concatenation.
* `mymodel`: Object that will be filled with patient details and returned.

---

### 2. **Build the SQL Query**

```java
qryStr.append(" select patient_registration_id as hsp001, patient_registration_last_name as hsp002, ...");
```

* Fetches **complete demographic info** of a patient from `patient_registration` table.
* Fields include:

  * **Name, DOB, sex, address, phone, employer, etc.**
  * `coalesce(patient_registration_sex, 0)` makes sure nulls are handled.
  * `to_mmddyyyy(...)`: likely converts DB date to a standard US format.
  * `extract(hour from patient_registration_dobtime)`: grabs DOB time info.

---

### 3. **Execute Query**

```java
Vector patientDetailVector = dbUtils.executeQueryToVector(qryStr.toString());
```

* Executes the SQL query and returns results as a **vector of vectors** (each row = one vector).

---

### 4. **Process Result**

```java
if(patientDetailVector.size() > 0) {
    Vector patientVector = (Vector)patientDetailVector.get(0);
```

* Check if data exists.
* Extract the **first row** (should be just one result per patient ID).

---

### 5. **Populate the Model**

Each of the following fields is parsed from the `patientVector` (index matches SQL order):

| Model Field                           | Description                               | Source Index                                 | Notes                                  |
| ------------------------------------- | ----------------------------------------- | -------------------------------------------- | -------------------------------------- |
| `patientId`                           | Unique ID of patient                      | `0`                                          | Cast to int                            |
| `lastName`, `firstName`, `midInitial` | Name fields                               | `1`, `3`, `2`                                |                                        |
| `accountNo`, `ssn`                    | Account and social security               | `4`, `5`                                     |                                        |
| `dob`, `dobtime`                      | Date and time of birth                    | `6`, `22`                                    | Default to current date if null        |
| `sex`                                 | Gender as integer (e.g. 1=Male, 2=Female) | `7`                                          |                                        |
| `sexString`                           | Gender as string (e.g. Male/Female)       | Lookup from `billing_config_table` using sex |                                        |
| `address1`, `address2`                | Address lines                             | `8`, `9`                                     |                                        |
| `city`, `state`, `zip`                | Location details                          | `10`, `11`, `12`                             | `state` is looked up from config table |
| `phoneNo`, `workNo`                   | Formatted phone numbers                   | `13`, `14`                                   | Formatting via `HUtil.PhoneNo`         |
| `allergies`                           | Any existing allergy notes                | `15`                                         |                                        |
| `maritalstatus`                       | Integer code for marital status           | `16`                                         |                                        |
| `race`, `ethnicity`                   | Demographic details                       | `17`, `18`                                   |                                        |
| `driverLicenseNo`                     | Driving license number                    | `19`                                         |                                        |
| `mailid`, `employer`                  | Email and employer                        | `20`, `21`                                   |                                        |

---

### 6. **Exception Handling**

```java
catch(Exception e) {
    // Silently caught (but should log it!)
}
```

* Catches any exceptions, but **doesn't log** them. This is not ideal.

  * You should at least log using `e.printStackTrace()` or a logger.

---

### 7. **Return the Model**

```java
return mymodel;
```

* Always returns an instance of `CompletePatientDetailModel`.
* If no data was found, it will return a mostly empty object.

---

## ✅ Summary of Purpose

The method:

* Fetches **demographic and profile information** for a given `patientId`.
* Constructs a `CompletePatientDetailModel` object with:

  * Formatted, validated, and enriched data
  * Derived values (e.g., `sexString`, `dobtime`)
* Is used by other modules (like `TabDetailAction`) to populate UI and XML tabs with **patient context**.

---

## 🔄 Where It Fits in the Flow

In `TabDetailAction.java`:

```java
CompletePatientDetailModel patientModel = CompletePatientDetailModel.getAgeGenderInstance(patientId, dbUtils);
```

This `patientModel` is later passed to:

* `TabDetailBean.getNewInstance(...)`
* `GenerateElementXML(...)`
* Various rendering methods that need patient info.

---
## 🔍 Method Overview

```java
public static TabDetailBean getNewInstance(int tabId, DataBaseUtils dbUtils, PatientDetailModel patientDetailModel)
```

This method **creates and returns a `TabDetailBean`** for a given `tabId`, populated from the **`tab_library`** table and, if XML-based, enriched with **XML-related configurations** from the **`leaf_xml_version`** and `leaf_version` tables.

---

## 🧱 Step-by-Step Breakdown

### 🔹 Step 1: Initialize

```java
TabDetailBean tabBean = new TabDetailBean();
ResultSet rst = null;
StringBuffer qry = new StringBuffer();
```

* Creates a new `tabBean` instance.
* Prepares a result set (`rst`) and a query buffer (`qry`) to hold SQL.

---

### 🔹 Step 2: Load Basic Tab Info

```java
qry.append("SELECT ... FROM tab_library WHERE tab_library_id = ").append(tabId);
```

This SQL query fetches the basic details about the tab, including:

| Field in DB               | Field in Bean      | Description                    |
| ------------------------- | ------------------ | ------------------------------ |
| `tab_library_displayname` | `tabName`          | Name of the tab                |
| `tab_library_description` | `description`      | Description                    |
| `tab_library_actionurl`   | `actionURL`        | Custom action if not XML-based |
| `tab_library_summaryurl`  | `summaryURL`       | Summary page                   |
| `tab_library_isactive`    | `isActive`         | Whether the tab is active      |
| `tab_library_isxml`       | `isXML`            | Whether tab is XML-based       |
| `tab_library_xml_group`   | `xmlGroup`         | Age/Gender/Normal based        |
| `tab_library_iscodified`  | `codifiedLeafType` | Type of codified layout        |

---

### 🔹 Step 3: Map DB Values to Bean

```java
tabBean.setTabName(rst.getString("displayname"));
...
tabBean.setCodifiedLeafType(rst.getInt("newleafdesign"));
```

* **Populates `tabBean`** with data from `tab_library`.
* `codifiedLeafType` determines which screen to return (e.g., clinical, vital, discharge, etc.).

---

### 🔹 Step 4: Exit Early if Not XML Tab

```java
if(tabBean.getIsXML() == 0)
    return tabBean;
```

* If tab is **not XML-based**, it doesn't need to fetch `leaf_xml_version` info.
* So it **returns early** with just the base data.

---

### 🔹 Step 5: XML Tab → Decide Which Version to Load

Now, it loads **extra XML metadata** based on the tab's configuration:

```java
int patientAgeParameter = 0;
StringBuffer qry1 = new StringBuffer();
```

Then logic branches depending on `tabBean.getXmlGroup()`:

---

### 🧭 (a) Normal XML Group

```java
if(tabBean.getXmlGroup() == TabConstants.NORMAL_GROUP)
```

→ Load standard XML/XSL for this tab.

```sql
SELECT xmlurl, xslurl, condition, ...
FROM leaf_xml_version
LEFT JOIN leaf_version ON ...
WHERE leaf_version_tab_id = ...
```

---

### 🧭 (b) Gender-Based XML Group

```java
else if(tabBean.getXmlGroup() == TabConstants.SEX_BASED)
```

→ Load based on `patientDetailModel.getSexString()` (like `Male`, `Female`)

```sql
... WHERE leaf_xml_version_printxslurl ILIKE 'Male-Male'
```

`Male-Male` is used as a **matching key** in the `printxslurl` field.

---

### 🧭 (c) Age-Based XML Group

```java
else {
    if(xmlGroup == AGE_BASED_ON_MONTH)
        patientAgeParameter = getAgeInMonths();
    else if(xmlGroup == AGE_BASED_ON_YEAR)
        patientAgeParameter = getAgeInYears();
```

This fetches the **patient's age** and compares against **ranges** in DB:

```sql
... WHERE
length(printxslurl) > 0 AND
startAge <= patientAgeParameter AND
patientAgeParameter < endAge AND
leaf_version_tab_id = ...
```

Where:

* `startAge` = substring before `-`
* `endAge` = substring after `-`

So it picks the correct **age group-specific XSL/XML** version.

---

### 🔹 Step 6: Map XML Version to Bean

If matching version found, assign values to `tabBean`:

```java
tabBean.setXmlURL(rst.getString("xmlurl"));
tabBean.setXslURL(rst.getString("xslurl"));
tabBean.setPrintingURL(rst.getString("printxslurl"));
tabBean.setVersionName(rst.getString("versionname"));
tabBean.setCondition(rst.getString("condition"));
tabBean.setTabType(rst.getInt("tabtype"));
```

---

### 🔚 Step 7: Return Final `tabBean`

```java
return tabBean;
```

Now the `tabBean` has all necessary info:

* Name, description, isXML
* Codified type
* XML + XSL URLs
* Age/gender specific selection
* Version name, tab type

---

## ✅ Summary of Flow

| Step | Description                                                 |
| ---- | ----------------------------------------------------------- |
| 1    | Fetch tab's core info from `tab_library`                    |
| 2    | If not XML → return immediately                             |
| 3    | Else → check tab group: Normal / Gender / Age               |
| 4    | Based on that → build and execute SQL to `leaf_xml_version` |
| 5    | Load version-specific URLs and configs                      |
| 6    | Return populated `TabDetailBean`                            |

---
### ✅ Execution Flow Summary (What Executes Next)

### 1. **Tab is not codified (`codifiedLeafType == 0`)**

So, all these `if (codifiedLeafType == X)` branches are **skipped**:

```java
if(codifiedLeafType == 1) { ... }
if(codifiedLeafType == 2) { ... }
...
if(codifiedLeafType == 11) { ... }
```

---

### 2. **openId == -1**

Means the tab is **not opened via portal**. So this block runs:

```java
tabDetail = tabDetailModel.getTabDetails(request, dbUtils, tabBean, tabId, patientModel, templateId);
```

---

### 3. **Inside `getTabDetails()`**:

This method:

* Fetches XML **from the DB** if `leaf_tab_patient_data` has a record for this tab.
* Otherwise, falls back to default XML template from disk (`tabBean.getXmlURL()`).

---

## 📄 Code Walkthrough (Up to `int tabIndex = -1`)

```java
package com.glenwood.hcare.actions.chart.newleafmodel;
```

* Declares the package containing tab handling logic for the new leaf model system.

---

```java
import ...
```

* Brings in required classes for:

  * **File I/O**: `BufferedReader`, `FileReader`
  * **Database**: `ResultSet`, `DataBaseUtils`
  * **Collections**: `Hashtable`, `Vector`
  * **Utilities**: `Pattern`, `Matcher`, `HUtil`, `XMLGenerator`
  * **Logging** and **Error Handling**

---

```java
public class TabDetailModel {
```

* This class encapsulates tab rendering logic, particularly for XML-based custom tabs.

---

### 🔧 Class-level variables:

```java
int encounterId;
int patientId;
int chartId;
int callType;
String pcareImageOCXVersion;
String realPath;
public String ENCODING = "UTF-8";
```

These are initialized before rendering starts:

* `encounterId`, `patientId`, `chartId`: context of the tab call
* `callType`: how the tab is being rendered (screen, print, etc.)
* `pcareImageOCXVersion`: viewer control version (for imaging, etc.)
* `realPath`: absolute path to resources on server (used to locate XML/XSL)
* `ENCODING`: character encoding used to read XML files

---

### 🔍 Now, inside `getTabDetails()`:

```java
public Vector<Hashtable<String, Object>> getTabDetails(HttpServletRequest request, DataBaseUtils dbUtils, TabDetailBean tabBean, int tabId, PatientDetailModel patientModel, int templateId) throws Exception {
```

* This method is called **when the tab is NOT codified**, i.e., `codifiedLeafType == 0`.
* Its goal is to return a `Vector<Hashtable>` which contains XML, XSL, and tab metadata, ready to be used in the JSP.

---

### 🛠 Local variables declared:

```java
String xmlFileName = new String();
String xslFileName = new String();
String xmlData = new String();
String originalXMLData = new String();
Vector<Hashtable<String, Object>> tabData = new Vector<Hashtable<String, Object>>();
XMLGenerator generator;
int tabIndex = -1;
```

| Variable          | Purpose                                                                 |
| ----------------- | ----------------------------------------------------------------------- |
| `xmlFileName`     | Full path to XML template to use (if not coming from DB)                |
| `xslFileName`     | Full path to XSL transformation file                                    |
| `xmlData`         | XML content (after cleaning/processing)                                 |
| `originalXMLData` | Raw XML content from DB or file (used for backup or audit)              |
| `tabData`         | Final return object — holds XML, XSL, and metadata as `Hashtable`       |
| `generator`       | Used to parse/transform XML content (`XMLGenerator` is likely a helper) |
| `tabIndex`        | Index of the current tab (may be used in grouping or navigation logic)  |

---

## 📌 What's Next in the Method?

After this point, typical logic will be:

1. **Check for patient-specific XML in DB** (`leaf_tab_patient_data`)
2. If found → load and parse XML from DB
   Else → load **default XML** from file (`tabBean.getXmlURL()` from disk)
3. Clean XML: remove illegal characters
4. Parse with `XMLGenerator` (which injects patient data, conditions, etc.)
5. Add to `tabData` as `Hashtable` with keys like:

   * `"xmldata"`, `"xslurl"`, `"tabdesc"`, `"originalxmldata"`

Then, it returns:

```java
return tabData;
```

---


Let's break down these **three lines** and explain their **purpose and use in the `getTabDetails()` method**, step by step:

---

### ✅ Line 1:

```java
Vector<Hashtable<String, Object>> tabData = new Vector<Hashtable<String, Object>>();
```

#### ✅ What it is:

* A **Vector** (synchronized list) that holds multiple **Hashtables**.
* Each `Hashtable<String, Object>` represents **a single tab’s data**, including:

  * The **XML content**
  * The **XSL file URL**
  * **Tab metadata** like description, version, etc.

#### ✅ Why it's used:

* The final **output of the method** is this `tabData`:

  ```java
  return tabData;
  ```
* This data will be placed in the request:

  ```java
  request.setAttribute("tabDetail", tabData);
  ```
* And rendered by `TabDetail.jsp`.

#### ✅ Example content of a `Hashtable`:

```java
{
  "tabdesc": "PREOPHEADER",
  "xmldata": "<tab>...processed XML here...</tab>",
  "xslurl": "/GlaceMaster/xsl/CustomTabs/preopheader.xsl",
  "originalxmldata": "<tab>...raw xml here...</tab>"
}
```

---

### ✅ Line 2:

```java
XMLGenerator generator;
```

#### ✅ What it is:

* A reference to the `XMLGenerator` class — likely a **utility** that:

  * **Parses raw XML**
  * **Applies patient/clinical data**
  * **Handles conditionals and macros**

#### ✅ Why it's used:

* Later in this method, you’ll see something like:

  ```java
  generator = new XMLGenerator(...);
  xmlData = generator.getProcessedXML(...);
  ```
* It transforms the raw XML (from DB or file) into **dynamic XML**, ready for rendering.

---

### ✅ Line 3:

```java
int tabIndex = -1;
```

#### ✅ What it is:

* A tab index number. Default is `-1` (i.e., uninitialized).

#### ✅ Why it's used:

* Could be used for:

  * Tracking **tab position** if there are multiple tabs in a group.
  * Managing **multi-tab rendering**, **navigation**, or **template versions**.

---

### 🔁 Summary of Roles

| Variable    | Role                                                               |
| ----------- | ------------------------------------------------------------------ |
| `tabData`   | Final output container — holds structured data for tab rendering   |
| `generator` | Used to parse and transform XML using rules/macros/patient context |
| `tabIndex`  | Placeholder for indexing — may or may not be used in final logic   |

---
