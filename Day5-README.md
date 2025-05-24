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
