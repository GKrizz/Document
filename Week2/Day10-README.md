# 📅 Day 10 — May 30, 2025 (Friday)

## 🧩 Task Overview

### ✅ Objective:
Update the **"Print Appointment"** screen to include **After Hours** information in the header section for printed patient appointments.

---

## 🔄 User Flow to Access Feature

1. Navigate to **Scheduler**
2. Select date: **Thursday, May 29, 2025**
3. Choose **Doctor**: `Mark test1343`
4. Click **Select Day**
5. Scroll down and click the **green appointment box**
6. Click **"More Options" > "Print Patient Appointment"**

---

## 🛠️ Technical Implementation Details

### 1. `PrintAppointmentAction.java`

#### 🔹 Main Method: `performAction()`

Handles the request and prepares data for rendering the appointment print view.

#### 🔍 Steps Performed:

- **a)** Initialize session and variables  
- **b)** Set date/time and configuration  
- **c)** Initialize `dbUtils`, handle JSON or appointment ID  
- **d)** Extract data from JSON  
- **e)** Lookup location and doctor  
- **f)** Fetch appointment vector  
- **g)** Fetch patient details  
- **h)** Fetch insurance & balance  
- **i)** Fetch payment info  
- **j)** Get print settings  
- **k)** ⬅️ ***[Updated Here]*** Set additional header data

```java
PrintReportDetails PrintReportDetailsObj = PrintReportDetails.createModel(request, session.getAttribute("connectString").toString());
request.setAttribute("HeaderFile", PrintReportDetailsObj.GetDefaultReportHeader(dbUtils));
request.setAttribute("Hsh_PracticeDetails", PrintReportDetailsObj.GetPracticeDetailsForScheduler(dbUtils, schLocationId, appId));
````

* **l)** Verbose appointment print settings
* **m)** Cleanup
* **n)** Return JSP:

  ```java
  return "PrintAppointment.Screen";
  ```

---

### 2. `PrintReportDetails.java`

#### ✅ New Method: `GetPracticeAfterHours(DataBaseUtils dbUtils)`

```java
public String GetPracticeAfterHours(DataBaseUtils dbUtils) {
    String afterHoursInfo = HUtil.Nz(
        dbUtils.tableLookUp(
            "initial_settings_option_value",
            "initial_settings",
            "initial_settings_option_name ilike 'After Hours%'"
        ),
        ""
    );
    return afterHoursInfo;
}
```

**Purpose:**
Fetches the **"After Hours"** message from `initial_settings` table.

---

#### ✅ Updated Method: `GetPracticeDetailsForScheduler(...)`

Appended logic:

```java
Hsh_PracticeDetails.put("PracticeAfterHours", GetPracticeAfterHours(dbUtils));
```

**Effect:**
Adds the After Hours message to the `Hsh_PracticeDetails` attribute available in the JSP.

---

### 3. `ActionMappings.properties`

| Property Key                     | JSP Path                                  |
| -------------------------------- | ----------------------------------------- |
| `PrintPatientAppointment.Screen` | `/jsp/patientLogin/PrintAppointments.jsp` |
| `PrintAppointment.Screen`        | `/jsp/scheduler/printAppointment.jsp`     |


---

### 4. `PrintAppointments.jsp`

#### 🧩 Parsing After Hours Info:

```jsp
String practiceAfterHours = (String) Hsh_PracticeDetails.get("PracticeAfterHours");
```

#### 🖨️ Rendering After Hours in Header:

```jsp
<%
if (practiceAfterHours != null && !practiceAfterHours.equals("")) {
    out.print("<tr>");
    out.print("<td class='formndata' nowrap >" + practiceAfterHours + "</td>");
    out.print("</tr>");
}
%>
```

---

### 5. 🖨️ Print Behavior

* Structured info blocks:

  * Patient Info: Name, Account No., Address, etc.
  * Appointment Info: Provider, Reason, Notes, etc.
* After Hours message displayed dynamically.
* Print triggered automatically on load:

```html
<body onload="self.print();">
```

---

## ✅ Summary

| Component      | Description                                            |
| -------------- | ------------------------------------------------------ |
| **Backend**    | Extracts After Hours info from DB (`initial_settings`) |
| **Model**      | Injects it into `Hsh_PracticeDetails`                  |
| **Frontend**   | JSP renders the value if present                       |
| **Print View** | Now displays "After Hours" message in header section   |


