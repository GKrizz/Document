# 📅 Day 17 — June 4, 2025

### 🥇 Task: Analyze Backend Auto-Save CPT Codes Flow for HEDIS Clinical Services


## 🏥 HEDIS Clinical Services Processor

**Auto-Save CPT Logic (June 4, 2025)**

This backend method is responsible for **automatically saving CPT codes** (medical billing codes) during a patient’s encounter based on HEDIS quality measures. It ensures codes are properly inserted, updated, or deleted based on the latest clinical data like vitals and lab results.

---

## 📌 Purpose

* Automatically detect applicable **clinical services** using `measureIds`
* **Avoid duplicate CPT entries**
* **Update or delete** outdated CPTs if data changes (e.g., vitals, labs)
* Dynamically build **CPT service records** for billing and compliance

---

## 🧩 Core Components

### 🔹 Initialization

```java
List<SaveServicesBean> saveServiceBean = new ArrayList<>();
List<String> newCptCodes = new ArrayList<>();
```

### 🔹 Measure Flags

```java
boolean includeBMI = measureIds.contains("128");
boolean includeBP = measureIds.contains("236");
boolean currentMed = measureIds.contains("130");
boolean isMammogramConfigured = measureIds.contains("112");
```

### 🔹 Existing CPT Trackers

```java
SaveServicesBean existingSystolicBean = null;
SaveServicesBean existingDiastolicBean = null;
SaveServicesBean existingBmiBean = null;
SaveServicesBean existingMammogramBean = null;
```

Used to check for previously submitted CPT services to:

* Avoid duplication
* Update if needed
* Delete if obsolete

---

## 🧪 CPT Code Definitions

### ✅ Blood Pressure

```java
List<String> systolicCodesList = Arrays.asList("3075F", "3074F", "3077F");
List<String> diastolicCodesList = Arrays.asList("3079F", "3078F", "3080F");
```

* **Systolic**

  * `3074F`: < 130 mm Hg
  * `3075F`: 130–139 mm Hg
  * `3077F`: ≥ 140 mm Hg

* **Diastolic**

  * `3078F`: < 80 mm Hg
  * `3079F`: 80–89 mm Hg
  * `3080F`: ≥ 90 mm Hg

---

### ✅ BMI

```java
List<String> bmiCodesList = Arrays.asList("G8420", "G8417", "G8418", "G2181", "G8419");
```

* `G8420`: Normal BMI
* Others indicate high/low BMI ranges

---

### ✅ Mammogram

```java
List<String> mammogramCodesList = Arrays.asList("G9900", "G9899");
```

* `G9900`: Ordered
* `G9899`: Completed

---

### ✅ Other Constants

```java
String currentMedReviewd = "G8427";
String mammogramOrderedCpt = "G9900";
String noBpCode = "G8756"; // No BP documented
```

---

## 🔄 Flow Breakdown

### 1. **Load Existing CPT Services**

```java
List<SaveServicesBean> existingServices = getCreatedServices(patientId, dos, "-99");
```

Match them:

```java
for (SaveServicesBean service : existingServices) {
    String cpt = service.getCptCode();
    if (systolicCodesList.contains(cpt)) existingSystolicBean = service;
    else if (diastolicCodesList.contains(cpt)) existingDiastolicBean = service;
    else if (bmiCodesList.contains(cpt)) existingBmiBean = service;
    else if (mammogramCodesList.contains(cpt)) existingMammogramBean = service;
}
```

---

### 2. **Fetch Vitals if Needed**

```java
if (includeBMI || includeBP) {
    JSONArray vitalsData = saveClinicalDataService.getVitals(encounterId, patientId, chartId);
    for (int i = 0; i < vitalsData.length(); i++) {
        JSONObject vital = vitalsData.getJSONObject(i);
        systolicCode = vital.optString("BPSystolic", "");
        diastolicCode = vital.optString("BPDiastolic", "");
        bmiCode = vital.optString("BMI", "");
    }
}
```

---

## 🛠️ Measure-by-Measure Logic


### 📘 BMI Logic (Measure ID: 128)

1. **Check if BMI is included** in the current measure IDs:

   ```java
   if (includeBMI) {
   ```
   

2. **If new BMI CPT (`bmiCode`) is present in vitals:**

   ```java
   if (bmiCode != null && !bmiCode.isEmpty()) {
   ```

3. **Check for an existing saved BMI service:**

   ```java
   if (existingBmiBean != null && !existingBmiBean.getCptCode().isEmpty()) {
   ```

4. **If not submitted yet and CPT code differs:**

   > ⚠️ Correct comparison uses `.equals()` in Java:

   ```java
   if (existingBmiBean.getSubmitStatus().equals("0") &&
       !bmiCode.equalsIgnoreCase(existingBmiBean.getCptCode())) {
   ```

5. **Update the existing BMI service:**

   ```java
   Cpt newBmiCpt = getCptCodesDetails(bmiCode, -1, -1);
   saveServiceBean.add(updateExistingService(existingBmiBean, newBmiCpt));
   ```

