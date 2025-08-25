# Cypress QRDA-III Testing with Glace EHR System

## 🔗 Cypress Access

* **URL:** [http://cypress.glaceemr.com:444/](http://cypress.glaceemr.com:444/)
* **Username:** `sam@glenwoodsystems.com`
* **Password:** `Glenwood100*`

---

## 🧭 Navigation Steps

1. Login to Cypress using the credentials above.
2. Click:

   * **Glenwood Systems** → **Glace 2025** → **C2 + C3 (QRDA-III)**
3. You will see the following option:

   > *"Test the EHR system's ability to import and calculate (C2) and submit (C3) measure-based data."*
4. Choose a **CMS ID** or **Measure Name**
   Example: `CMS125v13 - Breast Cancer Screening`

---

## 👤 Registering the Provider

> **Note:** UI registration access may be restricted. You may need to bypass access validation.

### 🔹 Step 1: Open the Employee Registration Page

Open the following URL in your browser:

```
http://localhost:8080/Glace/jsp/configure/EmployeeList.jsp
```

---

### 🔹 Step 2: Bypass Access Restriction (if needed)

Modify `EmployeeList.jsp` and **comment out** the following block to bypass the restriction:

```jsp
<%-- 
	if(!<%=INSTALL_LOGIN_USER_ACCESS%>){
		if(!existeduser){
			alert("Access denied. Please Contact Glenwood Administrator");
			return false;
		}
	} 
--%>
```

---

### 🔹 Step 3: Register the Employee

Fill out the required fields and register the provider manually.

---

### 🔹 Step 4: **Back Up the Employee ID**

After registering, make sure to **note down the Employee ID** for future use.

> Example: `2428`

You can verify or search for the registered employee using this URL:

```
http://localhost:8080/GlaceStageNew/jsp/configure/EmployeeIndex.Action?empType=0&lastname=&firstname=&staffid=SteveCollier&username=
```

Use this **Employee ID** later during patient import verification (e.g., via the `getPatientsSeen` API).

---

## 🛠️ Configure MIPS in PostgreSQL

> **Database:** `macra_test`

Run the following SQL queries to configure MIPS for the provider:

```sql
-- Insert provider MIPS configuration
INSERT INTO macra_provider_configuration (
  macra_provider_configuration_provider_id,
  macra_provider_configuration_reporting_year,
  macra_provider_configuration_reporting_start,
  macra_provider_configuration_reporting_end,
  macra_provider_configuration_reporting_method,
  macra_provider_configuration_report_type,
  macra_provider_configuration_hardship_exemption,
  macra_provider_configuration_aci_end,
  macra_provider_configuration_aci_start
) VALUES (
  2417, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);

-- Map Quality Measure
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2417, '2025', '134'
);
```

---

## 📦 Import QRDA-III File

1. Download the **ZIP file** from Cypress.

   ![Download ZIP](https://github.com/user-attachments/assets/4fc9ead3-ca7e-4d50-b22a-ed5943f213f3)

2. Import it into the **QRDA** section under **General Practice** in the Glace system.

   ![Import QRDA](https://github.com/user-attachments/assets/decf9341-652c-44d6-a611-928c4695bec8)

3. **If you encounter a permission error during the import**, run the following commands to grant the required file permissions:

   ```bash
   sudo chmod -Rf 777 /home/software/Documents/shared
   sudo chmod -Rf 777 /home/software/Documents/shared/QRDA/
   ```

   > These commands recursively give full read/write/execute permissions to the shared and QRDA directories.

---

## 🔍 Post-Import: Patient Verification, Cleanup & Re-Import

### ✅ Step 1: Verify Patient Data

After importing the QRDA ZIP file, use the following **1st API** to verify whether patient data has been successfully inserted:

```http
GET http://192.168.2.241:9090/glaceemr_backend/api/emr/glacemonitor/mipsperformance/getPatientsSeen?dbname=glace&accountID=glace&mode=3&reportingYear=2025
```

---

### 🧑‍⚕️ Step 2: Lookup Employee ID (Optional Filter)

If you want to verify data for a specific provider (e.g., `Steve Collier`), search using their **staff ID**:

```http
GET http://localhost:8080/GlaceStageNew/jsp/configure/EmployeeIndex.Action?empType=0&lastname=&firstname=&staffid=SteveCollier&username=
```

> Example Employee ID: `2428`

Use this ID to filter results from the `getPatientsSeen` API if needed.

---

### ⚠️ Step 3: Clean Up Duplicate Entries (If Needed)

If **duplicate patient data** is found after import, delete the related entries from all relevant tables using the following SQL script.

> **Note:** Replace `3249153` with the actual `patient_id` you want to clean up.

```sql
DELETE FROM lab_entries 
WHERE lab_entries_chartid IN (
  SELECT chart_id FROM chart WHERE chart_patientid IN (3249153)
);

DELETE FROM lab_entries_parameter 
WHERE lab_entries_parameter_chartid IN (
  SELECT chart_id FROM chart WHERE chart_patientid IN (3249153)
);

DELETE FROM patient_assessments 
WHERE patient_assessments_patientid IN (3249153);

DELETE FROM careplan_intervention 
WHERE careplan_intervention_patient_id IN (3249153);

DELETE FROM problem_list 
WHERE problem_list_patient_id IN (3249153);

DELETE FROM doc_presc 
WHERE doc_presc_patient_id IN (3249153);

DELETE FROM current_medication 
WHERE current_medication_patient_id IN (3249153);

DELETE FROM encounter 
WHERE encounter_chartid IN (
  SELECT chart_id FROM chart WHERE chart_patientid IN (3249153)
);

DELETE FROM patient_ins_detail 
WHERE patient_ins_detail_patientid IN (3249153);

DELETE FROM chart 
WHERE chart_patientid IN (3249153);

DELETE FROM patient_registration 
WHERE patient_registration_id IN (3249153);

DELETE FROM quality_measures_patient_entries 
WHERE quality_measures_patient_entries_patient_id IN (3249153);

DELETE FROM not_done_details 
WHERE not_done_patientid IN (3249153);

DELETE FROM patient_allergies 
WHERE patallerg_chartid IN (
  SELECT chart_id FROM chart WHERE chart_patientid IN (3249153)
);
```

---

### 🔁 Step 4: Re-Import the QRDA ZIP

After deleting the duplicate data:

1. Re-import the **ZIP file** through the **QRDA section** in Glace.
2. Call the `getPatientsSeen` API again.
3. Optionally, filter using the **employee ID** (`2428`) to confirm correct association.

---

## 📊 MIPS QDM Validation & Final Reporting


### 📌 Step 5: Run QDM Validation API for Single Patient

After verifying the patient and provider entries:

Use the following **2nd API** to validate QDM measures for a single patient:

```http
GET http://192.168.2.241:9090/glaceemr_backend/api/emr/glacemonitor/mipsperformance/generateAndValidateQDM?dbname=glace&accountId=glace&patientID=3274625&providerId=2419&reportingYear=2025
```

> Replace:
>
> * `patientID` → with the actual patient ID
> * `providerId` → with the actual provider ID

---

### ⚙️ Step 6: Bulk QDM Validation using `MIPSSlave.java`

To **validate QDM for multiple patients** in bulk:

1. Open `MIPSSlave.java`
2. Update the file with correct:

   * `patientID`
   * `providerID`
3. Run the class to trigger QDM validation for all configured patients.

---

### 🧹 Step 7: Clean Up Existing Measure Data (Optional)

If duplicate or outdated measure results exist, delete them before re-calculation:

```sql
DELETE FROM macra_measures_rate 
WHERE macra_measures_rate_reporting_year = 2025;
```

---

### 📌 Step 8: Calculate MIPS Performance (Final API)

Call the **3rd API** to calculate the overall MIPS performance:

```http
GET http://192.168.2.241:9090/glaceemr_backend/api/emr/glacemonitor/mipsperformance/calculateMIPSPerformance?reportingYear=2025&accountID=glace&isMonthlyReport=false&dbname=glace
```

---

### 📄 Step 9: View MIPS Performance Report in UI

1. Go to the **Glace Web UI**
2. Navigate to:
   `Reports → MIPS Performance Report`
3. Choose the **Provider**
   Example: `Steve Collier`

---

### 🧪 Step 10: Compare with Cypress Measures

Compare the **calculated measures** from the Glace report with the **Cypress test results**.

> 🔎 If there is **any deviation**, do the following:

* Identify the affected **patients**
* Re-run the **QDM Validation API** (Step 5) for those specific patients
* Investigate the cause (data mismatch, missing elements, timing, etc.)

---









## ⚙️ Project Configuration

### 🔹 Database

* `macra_test`

### 🔹 Legacy Frontend: `EmployeeList.jsp`

Comment out the access restriction code:

```jsp
<%-- 
	if(!<%=INSTALL_LOGIN_USER_ACCESS%>){
		if(!existeduser){
			alert("Access denied. Please Contact Glenwood Administrator");
			return false;
		}
	} 
--%>
```

### 🔹 Backend: `MUPerformanceRateController.java`

Update the `/generateAndValidateQDM` API:

```java
String hub_url = "http://192.168.2.241:8080/glacecds/ECQMServices/validateECQM";
```

## ⚙️ CDS Configuration

> **Note:** Every time a change is made in **Drools**, follow these steps:

1. **Give execution permission** for the rule files.
2. Run:

   ```bash
   mvn clean
   mvn install
   ```

---


Thanks for the detailed continuation! Here's your updated **README.md** section that seamlessly follows from the previous **Post-Import** steps. It includes:

* Calling the **second** and **third** APIs
* Running `MIPSSlave.java` for bulk validation
* Cleaning up duplicate measure data
* Final report comparison instructions

---
