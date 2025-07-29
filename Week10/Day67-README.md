# Headache Questionnaire - Date Picker Positioning Fix

## 📌 Issue

In the **Headache Questionnaire (HeadacheQuestionnaireEaseTN.xml)** form, the **bottom date picker** was **partially hidden**, especially when the calendar appeared near the bottom of the screen. This was causing a poor user experience, preventing users from selecting dates properly.

---

## 🔍 Root Cause

The positioning logic in the `popUpCalendar` function (located in `ProgressNotesHelper.js`) failed to account for the **calendar's height** when the triggering element was near the bottom of the page.

### Problem Code:

```js
if ((tempY + CalendarOffHt) > FullDocHeight) {
	var intCalendarTop = tempY - CalendarOffHt;
	if (intCalendarTop <= 0 ) 
		crossobj.top = '0px';
	else 
		crossobj.top = intCalendarTop + 100 + 'px'; // <== 100px adjustment, but might overflow upwards
}
```

* This logic was trying to push the calendar up if it overflowed below the page.
* But it added **`+100` px** instead of subtracting margin, which could make the calendar appear **even further off-screen**.

---

## ✅ Solution

## **Aggressive Top Padding Adjustment**

Alternatively, a slightly more aggressive vertical shift was applied (e.g., `-150`px), with debugging `alert()` messages to test and validate:

```js
var CrossObjTop = parseInt(crossobj.top);
var CalendarOffHt = parseInt(calendarobj.offsetHeight);
if ((tempY + CalendarOffHt) > FullDocHeight) {   					
	var intCalendarTop = tempY - CalendarOffHt - 150; // give space
	if (intCalendarTop <= 0) { 
		alert("in if");
		crossobj.top = '0px';
	} else {
		alert("intCalendarTop: " + intCalendarTop);
		crossobj.top = intCalendarTop + 'px'; // else here also we can reduce 150 to make it correct 
	}
}
```
---

## 🗂️ File References

* **XML Trigger**: `HeadacheQuestionnaireEaseTN.xml`

  ```xml
  onclick="PopDateNew('xml_element2_HeadacheQuestionnaireEaseTN')"
  ```

* **JS Function**: `ProgressNotesHelper.js`

  * `PopDateNew(...)`
  * `popUpCalendar(...)` – This is the main focus of the fix.
  * `setPopCalendarClientWidthHeight()`

---

## 🧪 Test Steps

1. Scroll to the **bottom** of the page.
2. Click on the **date field** near the page bottom.
3. Ensure the calendar is **fully visible** and not cut off.
4. Confirm date selection and rendering is working as expected.

---

## ✅ Status

**✅ FIXED**: Calendar popup now auto-adjusts its vertical position if it risks being cut off by the bottom of the screen.

---

