## 📄 Day 52 – July 9, 2025 – Wednesday

### 🔧 Feature: Practice Settings – Enable Custom XML Desktop View & Switch Link


#### 📌 Before Saving (GET Request)

```json
{
	"GET": {
		"scheme": "http",
		"host": "localhost:8080",
		"filename": "/GlaceMaster/jsp/chart/leafs/PractiseGeneral.Action",
		"remote": {
			"Address": "127.0.0.1:8080"
		}
	}
}
```

* The page is being **loaded in view mode**, without user interaction.
* No query parameters are sent.
* You're seeing the **default settings**, possibly as unchecked or uninitialized.

---

#### 📌 User Action

* The user **enables**:

  * ✅ "Do you want to enable default custom xml desktop view?"
  * ✅ "Do you want to enable custom xml desktop switch link?"
* Then clicks **Save** (which triggers a POST request).

---

#### 📌 After Saving (POST Request)

```json
{
	"POST": {
		"scheme": "http",
		"host": "localhost:8080",
		"filename": "/GlaceMaster/jsp/chart/leafs/PractiseGeneral.Action",
		"query": {
			"flagLink": "1",
			"updatePOSactive": "0",
			"insVerificationactive": "1"
		},
		"remote": {
			"Address": "127.0.0.1:8080"
		}
	}
}
```

* The form submitted data:

  * `"flagLink": "1"` → Corresponds to **“Enable custom xml desktop switch link”**.
  * `"updatePOSactive": "0"` and `"insVerificationactive": "1"` → Other settings in the same form.
* The data is now persisted in the **`initial_settings`** table.

---

### 🧮 **Database Confirmation**

```sql
select * from initial_settings where initial_settings_option_name='Do you want to enable custom xml desktop switch link?';
```

**Output:**

* `initial_settings_option_value: 1` ✅ → Indicates it is enabled
* `initial_settings_view_type: check` → A checkbox-type input
* `initial_settings_visible: t` → This setting is currently **visible** in the UI

So everything worked as expected:

* The UI change triggered a POST request with the correct flags.
* The DB table `initial_settings` reflects the enabled status with value `1`.

---

* Your **POST payload**:

```json
{
  "flagLink": "1",
  "updatePOSactive": "0",
  "insVerificationactive": "1"
}
```

* And optional form fields like:

```http
recsToUpdate=chk10031,chk10032,...
chk10031=1
chk10032=
```

---

## 🔁 **Step-by-Step Execution Flow**

### ✅ 1. **Session and DB Utils Initialization**

```java
HttpSession session = request.getSession(true);
dbUtils = new DataBaseUtils(session.getAttribute("connectString").toString());
```

→ **Database connection is established** using session’s `connectString`.

---

### ✅ 2. **Processing Field Updates (`recsToUpdate`)**

```java
String recsToUpdate = request.getParameter("recsToUpdate"); // e.g., chk10031,chk10032
```

Loop through each:

```java
for (int i = 0; i < recsToUpdateArr.length; i++) {
  optionName = recsToUpdateArr[i];       // e.g., "chk10031"
  optionId = optionName.substring(3);    // e.g., "10031"
  optionValue = request.getParameter(optionName); // e.g., "1" or ""
```

➡️ This block checks if the prefix is `chk`:

```java
if (optionName.substring(0, 3).equals("chk")) {
    if (optionValue.equals("")) {
        updateQry = "update initial_settings set initial_settings_option_value = '0' where initial_settings_option_id = " + optionId;
    } else {
        updateQry = "update initial_settings set initial_settings_option_value = '1' where initial_settings_option_id = " + optionId;
    }
    dbUtils.execute(updateQry);
}
```

📌 **Result**:

* `chk10031=1` → Enables the setting → DB sets `option_value='1'` for `option_id=10031`
* `chk10032=` (unchecked) → DB sets `option_value='0'` for `option_id=10032`

---

### ✅ 3. **Insurance Setting (optional)**

```java
String insuranceNameId = request.getParameter("insuranceNameId"); // If present
```

If it's present, it updates:

```sql
UPDATE initial_settings SET initial_settings_option_value = '<insuranceNameId>' WHERE initial_settings_option_id = 20;
```

---

### ✅ 4. **Special Case: flagLink = 1**

You passed:

```http
flagLink=1
updatePOSactive=0
insVerificationactive=1
```

This triggers:

```java
if (flagLink == 1) {
  String updateQrylink = "update initial_settings set initial_settings_option_type='0' where initial_settings_option_name ilike 'Update POs'";
  dbUtils.execute(updateQrylink);

  String updateQrylink1 = "update initial_settings set initial_settings_option_type='1' where initial_settings_option_name ilike 'Ins Verification Form'";
  dbUtils.execute(updateQrylink1);
}
```

