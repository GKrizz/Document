# 🗓️ Day 29 – June 16, 2025 (Monday)

````markdown
# 🔍 API: `getPatientsSeen(...)`

### Endpoint: `/glacemonitor/mipsperformance/getPatientsSeen`

## 🧠 Purpose

This API returns a list of **patient IDs seen by a provider (or list of providers)** during a specified time range, determined by a mode.  
It returns:  
```java
Map<ProviderID, List<PatientID>>
````

---

## 📥 API Parameters

| Parameter              | Type     | Required | Description                                                                  |
| ---------------------- | -------- | -------- | ---------------------------------------------------------------------------- |
| `reportingYear`        | `int`    | ✅        | The MIPS reporting year                                                      |
| `accountID`            | `String` | ✅        | Used for logging and error alert emails                                      |
| `startDate`, `endDate` | `Date`   | ❌        | Used only in **mode 4** for custom date range                                |
| `mode`                 | `int`    | ✅        | Determines the date range:                                                   |
|                        |          |          | `1` = Today<br>`2` = Current Month<br>`3` = Reporting Period<br>`4` = Custom |
| `triggerDate`          | `String` | ❌        | Not used in current implementation                                           |

---

## ⚙️ Core Variables & Their Role

| Variable             | Purpose                                              |
| -------------------- | ---------------------------------------------------- |
| `patientsList`       | Final output: provider ID → list of patient IDs      |
| `providers`          | List of provider configurations fetched for the year |
| `providerId`         | Temporary looping holder for provider ID             |
| `providersList`      | Used in mode 1 when checking only today's patients   |
| `responseMsg`        | Holds message for alert email on error               |
| `writer/printWriter` | Collects stack trace in case of exceptions           |

---

## 🏗️ How the Method Works

### Step 1: Fetch Provider Configurations

```java
providers = providerConfService.getProviderReportingInfo(reportingYear);
```

* This internally:

  * Loads data from `macra_provider_configuration` (MACRA configs)
  * Joins with `emp_profile` (provider details)
  * Returns `List<MacraProviderQDM>` objects
  * Appends measures from `quality_measures_provider_mapping`

📌 These `MacraProviderQDM` beans are **not DB tables**, but structured Java POJOs for internal logic.

---

## ⏳ Date Mode Handling Logic

| Mode | Description      | Dates Used                       | Activity Filter? |
| ---- | ---------------- | -------------------------------- | ---------------- |
| 1    | Today            | Current date only                | ✅ Yes            |
| 2    | Current Month    | 1st to last day of current month | ❌ No             |
| 3    | Reporting Period | Provider-specific dates from DB  | ❌ No             |
| 4    | Custom Dates     | Provided startDate & endDate     | ❌ No             |

---

## 🧩 Internal Logic by Mode

### 🔵 **Mode 1**: Today

* Filters patients using `getActivityPatients(...)`:

  * From `patient_updates` and `quality_measures_patient_entries`
  * Includes only:

    * Patients with **no QMPE entry**
    * Or `lastUpdate > updatedOn` (new activity)

* Fetches:

  * `getPatientsSeenByDos(...)` (→ from `service_detail`)
  * `getPatientsSeenByEncounter(...)` (→ from `chart`, `encounter`, `chartcenter_encounter`)
  * Final filter from `patient_registration`

➡️ Filters out dummy patients like `000000`

---

### 🟣 **Mode 2**: Current Month

* Constructs first & last date of the current month:

  ```java
  setTimeToBeginningOfDay(calendar.set(DAY_OF_MONTH, 1));
  setTimeToEndOfDay(calendar.set(DAY_OF_MONTH, last));
  ```

* Calls `performanceService.getPatientsSeen(providerId, startDate, endDate, null)`

* No activity filtering

* Same data flow as Mode 4
---
       +------------------------+
	     |   GET /getPatientsSeen|
	     |   mode=2              |
	     +-----------+-----------+
		         |
		         v
	     +----------------------------+
	     | getCalendarForNow()       |
	     | calendar.set(DAY_OF_MONTH, min) |
	     | setTimeToBeginningOfDay() |
	     +----------------------------+
		         |
		 startDate = 1st of Month 00:00
		         |
		         v
	     +----------------------------+
	     | getCalendarForNow()       |
	     | calendar.set(DAY_OF_MONTH, max) |
	     | setTimeToEndofDay()         |
	     +----------------------------+
		         |
		 endDate = Last of Month 23:59
		         |
		         v
	     +------------------------------------------+
	     | performanceService.getPatientsSeen(...)  |
	     +------------------------------------------+

---

### 🟢 **Mode 3**: Reporting Period (from DB)

* Gets dates from:

  * `getMacraProviderConfigurationReportingStart()`
  * `getMacraProviderConfigurationReportingEnd()`

* Calls:

  ```java
  performanceService.getPatientsSeen(providerId, startDate, endDate, reportingYear)
  ```

* Does **not use activityPatients**

* Final merge with `patient_registration`
---
| Condition                                     | Behavior                                                                             |
| --------------------------------------------- | ------------------------------------------------------------------------------------ |
| `mode == 3` with non-null `startDate/endDate` | ❌ You **do not** filter by activity patients. Only filter by date range and provider |
| `mode == 1` with `null` dates  | ✅ You **do** filter by active patients (those with updates/new info for the year)    |

🧠 Flow
---
					Start (mode == 3)
					     ↓
					Read startDate & endDate from provider config
					     ↓
					Fetch activity patients (not used in this mode)
					     ↓
					Fetch patients from ServiceDetail (getPatientsSeenByDos)
					     ↓
					Fetch patients from Encounter/Chart (getPatientsSeenByEncounter)
					     ↓
					Combine both lists
					     ↓
					Filter via PatientRegistration (get full demographics)
					     ↓
					Return List<Integer> of patient IDs


---

### 🔶 **Mode 4**: Custom Dates

* Uses explicitly passed `startDate` and `endDate`

* Calls:

  ```java
  performanceService.getPatientsSeen(providerId, startDate, endDate, null)
  ```

* Internally:

  * `getPatientsSeenByDos(...)`
  * `getPatientsSeenByEncounter(...)`
  * Merges lists and filters through `patient_registration`

* **No filtering by activityPatients**

---

## ⚙️ Data Sources & Joins

| Table                                         | Role                                                       |
| --------------------------------------------- | ---------------------------------------------------------- |
| `macra_provider_configuration`                | Provider-level MIPS reporting config                       |
| `emp_profile`                                 | Provider details                                           |
| `quality_measures_provider_mapping`           | Quality measures per provider                              |
| `service_detail`                              | Visit/service details by provider/date                     |
| `chart`, `encounter`, `chartcenter_encounter` | Encounter-type patient visits                              |
| `patient_registration`                        | Final patient demographic filter                           |
| `patient_updates`                             | Tracks last update per patient                             |
| `quality_measures_patient_entries`            | Quality measure-specific entries per patient/provider/year |

---

## 🔄 Example Execution Flow (Mode 4)

```text
Input: providerId=101, startDate=June 1, endDate=June 10

