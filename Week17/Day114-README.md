
### 🔁 **Overview: What is this code doing?**

This flow is triggered when a user clicks **"Add CPT II"** on the UI. It:

1. Calls a JavaScript function `addCptIIServices()`
2. Sends an AJAX request (`mode=455`) to the backend (`ServiceEntry.java`)
3. The backend checks HEDIS configuration for a provider & year
4. If active, it fetches measure IDs
5. Sends another internal call to save CPT II codes
6. On success, refreshes the services on the UI

---

## 🔹 1. **JSP File** (`ServiceEntryForEMR.jsp`)

```jsp
<a id="addCPTII" href='javascript:addCptIIServices();' ...>Add CPT II</a>
```

* This is the clickable link/button the user presses in the UI to start the CPT II service creation process.
* It calls `addCptIIServices()` JavaScript function.

---

## 🔹 2. **JavaScript Function** (`ServiceEntryForEMR.js`)

### Function: `addCptIIServices()`

```javascript
function addCptIIServices(){
	var patientId = getPatientId();               // gets the current patient ID
	alert("patientId >>>>>>>>>>>."+patientId)
	alert("ENCOUNTER_ID >>>>>>>>>>>"+ENCOUNTER_ID)

	var callback = {
		success: addCptIIServicesSuccess,         // calls this function if server returns success
		failure: onFailure,                       // calls this if server returns error
	}

	getAjaxDataFromServer("ServiceEntry.Action", "patientId="+patientId+"&mode=455&encounterId="+ENCOUNTER_ID, false, callback);
}
```

* **Purpose**: Sends a request to the server with `mode=455`, along with `patientId` and `encounterId`.

---

### Function: `addCptIIServicesSuccess()`

```javascript
function addCptIIServicesSuccess (){
	alert("addCptIIServicesSuccess  called ::::::::")
	onloadServiceActions()  // reloads the service entries on the screen
}
```

---

## 🔹 3. **Java Backend** (`ServiceEntry.java`)

### Code triggered by: `mode == 455`

Here’s what the backend does in order:

---

### ✅ **STEP 1: Extract Request Params**

```java
String encounterId = HUtil.Nz(request.getParameter("encounterId"), "-1");
String patientId = HUtil.Nz(request.getParameter("patientId"), "-1");
```

---

### ✅ **STEP 2: Fetch Encounter Details**

```java
String encounterDetails = "SELECT encounter_date, encounter_service_doctor, encounter_chartid FROM encounter WHERE encounter_id = " + encounterId;
```

* Gets the **encounter date**, **provider ID**, and **chart ID**

---

### ✅ **STEP 3: Determine Reporting Year**

```java
Integer reportingYear = (encounterDate != null) ? Integer.parseInt(new SimpleDateFormat("yyyy").format(encounterDate)) : -1;
```

---

### ✅ **STEP 4: Check HEDIS Configuration**

```java
String isHedisConfigured = dbutils.newTableLookUp("hedis_configuration_isactive", "hedis_configuration", "hedis_configuration_reporting_year = " + reportingYear);
```

* Looks up if HEDIS is active for that reporting year

---

### ✅ **STEP 5: If HEDIS is Active, Get Measures for Provider**

```java
String configMeasureQry = "SELECT string_agg(hedis_measure_provider_configuration_measure_id, ',') AS measures FROM hedis_measure_provider_configuration WHERE hedis_measure_provider_configuration_provider_id = " + providerId1 + " AND hedis_measure_provider_configuration_reporting_year = " + reportingYear;
```

* Gets a comma-separated string of **measure IDs** for that provider and year

---

### ✅ **STEP 6: Call Internal Endpoint to Save CPT II Codes**

```java
String urlData = "Charges/saveCptIICodes?encounterId=" + encounterId + "&providerId=" + providerId1 + "&patientId=" + patientId + "&measureIds=" + measureIds + "&chartid=" + chartId + "&username=" + URLEncoder.encode(session.getAttribute("username").toString(), StandardCharsets.UTF_8.toString());

EMRResponseBean emrResponseBean = new CustomURLConnection(ConnectionType.GET.toString(), request, urlData).perform();
```

* Sends a GET request internally to the `Charges/saveCptIICodes` endpoint
* Includes: `encounterId`, `providerId`, `patientId`, `measureIds`, `chartId`, `username`

---

### ✅ **STEP 7: Handle Response**

```java
if (emrResponseBean != null) {
    // Success
} else {
    GlaceOutLoggingStream.console("Error: Failed to create services.");
    throw new RuntimeException("Failed to create services..");
}
```

* If the CPT II codes were not saved correctly, it logs and throws an error

---

## ✅ Flow Summary:

| Step | Layer    | What Happens                                                                |
| ---- | -------- | --------------------------------------------------------------------------- |
| 1    | **JSP**  | User clicks **"Add CPT II"**                                                |
| 2    | **JS**   | `addCptIIServices()` sends AJAX call to server                              |
| 3    | **Java** | Server handles `mode=455` request                                           |
| 4    |          | Fetches encounter info and reporting year                                   |
| 5    |          | Checks if HEDIS config is active for that year                              |
| 6    |          | If yes, fetches configured **measure IDs**                                  |
| 7    |          | Calls backend endpoint to **save CPT II codes**                             |
| 8    | **JS**   | If successful, `onloadServiceActions()` is called to refresh services in UI |