📌 **Result**:

* **Update POS setting** → updates `option_type = '0'`
* **Ins Verification Form setting** → updates `option_type = '1'`

⚠️ **Note**: These values go into `initial_settings_option_type`, not `option_value`.

---

### ✅ 5. **Query to Refresh Settings in UI**

```sql
SELECT billing_config_table_lookup_desc, ...
FROM billing_config_table
INNER JOIN initial_settings ON billing_config_table_config_id = initial_settings_option_type
WHERE billing_config_table_lookup_id = 105
AND initial_settings_visible IS TRUE
AND initial_settings_option_type = 2
ORDER BY ...
```

📌 **Purpose**: Refetches settings of type `2` (checkboxes) for re-rendering the page.

---

### ✅ 6. **Log & Return**

Logs audit (currently commented) and finally returns:

```java
return "PractiseGeneral.Screen";
```

---

## ✅ **Final Outcome in Your Case**

| Setting                   | DB Column      | Value Set | Notes                                                |
| ------------------------- | -------------- | --------- | ---------------------------------------------------- |
| `chk10031=1`              | `option_value` | `1`       | Checkbox enabled                                     |
| `chk10032=`               | `option_value` | `0`       | Checkbox disabled                                    |
| `flagLink=1`              | (trigger)      | —         | Triggers below updates                               |
| `updatePOSactive=0`       | `option_type`  | `0`       | For setting with name like `'Update POs'`            |
| `insVerificationactive=1` | `option_type`  | `1`       | For setting with name like `'Ins Verification Form'` |

---

```sql
SELECT 
    billing_config_table_lookup_desc,
    initial_settings_option_id AS optionid,
    initial_settings_option_type AS optiontype,
    initial_settings_option_name_desc AS optionname,
    initial_settings_option_value AS optionvalue,
    initial_settings_table_row_no AS rowno,
    initial_settings_table_col_no AS colno,
    initial_settings_table_colspan AS colspan,
    initial_settings_textbox_size AS txtsize,
    initial_settings_view_type AS viewtype,
    initial_settings_option_source AS optionsource
FROM billing_config_table
INNER JOIN initial_settings 
    ON billing_config_table_config_id = initial_settings_option_type
WHERE 
    billing_config_table_lookup_id = 105 
    AND initial_settings_visible IS TRUE 
    AND initial_settings_option_type = 2
ORDER BY 
    initial_settings_option_type,
    initial_settings_table_row_no,
    initial_settings_table_col_no;
```

---

### ✅ To Specifically View:

1. `"Do you want to enable default custom xml desktop view?"`
2. `"Do you want to enable custom xml desktop switch link?"`

---

## 🔍 Options:

### Option 1: **Filter for specific descriptions**

To see only the rows matching **those two setting descriptions**, modify your query with `ILIKE` filters:

```sql
SELECT 
    billing_config_table_lookup_desc,
    initial_settings_option_id AS optionid,
    initial_settings_option_type AS optiontype,
    initial_settings_option_name_desc AS optionname,
    initial_settings_option_value AS optionvalue,
    initial_settings_table_row_no AS rowno,
    initial_settings_table_col_no AS colno,
    initial_settings_table_colspan AS colspan,
    initial_settings_textbox_size AS txtsize,
    initial_settings_view_type AS viewtype,
    initial_settings_option_source AS optionsource
FROM billing_config_table
INNER JOIN initial_settings 
    ON billing_config_table_config_id = initial_settings_option_type
WHERE 
    billing_config_table_lookup_id = 105 
    AND initial_settings_visible IS TRUE 
    AND initial_settings_option_type = 2
    AND (
        initial_settings_option_name_desc ILIKE '%custom xml desktop view%' OR
        initial_settings_option_name_desc ILIKE '%custom xml desktop switch link%'
    )
ORDER BY 
    initial_settings_option_type,
    initial_settings_table_row_no,
    initial_settings_table_col_no;
```

---

### Option 2: **Find All Matching "custom xml" Settings**

If you're not sure about the exact text, this broader query helps:

```sql
SELECT 
    initial_settings_option_id AS optionid,
    initial_settings_option_name_desc AS optionname,
    initial_settings_option_value AS optionvalue
FROM initial_settings
WHERE 
    initial_settings_option_name_desc ILIKE '%custom xml%'
    AND initial_settings_visible IS TRUE;
```

---

### 🟩 Sample Expected Output

| optionid | optionname                                             | optionvalue |
| -------- | ------------------------------------------------------ | ----------- |
| 10030    | Do you want to enable default custom xml desktop view? | 1           |
| 10031    | Do you want to enable custom xml desktop switch link?  | 1           |

