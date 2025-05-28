# **Day 8 - Task Summary**

**📅 Date:** 28-May-2025, **Wednesday**

### ✅ **Task: UI Modification – Dynamic Positioning of "OK" Button in Insurance Modal**

**Module:** Chart Center → Edit Demographics → Insurance Popup
**UI Framework:** Spring MVC + GWT

---

### 🔧 **Problem Statement**

When editing a patient’s insurance details in the popup modal, the **"OK"** button is statically placed. This causes it to appear awkwardly positioned if the **"Insurance Type"** dropdown is changed, leading to the form content resizing.

<table>
  <tr>
    <td align="center"><strong>Account info page </strong></td>
    <td align="center"><strong>Pop up </strong></td>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/9653d7b5-2f8a-423a-80de-5f7a1f8a379e" width="350"/>
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/016d2751-bdc0-4739-94df-c8e505e30e6d" width="350"/>
    </td>
  </tr>
</table>

---

## 🎯 **Scenario Description**

1. Navigate to the **Chart Center UI**.
2. Click the **three-dot "More" menu** next to any patient’s name.
3. Select **Edit Demographics** from the dropdown.
4. The form opens with **Personal Info** (Last Name, First Name, DOB, etc.).
5. Click the **Account Info** tab to open insurance-related details.
6. In the **Account Info** popup:

   * There is a field labeled **"Insurance Type:"** (dropdown).
   * At the bottom of this form, there is an **"OK"** button.
7. When the **Insurance Type** dropdown value changes, the UI content (e.g., tables or sections) may resize based on the selected option.

---

## 🛠️ **Task**

Modify the UI so the **"OK"** button:

* Always appears **just below the visible content**.
* Adjusts to changes in the **form/table height** caused by different selections in the **Insurance Type** dropdown.
* Does **not overlap** the content or float in a fixed position when the form size changes.

---

## ✅ **Expected Outcome**

| Interaction                            | Expected Behavior                                                     |
| -------------------------------------- | --------------------------------------------------------------------- |
| Select a new value in "Insurance Type" | If table height increases, the "OK" button moves downward accordingly |
| Resize or scroll the popup content     | The "OK" button stays aligned to the bottom of the content            |
| Static form with fewer rows            | The "OK" button remains just below the section                        |