---

## 🧠 Why is this done?

* **CPT II codes** are used for **quality measurement** (common in HEDIS).
* This flow auto-adds CPT II codes based on encounter/provider/year/measure configuration.
* Makes it easier for providers to stay compliant with reporting standards.

---




# 🏥 Superbill – Add CPT II Codes [MODERN VIEW]

## 📌 Overview

This module provides functionality to **add CPT II codes** to a patient encounter from the Superbill desktop view.
It is implemented using **MVP (UI layer in GWT)** and **MVC (backend layer in Spring)**.

---

## 🏗️ Architecture

### UI Layer (MVP pattern)

* **`SuperbillSummaryDesktopView.ui.xml`**
  GWT UI XML with the **Add CPT** button.

* **`SuperbillSummaryDesktopView.java`**
  Concrete implementation of the desktop view; wires UI elements with logic.

* **`SuperbillSummaryView.java`**
  View interface, defines what the Presenter can call on the view.

* **`SuperbillSummaryActivity.java`**
  Presenter/Activity:

  * Handles UI events (e.g. Add CPT button click).
  * Calls the client service.
  * Updates the view with success/error notifications.

* **`SuperBillClient.java`**
  GWT client service to call backend REST endpoints.

---

### Backend Layer (MVC pattern)

* **`ChargesController.java`**
  Spring REST controller exposing `/Charges/addCptIiCodes`.

* **`ChargesServices.java`**
  Service interface for charges logic.

* **`ChargesServicesImpl.java`**
  Service implementation:

  * Validates `patientId` and `encounterId`.
  * Loads encounter details from `encounterRepository`.
  * Checks HEDIS measure configuration.
  * Builds performed date & context info.
  * Calls `saveServicesBasedOnHedisMeasuresConfig(...)`.
  * Returns success/failure.

---

## ⚙️ Flow – Add CPT II Codes

### 1. UI

```xml
<b:Button ui:field="addCptButton"
          b:id="addCptButton"
          text="Add CPT"
          type="DEFAULT"
          size="SMALL"
          title="Add CPT Code" />
```

### 2. Presenter

```java
@UiHandler("addCptButton")
public void onAddCptClicked(ClickEvent event) {
    if (presenter != null) {
        presenter.onAddCptClicked(presenter.getActionServletURL().getaccountId());
    }
}
```

* Retrieves `patientId` and `encounterId`.
* Shows a loading notification.
* Calls `superBillClient.addCptIiCodes(...)`.

### 3. Client → Controller

```java
@POST
@Path("/Charges/addCptIiCodes?dbname={dbname}&patientId={patientId}&encounterId={encounterId}")
void addCptIiCodes(@PathParam("dbname") String dbname,
                   @PathParam("patientId") Integer patientId,
                   @PathParam("encounterId") Integer encounterId,
                   MethodCallback<Boolean> callback);
```

### 4. Controller

```java
@RequestMapping(value = "/addCptIiCodes", method = RequestMethod.POST)
@ResponseBody
public Boolean addCptIiCodes(@RequestParam("dbname") String dbname,
                             @RequestParam("patientId") Integer patientId,
                             @RequestParam("encounterId") Integer encounterId) throws Exception {
    return chargesServices.addCptIiCodes(dbname, patientId, encounterId);
}
```

### 5. Service Implementation

```java
@Override
@Transactional
public boolean addCptIiCodes(String dbname, Integer patientId, Integer encounterId) throws Exception {
    if (patientId == null || encounterId == null) return false;

    Encounter encounter = encounterRepository.findbyEncounterId(encounterId);
    if (encounter == null) return false;

    Integer chartId = (encounter.getEncounterChartid() != null) ? encounter.getEncounterChartid() : -1;
    String measureIds = measureCalculationService.checkHedisConfiguration(encounterId);
    if (measureIds == null || measureIds.isEmpty()) return false;

    Timestamp encounterTs = encounter.getEncounterDate();
    String performedDate = (encounterTs != null)
            ? new SimpleDateFormat("yyyy-MM-dd").format(encounterTs)
            : new SimpleDateFormat("yyyy-MM-dd").format(new java.util.Date());

    String username = TennantContextHolder.getRequestcontext().getUserName();
    String responseMsg = saveServicesBasedOnHedisMeasuresConfig(
            encounterId, patientId, chartId, measureIds, performedDate, "", username
    );

    return (responseMsg != null && !responseMsg.isEmpty());
}
```

---

## 🔑 Endpoint Summary

### Add CPT II Codes

```
POST /Charges/addCptIiCodes
Params:
  - dbname (String, required by signature, not used internally)
  - patientId (Integer)
  - encounterId (Integer)
Returns:
  - Boolean (success/failure)
```

---

## 📂 File Reference

```
UI
 ├── SuperbillSummaryDesktopView.ui.xml   (UI definition – Add CPT button)
 ├── SuperbillSummaryDesktopView.java     (UI implementation)
 ├── SuperbillSummaryView.java            (View interface)
 ├── SuperbillSummaryActivity.java        (Presenter handling logic)
 └── SuperBillClient.java                 (Client-side REST calls)

Backend
 ├── ChargesController.java               (REST endpoints)
 ├── ChargesServices.java                 (Service interface)
 └── ChargesServicesImpl.java             (Service implementation logic)
```

---


