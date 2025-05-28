# 📅 **Day 6 — 27/05/2025**

## 🧩 **Patient Insurance Change Validation & Save Workflow**

---

### ✅ **Key Area: `saveData(isClose)` Function**

📍 *Located in:* `patientregistration1.js`

This function manages validation before patient insurance data is saved. It ensures data consistency, logs changes, and triggers insurance update workflows.

#### 📌 **Core Logic**

| Action                          | Purpose                                                                                   |
| ------------------------------- | ----------------------------------------------------------------------------------------- |
| **Name Change Check**           | Compares `initialLastName`/`initialFirstName` with current inputs. Shows alert on change. |
| **eRx Consent Check**           | If `consentTypeValue === 'N'`, shows warning and halts save if user cancels.              |
| **Deceased Patient Validation** | If `isAlertDeceased === '1'`, validates date/time/cause of death fields.                  |
| **Audit Logging**               | Uses `logger(3)` and `prepareForOP(2)` to track changes. Skipped for new patients.        |
| **Form & Data Validation**      | Ensures all fields are correctly filled before proceeding to save.                        |
| **Insurance Update Trigger**    | Calls `checkOldServiceUpdate(isClose)` to validate insurance changes.                     |

---

### 🔄 **Insurance Change Workflow — `checkOldServiceUpdate(isClose)`**

📍 *Located in:* `UpdateServiceInsurance.js`

#### 1. **Initial Insurance Info Storage**

* `storeInsInfo()` saves original Primary and Secondary insurance IDs on load.

#### 2. **Change Detection**

* Compares current vs. stored insurance IDs.
* If unchanged or `patientId` is not present → calls `saveNow()` immediately.
* If changed → proceeds with update validation via AJAX.

#### 3. **AJAX Call → `ChangeInsurance.Action`**

Sends:

* `patientId`
* Current insurance IDs
* Flags: `isPrimaryChanged`, `isSecondaryChanged`

Receives:

* Flags indicating change
* Lists of **pending services** linked to old insurance data

#### 4. **Handling Response**

* If no pending services → skip popup, directly call `saveNow()`
* If services exist:

  * Store data in `primaryData` / `secondaryData`
  * Show popup for user selection

#### 5. **User Interaction**

Popup allows:

* View of services affected by insurance change
* Selection of services to update with new insurance
* Confirmation before final submission

#### 6. **Final Submission**

* `drawSubmit()` captures selection
* Proceeds with `saveNow()` to complete the save

---

### 💾 `saveNow(isClose)` Overview

| Component             | Purpose                                                              |
| --------------------- | -------------------------------------------------------------------- |
| `patientId` Check     | Determines whether it’s an update or a new patient entry             |
| `insuranceDifference` | Tracks whether insurance details were changed                        |
| `form.action`         | Submits form data to the backend                                     |
| `checkduplicate()`    | Prevents duplicate patient creation                                  |
| `createNewPatient()`  | Registers new patient if applicable                                  |
| `isClose` Flag        | Indicates if the form should simply save or save and close the popup |

---

## 🔍 **Today’s Core Task: Understanding Primary Insurance ID Linking**

---

### 🗂️ Step 1: Understand the Tables

| Table                | Description                                  |
| -------------------- | -------------------------------------------- |
| `patient_ins_detail` | Stores patient's insurance details           |
| `ins_comp_addr`      | Stores insurance company names and addresses |

---

### 🔗 Step 2: Identify Linking Columns

* `patient_ins_detail.patient_ins_detail_insaddressid` → Links to:
* `ins_comp_addr.ins_comp_addr_id`

These columns connect **patient insurance entries** with **insurance company addresses**.

---

### 🧪 Step 3: Verify Mapping

* Confirm that IDs from both columns match.
* If they do, the insurance detail refers to the insurance company in `ins_comp_addr`.

---

### 🧾 Step 4: SQL JOIN to Fetch Insurance Info

```sql
SELECT 
  pid.patient_ins_detail_patientinsuranceid,
  ica.ins_comp_addr_ins_name,
  ica.ins_comp_addr_address,
  ica.ins_comp_addr_city,
  ica.ins_comp_addr_state,
  ica.ins_comp_addr_zip
FROM patient_ins_detail pid
JOIN ins_comp_addr ica 
  ON pid.patient_ins_detail_insaddressid = ica.ins_comp_addr_id
WHERE pid.patient_ins_detail_patientid = 376642;
```

