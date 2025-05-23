
## 📝 **Day 4**

**📅 Date:** 23/05/2025
**🔧 Task:** Investigation – Source of Menu Items in Main Navigation
**🗂 Module:** Main Menu / Navigation


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

