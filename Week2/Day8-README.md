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



---


## 🔍 Implementation Details

### 📄 Modified Files

* `InsuranceDesktopView.java`
* `InsuranceDesktopView.ui.xml`

### ✅ Key Changes

#### 1. **Created Method: `setModalHeight(int insType)`**

This method dynamically sets the height of the modal dialog element based on the `insType`.

```java
private void setModalHeight(int insType) {
    Element modalElement = getDomElementById("insuranceMyTaskCommonModal");

    if (modalElement != null) {
        Element modalDialogElement = modalElement.getFirstChildElement();
        if (modalDialogElement != null) {
            String heightStyle = (insType == 1 || insType == 2 || insType == 3) ? "94%" : "550px";
            modalDialogElement.setAttribute("style",
                "transition: height 0.3s ease;" +
                "height:" + heightStyle + ";" +
                "width: 75%;" +
                "margin-top: 20px;" +
                "margin-bottom: 10px;");
        }
    }
}
```

#### 2. **Switch Case Block – Integration**

Within the `switch (insType)` block (that handles drawing different insurance info panels), the following line was added for cases 1 through 8:

```java
setModalHeight(insType);
```

#### 3. **Modal Visibility Handling**

```java
Element modalElement = getDomElementById("insuranceMyTaskCommonModal");
if (modalElement != null) {
    modalElement.setClassName("modal show");
} else {
    GWT.log("Modal element with ID 'insuranceMyTaskCommonModal' not found in onDataSubmitResponse");
}
```

---

## 📌 Notes

* Modal height now dynamically adjusts based on insurance type:

  * `insType 1–3`: Taller modal (`94%`) for potentially more content.
  * `insType 4–8`: Default modal height (`550px`).
* This ensures the **"OK" button stays visible and appropriately positioned** across different insurance panels.
* No change for `insType 9` as it's unhandled (remains unchanged).

---

## ✅ Status

**Completed:** ✔️

### 📸 **Final Output**

The modal correctly resizes based on content and the "OK" button remains well-positioned:

![Screenshot from 2025-05-29 22-55-06](https://github.com/user-attachments/assets/a231e38a-3428-41ab-8042-4be2c762b05c)