Step 1: getActivityPatients(101, 2025) → [201, 202]
Step 2: getPatientsSeenByDos(...) → [201, 203]
Step 3: getPatientsSeenByEncounter(...) → [204]
Step 4: Merge → [201, 203, 204, 000000]
Step 5: Filter via patient_registration → [201, 203, 204]
Output: patientsList.put(101, [201, 203, 204])
```

---

## 📋 Special Filtering Rules

| Condition                             | Behavior                                                              |
| ------------------------------------- | --------------------------------------------------------------------- |
| `mode == 1` with null dates           | ✅ Filters by updated/active patients (`getActivityPatients(...)`)     |
| `mode == 3` or `mode == 4` with dates | ❌ No activity filtering, uses full result from `getPatientsSeen(...)` |

---

## 🧠 Why `getActivityPatients(...)` Matters

Used only in Mode 1:

* Avoids re-processing all patients every time
* Optimizes reporting by only selecting those:

  * Who are new
  * Or who have had new data/activity

---

## 🧪 Testing Tips

* Use dummy providers with known patients & encounters
* Simulate updates to `patient_updates` to test activity filtering
* Use each mode individually to validate behavior and filtering logic

---

## ✅ Summary

| Mode | Uses Dates? | Uses Activity? | Notes                                            |
| ---- | ----------- | -------------- | ------------------------------------------------ |
| 1    | ❌           | ✅              | Today's patients with activity filter            |
| 2    | ✅ (auto)    | ❌              | Patients from current month                      |
| 3    | ✅ (from DB) | ❌              | Uses provider's configured reporting period      |
| 4    | ✅ (manual)  | ❌              | Full custom date range, no activity optimization |

---
## ⚙️ Data Sources & Joins

| Table                                         | Description                                                                     |
| --------------------------------------------- | ------------------------------------------------------------------------------- |
| `macra_provider_configuration`                | Holds provider-level reporting configuration for MIPS (e.g., reporting period). |
| `emp_profile`                                 | Employee profile, used to filter active providers in allowed groups.            |
| `quality_measures_provider_mapping`           | Lists quality measures selected per provider for a given year.                  |
| `patient_registration`                        | Patient demographic info. Final output is list of patient IDs.                  |
| `patient_updates`                             | Tracks last activity/update per patient.                                        |
| `quality_measures_patient_entries`            | Tracks quality measure-specific updates per patient/provider/year.              |
| `service_detail`                              | Stores encounter-like services, used for Dates of Service (DOS).                |
| `chart`, `encounter`, `chartcenter_encounter` | Core of the encounter structure. Used for patient visits.                       |
---
