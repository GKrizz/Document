# 📄 Case #233445 – Referrals to 3 Fax Numbers (Dr. Gaffney)
---

<table>
  <tr>
    <th style="text-align:left; width: 50%;">📌 <strong>Client Information</strong></th>
    <th style="text-align:left; width: 50%;">👤 <strong>Case Ownership</strong></th>
  </tr>
  <tr>
    <td>
      <strong>Account:</strong> Willis F. Gaffney MD PC<br>
      <strong>Contact Name:</strong> Dr. Willis F. Gaffney, M.D.<br>
      <strong>Phone:</strong> 9898319009<br>
      <strong>Email:</strong> <a href="mailto:gaffneywillmd11@gmail.com">gaffneywillmd11@gmail.com</a>
    </td>
    <td>
      <strong>Case Owner:</strong> Padmanaban Guruswamy (Paddy)<br>
      <strong>Developer Assigned:</strong> Gobala Krishnan
    </td>
  </tr>
</table>

---

### 📌 Case Summary

| Field                         | Detail                       |
| ----------------------------- | ---------------------------- |
| **Case ID**                   | 233445                       |
| **Account**                   | Willis F. Gaffney MD PC.     |
| **Reported By**               | Dr. Willis F. Gaffney, M.D.  |
| **Product**                   | GlacePremium                 |
| **Module**                    | DIRECT                       |
| **Assigned To**               | Gobala Krishnan              |
| **Case Owner**                | Padmanaban Guruswamy (Paddy) |
| **Reported On**               | 18-June-2025                 |
| **Priority**                  | Medium                       |
| **Status**                    | Open                         |
| **Estimated Resolution Date** | 30-June-2025                 |

---
### 🎯 **Subject**

> **Referrals to be sent to 3 fax numbers at a time**

### 🧾 **Summary**

Dr. Gaffney requested that **referrals be broadcast to multiple fax numbers (up to 3 simultaneously)** for a specific referring doctor.

---


### 🔍 **Root Cause**

The existing database schema for `referring_doctor_fax_number` was restricted to `VARCHAR(25)`. When saving multiple fax numbers separated by `$$`, it exceeded the limit, resulting in:

```
ERROR: value too long for type character varying(25)
```

This caused an **HTTP 500 – Internal Server Error** on saving the referring doctor with multiple fax numbers.

---

### 🧾 Request Description

> **"Can I get a referral set up which will broadcast information to 3 fax numbers at one time?
> If so, I would like to add one specific referral to..."**
> – Dr. Willis Gaffney

---

### ⚙️ Objective

**Enable a referral configuration that can send (broadcast) to multiple fax numbers (specifically 3) simultaneously for a provider.**

---

### 🔍 Analysis

* Existing implementation stores fax numbers in the DB in a single column:
  `referring_doctor_fax_number` (type: `VARCHAR(25)`).

* Format: Multiple fax numbers separated using `$$` delimiter.

* When trying to save more than one fax number, the value length exceeds **25 characters**, resulting in:

  ```
  ERROR: value too long for type character varying(25)
  ```

* Verified actual failing data:

  ```
  1235678999$$7539512584 → 23 characters (allowed)  
  1235678999$$7539512584$$9999999999 → 36 characters (ERROR)
  ```

---

### 🛠️ Resolution

#### ✅ Fix Applied

* **Database schema updated**:

  ```sql
  ALTER TABLE referring_doctor
  ALTER COLUMN referring_doctor_fax_number TYPE VARCHAR(100);
  ```

* Allows storing **up to 3 fax numbers** (max length \~34 characters, with room for future needs).

#### ✅ Frontend Code Review

* Confirmed JS constructs fax values using:

  ```js
  faxValue += fullFax + "$$";
  ```

* Ends with:

  ```js
  document.forms[0].txtfaxno.value = faxValue;
  ```

* Save logic and input field IDs verified to match backend structure.

---

# 🔍 Technical Breakdown

---
## 1️⃣ Referring Physician Data Flow

### 🔍 **1. Locate How 'Referring Physician' Is Drawn and Handled (Add / Update / Delete)**

#### 🔹 Where to Look:

