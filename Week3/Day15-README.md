# 📅 Day 15 — Jun 2, 2025 (Monday)

##  Appointment Count Fetch on Scheduler Click

This document explains the **complete flow** of how clicking the Scheduler or Calendar in the GWT UI triggers a backend call, processes appointment data, and updates the frontend.

---

## ✅ Feature Purpose

When a user clicks the **Scheduler/Calendar** view, the system fetches and displays the **number of appointments** for each doctor/resource on a selected **day** or **week**.

---

## 🧭 End-to-End Flow (With Filenames)

---

### 1️⃣ User Click in GWT UI

**File:** `SchedulerActivity.java`  
**Method:** `getAppointmentCountValue()`

```java
public void getAppointmentCountValue() {
    if (isWeekView) {
        schedulerClient.getAppointmentCountValueforWeek(resourceId, weekStartDate, callback);
    } else {
        schedulerClient.getAppointmentCountValue(resourceId, date, callback);
    }
}
````

* Decides whether to fetch appointment data for **week** or **day**.
* Calls the appropriate REST method.

---

### 2️⃣ REST Client Call

**File:** `SchedulerClient.java`
**Method:** `getAppointmentCountValue(...)`

```java
@GET
@Path("/Scheduler/getAppointmentCountValue?resourceId={resourceId}&apptDate={apptDate}")
void getAppointmentCountValue(@PathParam("resourceId") String resourceId,
                              @PathParam("apptDate") String apptDate,
                              JsonCallback callback);
```

* RESTyGWT sends an HTTP `GET` request to the backend controller.
* Parameters: `resourceId`, `apptDate` (e.g., 06/02/2025)

---

### 3️⃣ Spring MVC Controller

**File:** `SchedulerController.java`
**Method:** `getAppointmentCountValue(...)`

```java
@GetMapping("/getAppointmentCountValue")
@ResponseBody
public EMRResponseBean getAppointmentCountValue(@RequestParam String resourceId,
                                                @RequestParam @DateTimeFormat(pattern = "MM/dd/yyyy") Date apptDate) {
    EMRResponseBean emrResponseBean = new EMRResponseBean();

    List<Integer> resourceList = convertResourceIdToList(resourceId);
    JSONObject apptCounts = schedulerService.getAppointmentCountValue(resourceList, apptDate);

    emrResponseBean.setData(apptCounts);
    return emrResponseBean;
}
```

* Converts query params to usable data.
* Calls the **service layer** for actual business logic.
* Returns a JSON response using `EMRResponseBean`.

---

### 4️⃣ Business Logic Layer

**File:** `SchedulerServiceImpl.java`
**Implements:** `SchedulerService.java`
**Method:** `getAppointmentCountValue(...)`

```java
public JSONObject getAppointmentCountValue(List<Integer> resourceIds, Date apptDate) {
    // Queries database or cache
    // Example output: { "1": 5, "2": 3 }
}
```

* Retrieves appointment counts per resource on a given date.
* Formats it into a `JSONObject`.

---

### 5️⃣ Controller Returns JSON Response

* JSON result is wrapped in `EMRResponseBean`.
* Sent back to GWT frontend via REST.

---

### 6️⃣ GWT Callback Receives Response

**File:** `SchedulerActivity.java`
**Method:** `onSuccess(...)`

```java
public void onSuccess(Method method, JSONValue response) {
    JSONObject apptCount = response.isObject();
    view.getApptResourceSlotInfo(apptCount);
}
```

* Handles JSON response.
* Passes appointment count data to the view layer.

---

### 7️⃣ UI Update

**File:** `Schedulerview.java`
**Method:** `getApptResourceSlotInfo(JSONObject apptCount);`

```java
void getApptResourceSlotInfo(JSONObject apptCount);
```

* The frontend updates the UI with appointment slots.

---

## 🔁 Visual Flow Summary (With File References)

```
[User Clicks Scheduler UI]
        ↓
SchedulerActivity.java 
    → getAppointmentCountValue()
        ↓
SchedulerClient.java 
    → getAppointmentCountValue()  (REST call)
        ↓
SchedulerController.java
    → getAppointmentCountValue()
        ↓
SchedulerServiceImpl.java
    → getAppointmentCountValue()
        ↓
Returns JSONObject (appointment count)
        ↓
SchedulerActivity.java
    → onSuccess()
        ↓
Schedulerview.java
    → getApptResourceSlotInfo()
        ↓
[UI Updated with Appointment Count]
```

---

## 📂 Files Involved

| File                        | Role                                                   |
| --------------------------- | ------------------------------------------------------ |
| `SchedulerActivity.java`    | GWT controller (handles user action, callback, logic)  |
| `SchedulerClient.java`      | REST interface for backend calls (RESTyGWT)            |
| `SchedulerController.java`  | Spring MVC Controller – receives and processes request |
| `SchedulerService.java`     | Interface defining business logic                      |
| `SchedulerServiceImpl.java` | Implements business logic for appointment counting     |
| `Schedulerview.java`        | GWT view interface to update UI                        |
| `EMRResponseBean.java`      | Standard response wrapper for backend JSON responses   |

---

## 🧠 Notes

* `EMRResponseBean` is used to standardize JSON responses across the backend.
* `RestyGWT` auto-binds HTTP methods and paths — no manual `RequestBuilder` needed.
* Week vs Day logic is fully separated in the client and backend.

---

## 📌 Example API Request

```
GET /Scheduler/getAppointmentCountValue?resourceId=1&apptDate=06/02/2025
```

### Sample Response

```json
{
  "1": 5,
  "2": 3
}
```

* Doctor 1 has 5 appointments
* Doctor 2 has 3 appointments

---

Let me know if you want this as a downloadable `.md` file or a visual diagram (PNG/PDF).

```

---

Would you like me to export this to a downloadable file or generate a diagram of this flow?
```