* This joins patient insurance with their insurance provider details.
* Filters by `patientid = 376642`.

---

### 🧾 Step 5: Result Interpretation

* Returns:

  * Insurance ID
  * Insurance Company Name
  * Address (City, State, Zip)
* Confirms which insurance company a patient’s insurance entry is linked to.

---


### 🧭 **Insurance Change Validation Workflow**

<details>
<summary>Click to expand Mermaid Flowchart</summary>

```mermaid
flowchart TD
    A[Start: Click "Save" Button] --> B{Insurance ID Changed?}
    B -- No --> C[Call saveNow()]
    B -- Yes --> D[Call checkOldServiceUpdate()]
    D --> E[Compare Stored vs Current IDs]
    E --> F{Insurance Changed?}
    F -- No --> C
    F -- Yes --> G[Send AJAX to ChangeInsurance.Action]
    G --> H[Receive Pending Services Data]
    H --> I{Pending Services Exist?}
    I -- No --> C
    I -- Yes --> J[Display Popup for Service Update]
    J --> K[User Selects Services]
    K --> L[Call drawSubmit()]
    L --> C
    C --> M[Save Data via Backend]
    M --> N[End]
```

</details>


### 📌 Result:

👉 [See the flow here](https://www.mermaidchart.com/raw/6e088123-b8a3-4857-b407-d9a2d2b572d1?theme=light&version=v0.1&format=svg)

---



### 🗃️ **Database Relationship: Patient Insurance ↔ Insurance Company Address**

<details>
<summary>Click to expand Mermaid ER Diagram</summary>

```mermaid
erDiagram
    PATIENT_INS_DETAIL ||--|| INS_COMP_ADDR : links_to

    PATIENT_INS_DETAIL {
        int patient_ins_detail_patientinsuranceid PK
        int patient_ins_detail_patientid
        int patient_ins_detail_insaddressid FK
        varchar insurance_type
        boolean isactive
        date effective_date
        ...
    }

    INS_COMP_ADDR {
        int ins_comp_addr_id PK
        varchar ins_comp_addr_ins_name
        varchar ins_comp_addr_address
        varchar ins_comp_addr_city
        varchar ins_comp_addr_state
        varchar ins_comp_addr_zip
        ...
    }
```

</details>

---

### 🧩 How It Works

* The foreign key `patient_ins_detail_insaddressid` in `PATIENT_INS_DETAIL` **references** `ins_comp_addr_id` in `INS_COMP_ADDR`.
* This lets you pull the full insurance company name and address for any given patient's insurance record.

Perfect! Here’s a **combined flowchart and data relationship diagram** using Mermaid, great for a full-picture view of how the **patient insurance change** flows from the **UI through validation to the database**.

---

## 🧭 **Complete Insurance Change Flow + Data Mapping**

<details>
<summary>Click to expand Combined Mermaid Diagram</summary>

```mermaid
flowchart TD
    %% UI & Frontend Logic
    A[Start: User clicks "Save" on Patient Registration] --> B{Insurance ID Changed?}
    B -- No --> C[Call saveNow() → Submit Form]
    B -- Yes --> D[Call checkOldServiceUpdate()]
    D --> E[Compare Stored vs Current IDs]
    E --> F{Insurance Changed?}
    F -- No --> C
    F -- Yes --> G[Send AJAX → ChangeInsurance.Action]
    G --> H[Server Checks for Pending Services]
    H --> I{Pending Services Exist?}
    I -- No --> C
    I -- Yes --> J[Popup: User selects services to update]
    J --> K[User confirms → drawSubmit()]
    K --> C
    C --> L[Form submitted to backend]

    %% Backend + DB Layer
    L --> M[PatientRegistrationActionNew.java]
    M --> N[Deactivate Old Insurance (isactive = false)]
    N --> O[Insert New Insurance Record → patient_ins_detail]
    O --> P[Link to ins_comp_addr via patient_ins_detail_insaddressid]

    %% DB Relationship
    subgraph DB [Database Tables]
        PID[patient_ins_detail] -->|FK: patient_ins_detail_insaddressid| ICA[ins_comp_addr]
    end

    P --> PID
```

</details>

---

### 📌 Highlights

* **Top half** shows **frontend logic** (JS + validation + AJAX).
* **Bottom half** shows the **backend and database insert** logic.
* **`patient_ins_detail` ↔ `ins_comp_addr`** link is clearly marked via FK relationship.