6. **Else (no existing BMI service): Add new CPT**

   ```java
   } else {
       newCptCodes.add(bmiCode);
   }
   ```

7. **If BMI CPT is now missing but an old unsaved one exists: Delete it**

   ```java
   else if ((bmiCode == null || bmiCode.isEmpty()) &&
            existingBmiBean != null &&
            !existingBmiBean.getCptCode().isEmpty() &&
            existingBmiBean.getSubmitStatus().equals("0")) {
       getDeleteService(existingBmiBean.getServiceId());
   }
   ```

* CPT Codes: `G8420`, `G8417`, `G8418`, `G2181`, `G8419`
* Checks if a **BMI value** is available.
* If yes, and it differs from what's already saved → **update**.
* If no BMI now but previously saved → **delete**.
* If new and no old record → **add**.
  
---

### 💉 Diastolic BP Logic (Measure ID: 236)

Handles **diastolic blood pressure CPTs** such as `3078F`, `3079F`, `3080F`.

1. **If new `diastolicCode` exists (non-empty):**

   ```java
   if (diastolicCode != null && !diastolicCode.isEmpty()) {
   ```

2. **If there's an existing service:**

   ```java
   if (existingDiastolicBean != null && !existingDiastolicBean.getCptCode().isEmpty()) {
   ```

3. **If CPT changed: update it**

   ```java
   if (!diastolicCode.equalsIgnoreCase(existingDiastolicBean.getCptCode())) {
       Cpt newCpt = getCptCodesDetails(diastolicCode, -1, -1);
       saveServiceBean.add(updateExistingService(existingDiastolicBean, newCpt));
   }
   ```

4. **Else if no existing CPT: add new**

   ```java
   } else {
       newCptCodes.add(diastolicCode);
   }
   ```

5. **If no diastolic code now, but old exists (unsaved): delete it**

   ```java
   } else if (existingDiastolicBean != null &&
              !existingDiastolicBean.getCptCode().isEmpty()) {
       getDeleteService(existingDiastolicBean.getServiceId());
   }
   ```

   * CPT Codes:

  * **Systolic:** `3074F`, `3075F`, `3077F`
  * **Diastolic:** `3078F`, `3079F`, `3080F`
  * **No BP documented:** `G8756`
* Checks if either systolic or diastolic value exists:

  * If not → add `G8756`
  * If values changed → **update** saved CPT
  * If missing now but was saved → **delete**
  * Else → **add new entries**


✅ *This same logic applies to **systolic BP** as well, and both can be **refactored** using a helper function to avoid duplication.*

---

### 🔹 **Current Medications — Measure 130**

* CPT Code: `G8427`
* If `medReviewedstatus == 1` (reviewed), then add `G8427`
---

### 🩺 Mammogram Logic (Measure ID: 112)

Manages CPTs related to breast cancer screening.

1. **Check if mammogram processing is enabled:**

   ```java
   if (isMammogramConfigured) {
   ```

2. **Only proceed if `labsFrom != "-1"`**

   ```java
   if (!"-1".equals(labsFrom)) {
   ```

3. **If it's a new order (`labsFrom == "saveNewOrder"`):**

   ```java
   if ("saveNewOrder".equals(labsFrom)) {
       newCptCodes.add("G9900");  // Mammogram Ordered
   }
   ```

4. **If updating an existing lab (`labsFrom == "updateLabTestDetails"`):**

   ```java
   else if ("updateLabTestDetails".equals(labsFrom)) {
       if ("mammogram".equalsIgnoreCase(labOrderEntryBean.getLabOrderTestName())) {
           String status = labOrderEntryBean.getLabTestStatus();
   ```

5. **If test is Cancelled/Declined/Not Needed:**

   ```java
   if (Arrays.asList("Cancelled", "Deleted", "Patient Declined", "Not Needed").contains(status)) {
       getDeleteService(existingMammogramBean.getServiceId());
   }
   ```

6. **If test was Performed/Completed/Reviewed:**

   ```java
   else if (Arrays.asList("Performed", "Results Reviewed", "Completed (Patient Informed)", "Completed").contains(status)) {
       Cpt updatedCpt = getCptCodesDetails("G9899", -1, -1);  // Mammogram Completed
       saveServiceBean.add(updateExistingService(existingMammogramBean, updatedCpt));
   }
   ```


* CPT Codes:

  * **Ordered:** `G9900`
  * **Performed/Reviewed/Completed:** `G9899`

* Logic based on lab order:

  * If test is **ordered** via lab: add `G9900`
  * If test is **performed or completed**: update to `G9899`
  * If **cancelled or declined**: delete CPT from services

---

## 🗃️ SQL Reference

```sql
-- View sample CPTs
SELECT * FROM cpt LIMIT 10;

-- Check active CPTs
SELECT * FROM cpt
WHERE cpt_cptcode IN ('G0356', '3075F', 'G8420')
AND cpt_isactive = true;
```

---



