### **🗓️ Day 43 – June 30, 2025 (Monday)**

---

### **🔧 Configuration Page Bug Fixes:**

* ✅ Fixed a bug related to handling **phone numbers** on the configuration page.
* 🖨️ Client requested an option to **add, update, and delete multiple fax numbers** in the database, which was successfully implemented.

---

### **👨‍⚕️ Referring Physician Search Picker Updates**

**📋 Description:**
Working on the **Referring Physician Search Picker**, understanding how it displays data when a user clicks the **\[Search ReferringDoctor]** button in the UI.

**🔍 Steps Involved:**

#### 1. **🖱️ User Action:**

* User clicks on **Referring Physician \[Search ReferringDoctor]**.

#### 2. **🖥️ Popup/Modal Picker:**

* A **modal dialog** or **popup** window is triggered, displaying a list of referring doctors (using `<div>` styled like a window or iframe).

#### 3. **🔄 Backend Call:**

* Clicking **Search Referring Physician** triggers a **POST request** to:

  ```
  http://localhost:8080/GlaceStage/jsp/chart/leafs/CommonPicker.Action
  ```

* Payload for the POST request:

  | **Key**                        | **Purpose**                                                             |
  | ------------------------------ | ----------------------------------------------------------------------- |
  | `"Mode": "8"`                  | Identifies the picker type, "Mode 8" refers to **Referring Physician**. |
  | `"Offset": "0"`                | Used for **pagination**, starting from record 0.                        |
  | `"limit": "10"`                | Fetches **10 records** at a time for pagination.                        |
  | `"Group": "-1"`                | Likely refers to **"All groups"** or no group filtering.                |
  | `"selectedValue": ""`          | Empty, as no value is preselected.                                      |
  | `"new": "1"`                   | Marks this as a **new picker request**.                                 |
  | `"userGroupsRequired": "true"` | Ensures **user access control/groups** are respected.                   |
  | `"dataRequired": "true"`       | Ensures that the **data** is fetched, not just metadata.                |
  | `"GlaceAjaxRequest": "true"`   | Tells the backend it’s an **AJAX request**, not a page reload.          |

#### 4. **📊 Data Display Logic:**

* The backend returns a **JSON list** of referring physicians.
* **Picker.js** loops through the records and renders each as a row in a table inside the modal/popup.
* Each row contains a **\[Select]** button.
* Upon clicking the **\[Select]** button, the **assignElements(respObj)** function is triggered, which populates the main form with selected physician details (phone, fax, address, etc.).

#### 5. **🔄 Action Mapping:**

* The **CommonPicker.Action** is routed to the **PickerAction.java** Java class, which handles the request.

#### 6. **🔍 Database Query:**

* The **GetPickerDataModel.java** file fetches data from the database using the following SQL query:

  ```sql
  select 
      coalesce(referring_doctor_phoneno,'') as col3,
      coalesce(referring_doctor_fax_number,'') as col2,
      referring_doctor_uniqueid as id
  ```

#### 7. **💬 Handling Multiple Phone/Fax Numbers:**

* Multiple phone and fax numbers are stored as `$$`-delimited strings, e.g.,:

  * `referring_doctor_phoneno = 4137321119$$5555555555$$3333333333`
  * `referring_doctor_fax_number = 6666666666$$3333444555`

* **To display** all phone and fax numbers in the picker:

  * Modify SQL logic to replace `$$` with a **pipe (`|`)** in the display.

  ```sql
  replace(coalesce(referring_doctor_fax_number,''), '$$', ' | ') as col2,
  replace(coalesce(referring_doctor_phoneno,''), '$$', ' | ') as col3
  ```

#### 8. **🔁 Flow of Data:**

* **User Action:** Clicks **Search Referring Physician**.
* **Browser:** Calls **showCommonPicker(8)**.
* **Picker.js:** Builds **Picker UI**.
* **Picker.js:** Sends **POST** to **PickerAction.java** with `"Mode: 8"`.
* **PickerAction.java:** Calls **getReferralDoctorDetails()** in **GetPickerDataModel.java**.
* **GetPickerDataModel.java:** Executes SQL query to fetch physician details from the DB.
* **PickerAction.java:** Returns **JSON response**.
* **Picker.js:** Renders rows in the **modal picker**.

---

### **📠 Fax Referral Page Analysis**

**📋 Description:**
Analyzing how **fax** and **referral numbers** are passed in the system.

#### 1. **Referral.jsp – faxReferral() Function:**

* Triggered when the user clicks **"Fax Referral"**.
* If the user confirms to save changes:

  * Sets **`printaftersave = 2`** (indicating a fax will be sent after saving).
  * Triggers the save logic.
* If the user cancels:

  * Checks if a referral exists (`refuniqueId != -1`), and if yes, opens the **fax window**.

#### 2. **openfaxpage() Function:**

* Collects **fax number**, **doctor name**, **patient files**, and **preview content**.
* Opens a new window with a **POST request** to **FaxLeafFrame.Action**, passing all necessary data.

#### 3. **🔄 Action Mapping:**

* **FaxLeafFrame.Action** maps to the backend class **FaxLeafFrameAction.java**.
* The screen/view **FaxLeafFrame.Screen** is rendered by **FaxLeafFrame.jsp**.

#### 4. **FaxLeafFrameAction.java:**

* Receives **POST** request from **Referral.jsp**.
* Processes parameters (e.g., **patientId**, **encounterId**, **refId**).
* Prepares a URL for fax with necessary parameters.
* Returns the view string: `return "FaxLeafFrame.Screen"`.

#### 5. **FaxLeafInformationAction.java:**

* Reads request parameters like **patientId**, **faxId**, **formId**, etc.
* Retrieves necessary information (referring doctor details, fax numbers, etc.).
* Sets attributes and forwards to **FaxLeafInformation.jsp**.

#### 6. **💬 Handling Multiple Fax Numbers:**

* **reffaxNo** is passed as a string of multiple fax numbers, e.g., `"1234567890|0987654321|1122334455"`.
* These are parsed into a list in **FaxLeafInformationAction.java**:

  ```java
  if (!reffaxNo.trim().equals("-1")) {
	    String[] splitFaxNos = reffaxNo.split(",");
	    for (String fax : splitFaxNos) {
	        fax = fax.trim();
	        if (!fax.isEmpty()) {
	            toList.put(fax, refdocName);
	        }
	    }
	}	
  ```

#### 7. **✅ Test Fax Number:**

* Tested with fax number **8884001837**.
* The system processed the fax correctly, passing the correct number to the backend and rendering it in the UI.

---

### **🔧 Issue Fixed:**

* The issue where **multiple fax numbers** weren’t properly displayed or passed was fixed. Now, **all fax numbers** are processed and displayed correctly. ✅

### **Fixed Images:**

Below are the screenshots that demonstrate the fixed issue:

![Screenshot 1](https://github.com/user-attachments/assets/f885a0f6-7c7c-410b-a462-b1cdc9ef9f76)

![Screenshot 2](https://github.com/user-attachments/assets/2dd3270a-c26e-48e9-b607-d9be5c92434e)

![Screenshot 3](https://github.com/user-attachments/assets/f0f1c086-1515-45dd-943c-3640c1a19e98)