---



## **JSP page**. Based on the structure, you're working with a **dynamic settings configuration UI**

---

### ✅ **Objective**

This JSP dynamically renders a table of general settings (like checkboxes, dropdowns, textboxes) from the database using the `initial_settings` and `billing_config_table`.

---

## 🔁 **Full Flow: From DB to UI**

---

### 1. **Backend (Java Action)**

* Runs a **query** like this:

```sql
SELECT
  billing_config_table_lookup_desc,
  initial_settings_option_id AS optionid,
  initial_settings_option_type AS optiontype,
  initial_settings_option_name_desc AS optionname,
  initial_settings_option_value AS optionvalue,
  initial_settings_table_row_no AS rowno,
  initial_settings_table_col_no AS colno,
  initial_settings_table_colspan AS colspan,
  initial_settings_textbox_size AS txtsize,
  initial_settings_view_type AS viewtype,
  initial_settings_option_source AS optionsource
FROM billing_config_table
JOIN initial_settings
  ON billing_config_table_config_id = initial_settings_option_type
WHERE billing_config_table_lookup_id = 105
  AND initial_settings_visible IS TRUE
  AND initial_settings_option_type = 2
ORDER BY initial_settings_option_type, initial_settings_table_row_no, initial_settings_table_col_no;
```

* The result is stored in a `Vector` called:

  ```java
  Vector VecGeneral = (Vector) request.getAttribute("SelectQuery");
  ```

---

### 2. **UI (JSP Page)**

* Iterates over `VecGeneral`:

  ```jsp
  for (int Index = 0; Index <VecGeneral.size(); Index++) {
      Hashtable HashGeneral = (Hashtable) VecGeneral.get(Index);
      ...
  }
  ```

* Renders each item as per `viewtype`:

| `viewType` | Input Type Rendered | Method Called             |
| ---------- | ------------------- | ------------------------- |
| `text`     | TextBox             | `Input.drawTextBox(...)`  |
| `combo`    | Select/Dropdown     | `Select.draw(...)`        |
| `check`    | Checkbox            | `Input.drawCheckBox(...)` |
| `number`   | Number TextBox      | `Input.drawTextBox(...)`  |
| `image`    | Image Picker Link   | Custom HTML + JS          |

* For each control, event like `onChange` or `onClick` triggers:

  ```js
  addIdList("chk123") or addIdList("txt123")
  ```

---

### 3. **JavaScript Interaction**

#### ✅ Track Changes

Each time a control changes:

```js
addIdList("controlId")
→ adds controlId to `ids`
→ sets `FormChanged = true`
```

#### ✅ Combine Selected Data

Before submitting:

```js
combine()
→ prepares a formatted string from multi-selects
→ updates `selectedDrs` hidden input
```

---

### 4. **Submit Flow**

When clicking the **Save** icon (image button):

```html
<img ... onClick="javascript:saveGeneral();patientbookLink();">
```

#### `patientbookLink()` function:

* Confirms update
* Sets `updatePOS` and `insVerify` values
* If `FormChanged == true`, submits form with:

  * `recsToUpdate`
  * `insuranceNameId`
  * `selectedDrs`
  * `selectMultiple`

The form is submitted to:

```jsp
PractiseGeneral.Action?flagLink=1...
```

---

## ✅ **Summary Flow Chart**

```
initial_settings + billing_config_table (SQL)
        ↓
     Java Action
        ↓
  request.setAttribute("SelectQuery", result)
        ↓
       JSP
        ↓
 Iterate Vector<VecGeneral>
        ↓
  Dynamically draw controls (textbox, combo, checkbox)
        ↓
User interacts → JS tracks → FormChanged = true
        ↓
User clicks Save (img button)
        ↓
JS function → collects values → sets hidden inputs
        ↓
Form submits to PractiseGeneral.Action
        ↓
Server processes updated values
```

---

## ✅ **Next Steps: Show the Two Checkbox Options**

If you're not seeing:

* ✅ **"Do you want to enable default custom xml desktop view?"**
* ✅ **"Do you want to enable custom xml desktop switch link?"**

### Do This:

1. ✅ Confirm they exist in `initial_settings` table.

   ```sql
   SELECT * FROM initial_settings 
   WHERE initial_settings_option_name_desc ILIKE '%custom xml%';
   ```

2. ✅ Ensure:

   * `initial_settings_option_type = 2`
   * `initial_settings_view_type = 'check'`
   * `initial_settings_visible = true`
   * `billing_config_table_lookup_id = 105`

3. ✅ Restart the page.

---
