# 🧠 Daily Progress Log

## 📅 Day 5 — 24/05/2025

### 🥇 Task: Identify Table Storing Patient Insurance Details

---

### 🔍 Investigation Steps

1. **Starting Point**

   * On UI page for patient insurance details:

     ```
     http://localhost:8080/GlaceStage/jsp/patient/PatientRegistrationActionNew.Action?patientId=376815&mode=2&isPopup=1
     ```
   * Patient insurance fields visible on this page.

2. **Trigger Save Action**

   * Clicked **Save** button to trace backend flow.
   * On clicking Save, URL changes to:

     ```
     http://localhost:8080/GlaceStage/jsp/patient/patientregistrationsave.Action?saveFromClickScan=0&currentInsType=0&isPopup=1&isClose=0&isSaveValue=10
     ```
   * JavaScript file triggered:

     ```
     patientregistration1.js
     ```
   * Action handler on server:

     ```
     PatientRegistrationActionNew
     ```
   * Save icon has:

     ```html
     onclick="Javascript:saveData(0);"
     ```

3. **Analyzing `patientregistration1.js`**

   * **`saveData(isClose)`**

     * Validates patient data before save
     * Handles name changes, consent, deceased validations, audit logging, multi-form validation, related updates
   * **`saveNow(isClose)`**

     * For existing patient (`patientId != -1`): updates services and submits form
     * For new patient: checks duplicates and creates new patient
   * **`swapInsurance()`**

     * Parses and swaps primary and secondary insurance details on the form
   * **`swapThirdInsurance()`**

     * Switches primary and other insurance details
   * **`saveRelativesAddressAndInsinfo()`**

     * Collects relatives’ IDs and address/contact info
     * Sends AJAX POST requests with data to `PatientRegistrationActionNew.Action` with different modes (52 and 56) for saving info
   * **Insurance Update Flags Checked:**

     * `priinstemp` (primary insurance)
     * `secinstemp` (secondary insurance)
     * `othinstemp` (other insurance)

4. **Database Updates**

   * For each insurance type flagged for update:

     * Deactivates previous active insurance records by setting `isactive=false`
     * Inserts new insurance detail records into:

       ```
       patient_ins_detail
       ```
   * Multiple patient IDs handled (e.g., for relatives)

---

### ✅ **Key Finding**

* The **patient insurance details** are stored in the database table:
  **`patient_ins_detail`**
* Database environment:

  ```
  ciama_nextrelease
  ```

---

### Summary Table

| Step                   | Description                                                                |
| ---------------------- | -------------------------------------------------------------------------- |
| UI Page                | Patient Registration with Insurance Fields                                 |
| Save Trigger           | `saveData(0)` in `patientregistration1.js`                                 |
| Backend Action         | `PatientRegistrationActionNew`                                             |
| Insurance Update Logic | Flags checked, existing active insurance deactivated, new details inserted |
| Database Table         | `patient_ins_detail` (in `ciama_nextrelease` DB)                           |

---



## `patientregistration1.js` logic:

### ✅ **1. Current Page and UI Element**

* **Page URL:**
  
  ```
  http://localhost:8080/GlaceStage/jsp/patient/PatientRegistrationActionNew.Action?patientId=376815&mode=2&isPopup=1
  ```
* **Observed:**
  Patient insurance field table in the UI.

---

### ✅ **2. Save Action Behavior**

* On clicking **Save**, the URL changes to:
  
  ```
  http://localhost:8080/GlaceStage/jsp/patient/patientregistrationsave.Action?saveFromClickScan=0&currentInsType=0&isPopup=1&isClose=0&isSaveValue=10
  ```

* This triggers:

  * `patientregistration1.js`
  * The controller: `PatientRegistrationActionNew`
  * Redirects to:
    ```
    http://localhost:8080/GlaceStage/jsp/patient/PatientRegistrationActionNew.Action?saveFromClickScan=0&currentInsType=0&mode=2&patientId=376815&prevInsName=0&prevInsId=0&backcount=3&flagforpending=0&secondaryserviceflag=0&otherserviceflag=0&isPopup=1&isClose=0&isguarantorupdate=0&isSaveValue=10&saveFlag=true`
    ```

---

### ✅ **3. JavaScript File: `patientregistration1.js` – Logic Breakdown**

| **#** | **Function/Variable**                                         | **Purpose**                                                                |
| ----- | ------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1     | `patientvalidations`                                          | Validates form fields before saving.                                       |
| 2     | `logfields`                                                   | List of patient fields, likely for tracking/logging.                       |
| 3     | `opfields`                                                    | Fields specific to Out-Patient registration.                               |
| 4     | `win`                                                         | Reference to a new/popup browser window.                                   |
| 5     | `frm`                                                         | Holds the HTML form (`frm`).                                               |
| 6     | `datetobox`                                                   | Likely used for date picker elements.                                      |
| 7     | `checkIfInteger(cntrl)`                                       | Clears field if value is not an integer.                                   |
| 8     | `prevZipvalue`                                                | Manages ZIP code formatting.                                               |
| 9     | `isValidZip`                                                  | Validates ZIP code.                                                        |
| 10    | `checkIfNumber(cntrl)`                                        | Ensures the field contains a valid number.                                 |
| 11    | `copyaddress(address, city, state, zip)`                      | Copies full address to clipboard.                                          |
| 12    | `setRateCombo(obj)`                                           | Sets related dropdown (CostCombo) based on selected insurance option.      |
| 13    | `pasteaddress(addresscntrl, citycntrl, statecntrl, zipcntrl)` | Pastes address from clipboard to fields.                                   |
| 14    | `openPatientDemographics()`                                   | Opens new window with patient demographics info.                           |
| —     | `PatientDemoGraphic.jsp`                                      | JSP for displaying formatted patient + insurance info from backend models. |
| 15    | `validatephonenumbers()`                                      | Validates format of phone numbers in the form.                             |
| 16    | `checkphnumber()`                                             | Ensures phone number parts (3-3-4 digits) are correct.                     |
| 17    | `changeToFocused(cntrl)`                                      | Changes background to LightYellow on focus (except excluded fields).       |
| 18    | `fillGuarantor(...)`                                          | Auto-fills guarantor details; adjusts fields if self-guarantor.            |
| 19    | `toggleGuarantor()`                                           | Enables/disables guarantor section based on "Self Guarantor" checkbox.     |
| 20    | `closing()`                                                   | Prevents form/page from closing without saving data.                       |
| 21    | `changeToNormal()`                                            | Resets field background on blur; revalidates data.                         |
| 22    | `openGuarantor()`                                             | Opens popup for guarantor registration.                                    |
| 23    | `addRelations()`                                              | Opens popup to add patient relationships.                                  |
| 24    | *Pending functions*                                           | (To be analyzed in next review phase)                                      |

---
