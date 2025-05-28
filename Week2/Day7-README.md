# Day 7 - Task Summary (27-May-2025, Tuesday)

---

## Task 1: Run UI and MVC Projects

### Steps to run projects:

1. **Glaceemr Backend**

   * Run as → Run on Server → Select Tomcat
   * URL: `http://192.168.2.241/glaceemr_backend/`
   * Expected display:

     ```
     Welcome To Glenwood Services !
     IP : 192.168.2.241
     ```

2. **Glaceemr UI**

   * Run as → JWT Development with Jetty → Open `Glaceemr.html`
   * URL: `http://192.168.2.241/glaceemr.html`

3. **Glaceemr Legacy**

   * Run on Tomcat
   * URLs:

     * `http://localhost:8080/GlaceStage/jsp/PostLoginProcess.Action`
     * `http://192.168.2.241/glaceemr.html?homeParam=true&userId=1&loginId=2&contextPath=glace&dbn=glace&sprToken=...&taskIntegration=true&tomcaturl=http://192.168.2.241:8080/GlaceStage&userName=Demodoctor&demoFlag=0#mytask:myTask/chartcenter:ChartCenter/...`

---

## Task 2: Edit Demographics UI - Relationship Dropdown Behavior

### Overview:

* In the **Chart Center UI**, when clicking the three-dot menu ("More") next to a patient name, select **Edit Demographics**.
* The UI shows **Personal Info** (Last Name, First Name, DOB, etc.).
* Clicking **Account Info** shows a popup where you can edit insurance details.
* Inside this popup, there is a **Relationship** dropdown with options like **None, Self, Spouse, Child, Other**.
* The goal: When clicking **Edit**, the dropdown should default to **Self**, and fields like Last Name, First Name, DOB should auto-fill accordingly.

---

### Investigation and Findings:

1. **Patient Search API**
   API call observed when typing patient names:

   ```
   GET http://192.168.2.241/glaceemr_backend/api/desktop/user/PatientSearch.Action/searchByPatientInput?...
   ```

   * Backend processes search input and returns matching patients as JSON.
   * Frontend calls `getpatient()` method to perform this.

2. **Edit Demographics API**
   On editing demographics:

   ```
   GET http://192.168.2.241/glaceemr_backend/api/desktop/user/Demographics/getPatientDetails?patientId=376642
   ```


---

### 3. Inspecting the "Primary Insurance" UI and Related Files

* When you open the **Edit Account Info** popup, you will see the heading **"Primary Insurance"**.
* To locate the relevant code, search for the string **"Primary Insurance"** in the project files.
* This search reveals **two key Java files** involved in rendering and handling this UI:

  1. `patientDemographicsDesktopView.java`

     * This file contains the **main demographics UI**, including the **Account Info** section.
     * When you modified the heading text here, it changed to **"Primary Insurance 111"**.
  2. `InsuranceDesktopView.java`

     * This file manages the **Edit popup for Account Info**.
     * Changes here affected the heading to **"Primary Insurance 222"**.
* From this, it’s clear:

  * The **Account Info main view** belongs to `patientDemographicsDesktopView.java`.
  * The **Edit popup UI and actions** belong to `InsuranceDesktopView.java`.
 
  * Conclusion:
      * Account Info view = `patientDemographicsDesktopView.java`
      * Edit popup = `InsuranceDesktopView.java`
        
* Next, search for the term **"Relationship"** in these files to locate the dropdown code and its handling logic.

---


4. **UI Code : Related to Relationship Dropdown**

   * UI element for Relationship dropdown:

     ```java
     FlowPanel primInsRelationFp = new FlowPanel();
     Label primInsRelationLbl = new Label("Relationship");
     Select primInsRelationSelect = new Select();
     primInsRelationSelect.setWidth("80%");

     // Static option added
     Option relationNoneOption = new Option("0", "None");
     primInsRelationSelect.add(relationNoneOption);

     // Dynamically add options from backend filtered by dropdown ID = 21
     presenter.getDropDownOptionList().stream()
       .filter(bean -> bean != null && bean.getDropDownId() != null && bean.getDropDownId() == 21)
       .forEach(bean -> {
         Option addOption = new Option(bean.getOptionValue(), bean.getOptionText());
         primInsRelationSelect.add(addOption);
       });
     ```

   * UI configurations:

     ```java
     primInsRelationSelect.refresh();
     primInsRelationSelect.setFixedMenuSize(5);
     primInsRelationSelect.setShowTick(true);
     primInsRelationSelect.setSelectOnTab(true);
     ```

   * Setting default value based on backend data:

     ```java
     if (insBean.getPatientInsDetailRelationtosubs() != null) {
         primInsRelationSelect.setValue(insBean.getPatientInsDetailRelationtosubs().toString());
     }
     ```

   * Add Change Handler for Autofill:

     ```java
     primInsRelationSelect.addValueChangeHandler(event -> {
         changeNameAnDOBBasedOnRelation(
             primInsRelationSelect,
             primInsSubDOBDatePicker,
             primInsLastNameTextBox,
             primInsFirstNameTextBox,
             1
         );
     });
     ```

---

### Important Notes:

* The `insBean` object holds insurance details for the patient, including the relationship value.
* If no relationship is set (`insBean.getPatientInsDetailRelationtosubs() == null`), default the dropdown to **Self** (usually option value `"1"`).
* The function `changeNameAnDOBBasedOnRelation(...)` should be called on dropdown value change to autofill **Last Name, First Name, DOB** based on selected relationship.
* **Important:** Just setting the dropdown value is not enough to autofill the fields; you must call the autofill function explicitly after setting the default.

---

### Recommended Code Fix for Default Selection and Autofill:

```java
if (insBean.getPatientInsDetailRelationtosubs() != null) {
    primInsRelationSelect.setValue(insBean.getPatientInsDetailRelationtosubs().toString());
} else {
    primInsRelationSelect.setValue("1");  // Default to "Self"
    changeNameAnDOBBasedOnRelation(
        primInsRelationSelect,
        primInsSubDOBDatePicker,
        primInsLastNameTextBox,
        primInsFirstNameTextBox,
        1
    );
}
```

---

### Summary Table

| Action                       | Expected Behavior                                                       |
| ---------------------------- | ----------------------------------------------------------------------- |
| Open Edit UI                 | Relationship dropdown defaults to "Self" if no existing value           |
| Relationship = "Self"        | Auto-fill Last Name, First Name, DOB fields with patient's own details  |
| Change relationship dropdown | Auto-fill related fields dynamically according to selected relationship |

---
