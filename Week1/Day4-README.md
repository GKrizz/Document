# ✅ Day 4

**📅 Date:** 23/05/2025 <br>
**📝 Tasks Completed:**

1. [Investigation – Source of Menu Items in Main Navigation](#-investigation--source-of-menu-items-in-main-navigation)
2. [Flow Analysis – “New Patient” Navigation](#-flow-analysis--new-patient-navigation)

---

## 🔧 Investigation – Source of Menu Items in Main Navigation

**📂 Module:** Main Menu / Navigation

---

### 🔍 **Steps Performed**

1. **UI Inspection**

   * Inspected the page using browser DevTools.
   * Found that navigation links are rendered using the class: `MainMenuLink`.

2. **Codebase Search**

   * Searched for the keyword `MainMenuLink` in the codebase.
   * Located relevant logic in the file: `mainmenu.js`.

3. **Tracing the Flow**

   * In `mainmenu.js`, found references to a JSP file: `main.jsp`.

4. **Database Query Identified**

   * Inside `main.jsp`, found SQL query:

     ```sql
     SELECT menus_name FROM menu_config
     ```

5. **Database Verification**

   * ✅ **Local Database (`ciama_local`)**

     * Found table: `menus`
     * Verified that menu items (`menus_name`) are stored here.
     * **Note:** `menu_config` table **not present**.
   * ✅ **Next Release Database (`ciama_nextrelease`)**

     * Found table: `menu_config`
     * Contains detailed menu structure including child items: `menu_config_menuname`

---

### ✅ **Findings**

| Environment         | Table Used    | Contains                                         |
| ------------------- | ------------- | ------------------------------------------------ |
| `ciama_local`       | `menus`       | Top-level menu items                             |
| `ciama_nextrelease` | `menu_config` | Menu and sub-menu items (`menu_config_menuname`) |

* The `main.jsp` file relies on `menu_config` for retrieving menu items, which suggests newer environments (e.g., staging/production) are using this updated structure.
* Legacy/local environments still use the older `menus` table.

---

## 🔍 Flow Analysis – “New Patient” Navigation

**📂 Module:** Patient Registration / Navigation

---

### 🧭 **User Navigation Path**

> **Lists → Patients → New Patient**

---

### 🌐 **1. Initial Request**

* **Browser sends request to:**

  ```url
  http://localhost:8080/GlaceStage/jsp/PostLoginProcess.Action
  ```

---

### 🖥️ **2. Server-Side Handling**

* The framework identifies this request is mapped to:

  ```properties
  PostLoginProcess.Action = com.glenwood.hcare.actions.PostLoginProcessAction
  ```

* Inside this action class:

  * The `performAction()` method is executed.
  * It returns:

    ```java
    "QuickLogin.NextScreen"
    ```

---

### 🔁 **3. Action Mapping Resolution**

* The framework checks `ActionMappings.properties` and finds:

  ```properties
  QuickLogin.NextScreen = /jsp/main.jsp
  ```

✅ The request is **forwarded to:**

```url
/jsp/main.jsp
```

---

### 🧩 **4. UI Interaction – “New Patient”**

* On the **main.jsp** screen, clicking “New Patient” triggers a **JavaScript function** from:

  ```js
  mainmenu.js
  ```

* The JavaScript dynamically sends a **UI-level request** to:

  ```url
  http://localhost:8080/GlaceStage/jsp/patient/PatientBook.Action?option=1
  ```

#### 🎯 Breakdown

* `PatientBook.Action`: A mapped Java Action class
* `option=1`: Indicates action to **create a new patient**

---

### 🧠 **5. Detailed Backend Workflow**

1. Browser → `PostLoginProcess.Action`

2. Framework maps to → `PostLoginProcessAction` class

3. Executes `performAction()` → returns `"QuickLogin.NextScreen"`

4. Framework looks up mapping → `/jsp/main.jsp`

5. JSP loads UI with "New Patient" option

6. On click, `mainmenu.js` sends:

   ```url
   /jsp/patient/PatientBook.Action?option=1
   ```

7. Server routes this to the corresponding patient registration logic

8. The JSP (likely `/WEB-INF/jsp/patientRegistration.jsp`) generates and sends back HTML

---

### 📌 **Note on Internal Forwarding**

* The final JSP (e.g., `patientRegistration.jsp`) is executed **server-side**.
* The response is sent back to the browser.
* **URL in address bar remains:**

  ```url
  http://localhost:8080/GlaceStage/jsp/PostLoginProcess.Action
  ```

  ⏤ because this was an **internal forward**, not a redirect.

---

### ✅ **Summary Table**

| Step | Component                    | Description                                              |
| ---- | ---------------------------- | -------------------------------------------------------- |
| 1    | **Browser Request**          | `PostLoginProcess.Action` sent by browser                |
| 2    | **Java Class**               | Handled by `PostLoginProcessAction`                      |
| 3    | **Return Mapping**           | `"QuickLogin.NextScreen"` → `/jsp/main.jsp`              |
| 4    | **JS Trigger (mainmenu.js)** | Loads `PatientBook.Action?option=1`                      |
| 5    | **New Patient UI Load**      | Action renders patient registration screen (via JSP)     |
| 6    | **Internal Forward**         | Final JSP forwards response without changing browser URL |

---
