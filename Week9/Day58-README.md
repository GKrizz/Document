
# README: Multi-Fax Number Broadcast Feature

### **Bug ID:** 233445  
### **Time Spent:** 3 Days 0 Hrs 0 Mins  
### **Response Category:** Case Response  

---

### **Description:**

Implementation of a feature to allow broadcasting referrals to **multiple fax numbers** for a referring physician, as requested by Dr. Willis F. Gaffney.

---

### **Problem:**

The existing system allowed only a single fax number per referring doctor, limiting the ability to send referral documents to multiple destinations simultaneously.

---

### **Cause:**

The referral faxing logic, UI configuration, and backend schema were designed for single fax number usage. There was no provision for multi-number input, storage, or fax dispatch.

---

### **Resolution:**

The following changes were implemented:

#### **1. UI Updates (`RefdocCategoryConf.jsp`):**

* Navigation Path: `Configure → General Practice Settings → Referring Physician`
* Enabled input for **multiple fax numbers** using `$$` as the delimiter.
* Added support to **save, edit, and delete** fax entries from the UI.
* Verified DB updates reflect user actions accurately.

#### **2. Backend & Schema (`GetPickerDataModel.java`):**

* Modified schema and query logic to support multiple fax numbers using `string_agg()` and `string_to_array()` functions.
* Formatted fax numbers for display with proper dashes (e.g., `123-456-7890 , 321-654-0987`).
* Ensured compatibility with existing DB logic.

#### **3. Fax Broadcast Logic (`FaxLeafFrameAction.java` & `FaxLeafInformationAction.java`):**

* Updated fax dispatch logic to parse and broadcast faxes to **all listed numbers**.
* Handled both `$$` and comma-separated fax numbers.
* Validated logic for both referring doctor and pharmacy paths using `reffaxNo` and `faxNo`.

---

### **Prevention:**

* Documented schema structure and logic flow for future maintainability.
* Added checks to avoid processing empty or invalid fax entries.
* Recommended test cases and validation steps to QA for future rollouts.

---

### **Internal Status:** Completed  
### **External Status:** Completed  
