# 🐞 Flash of Old Template Data Bug – Root Cause & Fix

## 📌 Issue Summary

When a user:

1. Checks out a **completed encounter** ✅
2. Creates a **new Office Visit** ✅
3. Adds a **new template** under the new encounter ✅
4. Clicks a **tab** (e.g., HPI, Plan, etc.) under the new template...

> The **newly opened template momentarily displays data from the previously opened template**, for \~1 second, **before the correct data loads**.

---

## 🧠 Root Cause

* The navigation (`gotoPlace(new XMLPlace(bean))`) triggers **before** the new XML data is fetched.
* The **old template view remains mounted** and re-renders with the **previous bean's data**, resulting in a **brief UI flash**.
* `getXMLFile()` is asynchronous and takes time to load the new template.
* The UI **does not clear the old state** before new data is available.

---

## ✅ Resolution

### ✔️ Step 1: Clear the View Immediately in `start()`

Modified the `start()` method of `XMLActivity.java` to **reset the view** before triggering data fetch:

```java
@Override
public void start(AcceptsOneWidget panel) {
	Window.alert("start method called");
	view.clearPanel();  // 🛑 Clear old data before new fetch
	view.setPresenter(this);
	panel.setWidget(view.asWidget());
	bindEvent();
	getXMLFile();       // ⏳ Asynchronous load of correct template
}
```

---

### ✔️ Step 2: Implemented `clearPanel()` in View

This method **hides old content** and shows a loading indicator:

```java
public void clearPanel() {
	dataPanel.clear(); // 🧹 Clear any lingering widgets
	dataPanel.getElement().getStyle().setDisplay(Display.NONE); // Hide content
	loadingXMLPanel.setVisible(true); // Show loading state
}
```

---

### ✔️ Step 3: Also Called `clearPanel()` on XML Tab Reload

Inside the XML event binding:

```java
if (event.getMode() == XMLEvent.GetXML) {
	setChartVariablesBean(event.getChartVariablesBean());
	view.clearPanel();       // 🛑 Prevent stale display
	getXMLFile();            // 🆕 Load fresh XML data
}
```

---

## ✅ Result

* ✅ **No more stale data flash** when switching templates.
* ✅ UI now shows **only the correct, updated template content**.
* ✅ Rendering logic is now **robust** against rapid navigation or back-to-back template loading.


### ✅ After Fix

> Clean template load with loading state — no flash of stale data.

> ![After Fix – Correct Data Load](https://github.com/user-attachments/assets/5868d9bd-b84a-4b0f-a59c-c13104db2de8)

> ![After Fix – Correct Data Load](https://github.com/user-attachments/assets/c9ed99f0-b101-4b3b-8df9-5afa7acfc779)


---

## 🔍 Debugging Aids Used

* Added `Window.alert()` and `GWT.log()` in key locations:

  * Before `gotoPlace()`
  * Inside `start()`, `clearPanel()`, `onFireXMLEvent()`

---

## 💡 Lessons Learned

* Always **clear previous state** before rendering new async content.
* Avoid **navigating with stale beans** or **reusing views** without a reset mechanism.
* Async data fetches must always be **preceded by a loading state** in UI-driven applications.

---
