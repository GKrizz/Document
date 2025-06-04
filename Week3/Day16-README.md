# 📅 Day 16 — Jun 3, 2025 (Tuesday)

## 💾 Save Call Flow in Screening Tab

This document explains how the **Save operation** works when a user updates and submits a **Screening Questionnaire** in the GWT-based application.

---

## ✅ Feature Overview

When the user:

1. Clicks the **"Edit"** button,
2. Updates screening questions,
3. Then clicks **"Save"** —

👉 The system sends a **POST request** to persist updated clinical findings in the database.

---

## 🔁 Flow Summary

### 1️⃣ **User Interaction**

* Clicks **"Edit"** in the Screening tab
* Application fetches existing answers using a **GET request**:

  ```
  GET /glaceemr_backend/api/desktop/user/Screenings/EditScreeningQuestions
  ```

  **Query Params:**

  ```
  riskAssessmentId=6862
  patientId=375356
  encounterId=152347
  ```

---

### 2️⃣ **Backend: Fetch for Edit**

**File:** `ScreeningsController.java`
**Method:** `editScreeningQuestions(...)`

```java
@RequestMapping(value="/EditScreeningQuestions", method = RequestMethod.GET)
@ResponseBody
public Map<String, Object> editScreeningQuestions(...) {
    ...
}
```

Returns a `Map` with:

* `"screeningStatus"` – current status
* `"patientQandAData"` – saved answers to screening questions

---

### 3️⃣ **User Updates Form**

* UI displays the fetched data in editable form.
* User modifies answers.

---

### 4️⃣ **User Clicks "Save"**

* Sends a **POST request** to save the updates:

  ```
  POST /glaceemr_backend/api/desktop/user/Screenings/SaveScreeningQuestions
  ```

---

### 5️⃣ **Backend: Save Call**

**File:** `ScreeningsController.java`
**Method Called:** `SaveScreeningsquestions(...)`

* Invokes **ScreeningsServiceImpl.java** → `SaveScreeningsquestions(...)`

---

### 🔧 Key Save Method

**File:** `ScreeningsServiceImpl.java`
**Method:** `SaveScreeningsquestions(...)`

```java
public void SaveScreeningsquestions(List<PatientClinicalFindingsBean> findingsList,
                                    Integer patientId, Integer encounterId,
                                    Integer riskAssessmentId, Integer userId, Integer mode) {
    for (PatientClinicalFindingsBean bean : findingsList) {
        PatientClinicalFindings entity;

        if (bean.getPatientClinicalFindingsId() != -1 && mode != 1) {
            entity = repository.findById(bean.getPatientClinicalFindingsId());
            entity.setModifiedBy(userId);
        } else {
            entity = new PatientClinicalFindings();
            entity.setCreatedBy(userId);
        }

        entity.setPatientId(patientId);
        entity.setEncounterId(encounterId);
        entity.setRiskAssessmentId(riskAssessmentId);
        // ... set date, result values, codes, comments, etc.

        repository.save(entity);
    }
}
```

---

## 🧠 Logic Highlights

| Task                 | Explanation                                                             |
| -------------------- | ----------------------------------------------------------------------- |
| **Create vs Update** | Checks if record already exists using ID and Mode flag                  |
| **Audit Trail**      | Sets `createdBy`, `modifiedBy`, and timestamps                          |
| **Flexible Fields**  | Handles rich data: result values, status, descriptions, codes, notes    |
| **Data Integrity**   | Ensures each clinical finding is tied to patient, encounter, assessment |

---

## 🧩 Components Involved

| File                                | Role                                           |
| ----------------------------------- | ---------------------------------------------- |
| `ScreeningsController.java`         | Receives REST requests (Edit + Save)           |
| `ScreeningsService.java`            | Interface with save/edit methods               |
| `ScreeningsServiceImpl.java`        | Implements business logic for save operations  |
| `PatientClinicalFindingsBean`       | DTO representing a single question/answer unit |
| `patientClinicalFindingsRepository` | Handles DB operations for findings             |

---

## 📌 Example API Calls

### ✏️ Edit

```http
GET /api/desktop/user/Screenings/EditScreeningQuestions
?riskAssessmentId=6862
&patientId=375356
&encounterId=152347
```

### 💾 Save

```http
POST /api/desktop/user/Screenings/SaveScreeningQuestions
Body: JSON Array of PatientClinicalFindingsBean
```

---

## 🖼️ Visual Flow

```
[User Clicks Edit]
      ↓
GET /EditScreeningQuestions
      ↓
ScreeningsController → editScreeningQuestions()
      ↓
ScreeningsService → fetchQ&A, status
      ↓
[Form Filled with Data]

[User Clicks Save]
      ↓
POST /SaveScreeningQuestions
      ↓
ScreeningsController → SaveScreeningsquestions()
      ↓
ScreeningsServiceImpl → Save each answer to DB
      ↓
[Save Success]
```

---


## 🗃️ Tables Involved

| Table Name                  | Description                                       |
| --------------------------- | ------------------------------------------------- |
| `patient_clinical_findings` | Stores individual screening responses per patient |
| `risk_assessment`           | Links assessments to patients and encounters      |
| `emp_profile`               | Tracks employee (user) info for audit purposes    |
