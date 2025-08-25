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

> **Note:** We don’t have access to register via UI. UI validation must be bypassed.

1. Open:
   [http://localhost:8080/Glace/jsp/configure/EmployeeList.jsp](http://localhost:8080/Glace/jsp/configure/EmployeeList.jsp)

3. **Bypass Access Restriction**
   Modify `EmployeeList.jsp`:

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
3.Register the employee/provider.

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
2. Import it into the **QRDM Configuration** under **General Practice** in the Glace system.

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