* **Page:** `Configure > General Practice Settings > Referring Physician`
![Screenshot from 2025-06-30 10-02-18](https://github.com/user-attachments/assets/f1970bb5-4df8-4510-ac4d-3169b80d01ca)
![Screenshot from 2025-06-30 10-02-48](https://github.com/user-attachments/assets/ba3ad749-d4ef-42fa-ab0d-0541be126b4c)

| Operation    | Description                                            | Table              |
| ------------ | ------------------------------------------------------ | ------------------ |
| Add / Update | Via `Saverefdoc()` → backend INSERT/UPDATE             | `referring_doctor` |
| Delete       | Soft delete (sets `referring_doctor_isactive = false`) | `referring_doctor` |
| Fetch        | Via `assignElements()` → populate UI fields            | `referring_doctor` |


#### 🔹 Key DB Table:

```sql
Table: referring_doctor
```

#### 🔹 Key Fields:

* `referring_doctor_lastname`
* `referring_doctor_firstname`
* `referring_doctor_fax_number` ✅
* `referring_doctor_phoneno` ✅

---

### 🔍 **2. Phone Number Flow (Multiple Numbers)**

#### ✅ Goal:

Understand how **+ Add button** appends additional phone fields and how they're **stored in DB**.

#### 🔹 What You've Already Observed:

* JS splits numbers into `<input>` fields: `txtphoneext1`, `txtphoneext2`, `txtphoneext3`, etc.
* In `Saverefdoc()`:

```js
phoneNumbers += document.getElementById("phNumber" + i).value;
if(phoneNumbers.length >= 10) phoneNumbers += "$$";
```

#### ✅ Conclusion:

| Aspect              | Value                                                |
| ------------------- | ---------------------------------------------------- |
| **Separator**       | `$$`                                                 |
| **Final DB Column** | `referring_doctor_phoneno`                           |
| **Data Format**     | Concatenated phone numbers: `1234567890$$4567890123` |

---

### 🔍 **3. Fax Number Logic (Update to Support Multiple)**

#### 🔹 Current Limitation:

* Only **one fax number** allowed.
* DB column: `referring_doctor_fax_number VARCHAR(25)`

#### 🔹 Fix Already Done:

* You **updated the DB column** to `VARCHAR(100)` ✅
* JS logic (in `Saverefdoc`) already builds:

```js
faxValue += fullFax + "$$";
```

#### 🛠️ What You Need to Do:

| Step      | Description                                                                                           |
| --------- | ----------------------------------------------------------------------------------------------------- |
| ✅ Step 1  | Make sure **fax field UI** allows multiple fax input rows (already done if `AddFax()` exists).        |
| ✅ Step 2  | Ensure `Saverefdoc()` logic **loops through fax fields**, appends with `$$`, and saves in `txtfaxno`. |
| ✅ Step 3  | Update server-side (Java/Servlet) to persist `txtfaxno` to `referring_doctor_fax_number`.             |
| 🔁 Step 4 | Update **fax sending module** (broadcasting) to split string by `$$` and send to **each number**.     |

#### 💡 Example Final Stored Format:

```text
1235678999$$7539512584$$9999999999
```

#### 💬 Business Logic Required:

* Wherever fax is being **sent**, loop like:

```java
String[] faxList = faxNumber.split("\\$\\$");
for(String fax : faxList){
    sendFaxTo(fax);
}
```

---

## 📦 Final Deliverables Checklist

| Task                                                 | Status                      |
| ---------------------------------------------------- | --------------------------- |
| Confirm `referring_doctor` is source table ✅         | ✅                           |
| Analyze how multiple phone numbers are stored ✅      | ✅ (via `$$`)                |
| Update fax number logic to mirror phone no. ✅        | ✅                           |
| Backend fix for DB size limit (VARCHAR → 100) ✅      | ✅                           |
| Ensure fax-sending logic handles multiple numbers 🔁 | **(To be checked/updated)** |

---

## ✅ **Client Request**

> **Dr. Gaffney** wants the system to support **multiple fax numbers** per referring physician and ensure referrals are faxed to **all of them simultaneously**.

---

## ✅ PHASE 1: HOW 'REFERRING PHYSICIAN' IS STORED

### 🔹 Storage Table:

* **Table:** `referring_doctor`
![Screenshot from 2025-06-30 10-01-55](https://github.com/user-attachments/assets/aff7656a-04dd-4c85-b808-1edbf139323b)

### 🔹 Add / Update / Delete:

| Action     | Code Flow                                                                                                                 |
| ---------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Add**    | Triggered via `Saverefdoc()` → `saveDoctor()` → `RefdocCategoryConf.java (FromSave = 1, ComboSaveId = -1)` → INSERT query |
| **Update** | Same as above, but ComboSaveId is NOT -1 → UPDATE query                                                                   |
| **Delete** | `actionType = DelRefDoc` → sets `referring_doctor_isactive = false`                                                       |
| **Fetch**  | `FromRP = 1` → SELECT query using `referring_doctor_uniqueid`                                                             |

---

## ✅ PHASE 2: PHONE NUMBER FLOW

### 🔹 UI Behavior:

* Phone numbers are entered as 3-part inputs:

  * `txtphoneext1`, `txtphoneext2`, `txtphoneext3`
* Add more using ➕ icon → JS: `AddContact(this)`
* Remove using ❌ icon → JS: `deleteNumber(this)`

### 🔹 Backend Handling:

* In `Saverefdoc()`:

  * Each phone number is joined (10 digits), then all are concatenated using `$$`
* Stored into DB field:

  * `referring_doctor_phoneno`

### 🔹 Example Format in DB:

```
1234567890$$9876543210
```

---

## ✅ PHASE 3: CURRENT FAX FLOW

### 🔹 Current Limitation:

* Only one fax number was allowed
* `referring_doctor_fax_number` column had `VARCHAR(25)` (already updated by you to `VARCHAR(100)` ✅)

### 🔹 Old Code (already in `Saverefdoc()`):

* Like phone number, fax is input in 3 parts: `txtfax{id1}`, `txtfax{id2}`, `txtfax{id3}`
* These are joined and validated
* All fax numbers concatenated using `$$` → stored into `txtfaxno`

### 🔹 ✅ What You’ve Already Fixed:

* JS supports multiple fax numbers (`faxValue += fullFax + "$$";`)
* DB field size is increased
* Field is written to: `referring_doctor_fax_number`

### 🔹 Example Format:

```
1235678999$$7539512584$$9999999999
```

---

## ✅ PHASE 4: TO-DO — BROADCAST TO ALL FAX NUMBERS

### 🔧 You need to:

#### 1. 🔍 **Locate the referral fax-sending logic**

* File will be something like: `ReferralService.java`, `FaxService.java`, or part of a servlet/controller class.
* Look for usage of `referring_doctor_fax_number` in backend.

#### 2. ✂️ **Split fax numbers by `$$` before sending**

```java
String faxCombined = resultSet.getString("referring_doctor_fax_number"); // Already $$ joined
if(faxCombined != null && !faxCombined.isEmpty()) {
   String[] faxArray = faxCombined.split("\\$\\$");
   for(String faxNumber : faxArray) {
       sendFax(faxNumber.trim(), ...); // Use your existing sendFax method here
   }
}
```

#### 3. ✅ **Ensure Fax History, Logs, or UI reflect multiple sends if needed**

* If there's a status for "fax sent", you may want to log one for each number.

---

## 🧪 TEST PLAN (UAT)

| Test                    | Input                             | Expected Result                                                   |
| ----------------------- | --------------------------------- | ----------------------------------------------------------------- |
| Add Referring Physician | 3 phone numbers and 3 fax numbers | DB stores phone in `phonenumber$$...` and fax in `faxnumber$$...` |
| Save and reload         | Existing physician                | UI splits them into 3-part boxes per row                          |
| Send Referral           | Fax field has 3 numbers           | All 3 receive the fax                                             |
| Try invalid fax         | <10 digits or non-numeric         | JS blocks submission with alert                                   |
| Delete one fax from UI  | Fewer fax numbers on save         | DB updated correctly                                              |

---


# 📄 Referring Physician – Phone & Fax Number Logic (UI/JS)

### 📁 Location: `RefdocCategoryConf.jsp`, `RefdocCategoryConf.js`

---

## ✅ Purpose

This module handles dynamic **input, validation, and aggregation** of multiple phone and fax numbers for a referring physician in:

> **Configure > General Practice Settings > Referring Physician**

The UI allows adding **multiple contact numbers** dynamically and storing them in a `$$`-separated format for backend submission.

---

## 🧩 Features

### 📱 **Phone Number Input Handling**

* Input format split into 3 parts:

  ```
  [Area Code] - [Prefix] - [Line Number]
  ```

  e.g. `123 - 456 - 7890`

* Each phone number occupies **3 input fields**, with the same `name="txtphoneext"`.

* **Add Phone** (`+`) → Calls `AddContact(this)`

* **Delete Phone** (`🗑️`) → Calls `deleteNumber(this)`

* On change/input: Moves focus, validates, marks form as dirty.

#### 🧮 Backend Storage

* Phone numbers are concatenated into a single hidden input:

  ```html
  <input type="hidden" name="txtphone" value="">
  ```
* Format: `1234567890$$9876543210`

---

### 📠 **Fax Number Input Handling**

* Same 3-part split as phone numbers:

  ```
  [Area Code] - [Prefix] - [Line Number]
  ```

* Each fax number uses 3 inputs named `txtfaxext`.

* **Add Fax** (`+`) → Calls `AddFax(this)`

* **Delete Fax** (`🗑️`) → Calls `deleteFax(this)`

#### 🧮 Backend Storage

* Fax numbers stored in:

  ```html
  <input type="hidden" name="txtfaxno" value="">
  ```
* Format: `9998887777$$6665554444$$...`

---

## ⚙️ JavaScript Functions

### 🔄 **Phone Logic**

| Function            | Role                                         |
| ------------------- | -------------------------------------------- |
| `AddContact(obj)`   | Adds new phone row with 3-part inputs        |
| `deleteNumber(obj)` | Deletes a phone input row and re-indexes IDs |
| `movetoNext1()`     | Auto-focus next field after max length       |
| `validateNum1()`    | Validates input for numeric-only             |
| `MakeDirty()`       | Marks form as modified (unsaved changes)     |

---

### 🔄 **Fax Logic**

| Function           | Role                                       |
| ------------------ | ------------------------------------------ |
| `AddFax(obj)`      | Adds new fax row with 3-part inputs        |
| `deleteFax(obj)`   | Deletes a fax input row and re-indexes IDs |
| `movetoNext1Fax()` | Moves to next input part automatically     |
| `validateNum1()`   | Validates fax parts for numeric-only       |
| `MakeDirty()`      | Same as above, tracks unsaved changes      |

---

## ⚠️ Constraints

| Type        | Constraint                                              |
| ----------- | ------------------------------------------------------- |
| Max Inputs  | 10 phone numbers, 10 fax numbers                        |
| Validation  | Only 10-digit numeric values accepted                   |
| Storage     | All numbers stored in a single field, delimited by `$$` |
| UI Behavior | Auto-focus after each segment is filled                 |

---

# `Saverefdoc()` Function Overview

## 📍 Location

**File:** `RefdocCategoryConf.jsp`
**Function:** `Saverefdoc()`

---

## 🧭 Purpose

The `Saverefdoc()` function is triggered during **Referring Physician Save**. It performs the following tasks:

* Collects and validates **Phone** and **Fax** numbers.
* Concatenates multiple numbers into a single string separated by `$$`.
* Sets hidden inputs to be submitted to the backend.
* Performs validations such as:

  * **10-digit rule**
  * **Digit-only checks**
  * **Zip Code validity**
  * **NPI duplicate check**

---

## ✅ Step-by-Step Breakdown

### 1. 🔁 **Phone Number Collection & Validation**

#### 🔹 Loop Over All Phone Rows

```js
for (var i = 0; i <= count; i++) {
    var phoneVal = txtphoneext1 + txtphoneext2 + txtphoneext3;
}
```

#### 🔹 Validations

* Check if each combined phone number:

  * Has exactly **10 digits**
  * Contains only digits
* Alert user if any rule fails

#### 🔹 Store Final Output

```js
document.forms[0].txtphone.value = phoneNumbers;
```

🔹 **Format Used for Storage:**

```text
<10-digit-phone1>$$<10-digit-phone2>$$<10-digit-phone3>
```

---

### 2. 📠 **Fax Number Collection & Validation**

#### 🔹 Query All Fax Fields

```js
var faxInputs = document.querySelectorAll("input[name='txtfaxext']");
```

#### 🔹 Loop by 3s and Join Parts

Each fax number is a triplet:

```js
var fullFax = part1 + part2 + part3;
```

#### 🔹 Validations

* All 3 parts together should:

  * Form **10 digits**
  * Be all numeric
* Alerts shown for invalid entries

#### 🔹 Store Final Output

```js
document.forms[0].txtfaxno.value = faxNumbers;
```

🔹 **Format Used for Storage:**

```text
<10-digit-fax1>$$<10-digit-fax2>$$<10-digit-fax3>
```

---

### 3. 📦 **Hidden Field Assignments**

Both **txtphone** and **txtfaxno** are `<input type="hidden">` and will be used by the backend to parse multiple numbers.

---

### 4. 📮 **Zip Code Validation**

```js
var zip = document.forms[0].chr_txtzip.value;
```

* Accepts only:

  * **5 digits** or
  * **9 digits**
* Removes special characters before validation

---

### 5. 🧠 **Duplicate Check for NPI & Referring Doctor**

#### NPI

```js
checkNPI();
```

* If NPI already exists, **stop save**.

#### Referring Doctor Duplicate

```js
checkDuplicateRefDoc();
```

* Validates using:

  * First + Last Name (for individuals)
  * Practice Name (for groups)

---

### 6. 💾 **Final Save Trigger**

```js
saveDoctor();
```

* Executes only if:

  * All validations pass
  * No duplicates found
  * Required fields are filled

---

## 🧼 Notes

| Feature           | Logic Summary                                                              |
| ----------------- | -------------------------------------------------------------------------- |
| Phone Save        | Split into triplets, validated, stored with `$$` separator                 |
| Fax Save          | Similar to phone logic, fully updated to support multi-fax                 |
| Max Inputs        | UI supports up to 10 phone/fax entries                                     |
| Error Handling    | Alerts used with focus fallback to invalid fields                          |
| Enhancement Ready | Fax logic now matches phone logic in scalability, UI behavior, and storage |

---

# 📄 `assignElements(respObj)` – Referring Physician Auto-Population

## 📍 Location

**File:** `RefdocCategoryConf.jsp`
**Function:** `assignElements(respObj)`

---

## 🧭 Purpose

This function **auto-populates** the form fields (Phone, Fax, Name, Address, etc.) for a **selected referring physician** using data received in `respObj`. It also manages:

* Dynamic rendering of **Phone** and **Fax** rows.
* Clean-up of previously added rows (avoids duplication).
* UI state control: disables the delete icon for the **first (static) row** of Phone and Fax.

---

## ✅ Step-by-Step Logic

### 1. 🔄 Assign Basic Field Values

Populates basic form fields like:

* `firstName`, `lastName`, `practiceName`
* `address`, `city`, `state`, `zip`
* `notes`, `directemail`

Escapes the `~` character by replacing it with `"` for consistency.

---

### 2. 🧹 Clear Previous Phone/Fax Rows

To avoid UI duplication:

```js
if (i !== staticPhoneRowIndex && i !== staticFaxRowIndex) {
    table.deleteRow(i);
}
```

Only **static rows** (the initial row in the table) are preserved.

---

### 3. ☎️ Populate Phone Numbers

* Splits `phoneno` using `$$`
* First phone number goes into the **static row**
* Subsequent phone numbers are added as **dynamic rows**
* Each phone number is split into 3 fields: `3 + 3 + 4` digits
* A delete icon (`deletenum`) is added to each dynamic row
* Adds `Add Phone` button to the last phone row

🔒 **Delete icon is hidden on the first row** if more than one phone number exists:

```js
if (phoneNumbers.length > 1) {
    document.getElementById("deletePhoneStatic").style.display = "none";
}
```

---

### 4. 📠 Populate Fax Numbers

* Similar logic as phone numbers:

  * Split into 3 fields: `3 + 3 + 4` digits
  * First fax goes into static row
  * Dynamic rows are added for the rest
  * Each gets a `deletefax` icon
  * Adds `Add Fax` button to last row

🔒 **Delete icon is hidden on the first row** if multiple faxes exist:

```js
if (faxNumbers.length > 1) {
    document.getElementById("deleteFaxStatic").style.display = "none";
}
```

---

## 🧼 Notes

| Feature                          | Behavior                                           |
| -------------------------------- | -------------------------------------------------- |
| Phone/Fax Limit                  | Up to 10 entries allowed each                      |
| Static Row                       | Always used for first phone/fax entry              |
| Delete Icon on First Row         | Hidden when dynamic rows exist, shown otherwise    |
| Event Handlers                   | Assigned for `oninput`, `onchange`, and validation |
| `rowcount` Management            | Ensures correct tracking for dynamic row indexing  |
| Add Buttons (`addcon`, `addfax`) | Appended to the last respective row only           |

---
### 🧪 QA / Verification Steps

1. Navigate to Referral Setup screen.
2. Add three fax numbers using the UI inputs.
3. Save the referral.
4. Query DB:

   ```sql
   SELECT referring_doctor_fax_number FROM referring_doctor
   WHERE referring_doctor_uniqueid = 1411;
   ```

   Ensure all 3 fax numbers are stored correctly, separated by `$$`.

---

### ✅ Status

* **Database Fix Done**
* **Code Verified**
* **Ready for Testing / UAT**

---



## ✅ Steps with Screenshots

---

### 🔹 **Step 1: Navigate to Referring Physician Settings**

> **Menu Path:**
> `Configure → General Practice Settings → Referring Physician`

📸 **Screenshot:**

![Step 1](https://github.com/user-attachments/assets/2c01540e-180a-4785-94b4-aa894735f888)
![Step 1-UI](https://github.com/user-attachments/assets/1a469a93-c440-46ca-ac64-fe11b72771a2)

---

### 🔹 **Step 2: Click the Picker Icon**

> Click the picker (magnifying glass) to browse the list of existing referring physicians.

📸 **Screenshot:**

![Step 2](https://github.com/user-attachments/assets/3da261f1-8636-402d-86db-aa9540c79a08)

---

### 🔹 **Step 3: Select a Referring Physician**

> Choose a referring physician to view/edit their details.

📸 **Screenshot:**

![Step 3](https://github.com/user-attachments/assets/e6f51e81-090c-4f25-ad08-960547fa71d0)

---

### 🔹 **Step 4: View DB Data Before Editing**

> Corresponding **database entry** for selected physician before adding phone/fax values.

📸 **Screenshot:**

![Step 4-1](https://github.com/user-attachments/assets/4a8aacf9-9d5c-4e65-be94-8824e06c3097)
![Step 4-2](https://github.com/user-attachments/assets/be301d19-7636-4fc0-8e01-7856469864ab)

---

### 🔹 **Step 5: Add Phone and Fax Numbers**

> Input up to **3-part segmented phone and fax numbers**, then **click Save**.

📸 **Screenshot:**

![Step 5](https://github.com/user-attachments/assets/130fede8-04ab-45f2-8866-825f970c3516)

---

### 🔹 **Step 6: Verify DB After Save**

> Check that the phone and fax values are **stored correctly** in DB, separated by `$$`.

📸 **Screenshot:**

![Step 6](https://github.com/user-attachments/assets/c2e9026c-0835-4a85-8aa3-9bda1e590bbb)

---

### 🔹 **Step 7: Delete One Entry, Save, and Verify**

> Remove one fax or phone number using ❌ icon, then save the form again.
> Confirm the DB update reflects the deleted value.

📸 **Screenshots:**

![Step 7-1](https://github.com/user-attachments/assets/b628302b-4713-459a-903b-49cd02558d59)
![Step 7-2](https://github.com/user-attachments/assets/e408874d-0e50-46fd-81b5-822102b24f48)
![Step 7-3](https://github.com/user-attachments/assets/dceadb90-d1e6-4cc1-bf7b-8217957c2fb5)

---



