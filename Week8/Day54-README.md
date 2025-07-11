# 📋 XML API Endpoints Overview

---

## 🖥️ Localhost APIs (GlaceMaster - `localhost:8080`)

| 🔹 Method | 🛣️ Endpoint                                                      | 📝 Description                                          |
| --------- | ----------------------------------------------------------------- | ------------------------------------------------------- |
| POST      | `/GlaceMaster/jsp/chart/leafmodel/FetchEncounterVariables.Action` | Fetch encounter-related variables and chart data.       |
| POST      | `/GlaceMaster/jsp/chart/leafmodel/SoapAjax.Action`                | Handle SOAP AJAX calls for chart operations.            |
| POST      | `/GlaceMaster/jsp/chart/leafmodel/HPIElements.Action`             | Fetch/update HPI (History of Present Illness) elements. |
| POST      | `/GlaceMaster/jsp/chart/patientdetails/NotesPreviewAction.Action` | Preview patient notes.                                  |

---

## 🌐 Glace EMR Backend APIs (`192.168.2.241`)

### 👤 User & Employee

| 🔹 Method | 🛣️ Endpoint                                                                | 🔑 Query Params | 📝 Description                   |
| --------- | --------------------------------------------------------------------------- | --------------- | -------------------------------- |
| GET       | `/glaceemr_backend/glace/api/desktop/user/Employee.Action/fetchAccessCodes` | `userId=1`      | Fetch access codes for the user. |

### 📬 Notifications & Tasks

| 🔹 Method | 🛣️ Endpoint                                                                             | 📝 Description                                    |
| --------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------- |
| GET       | `/glaceemr_backend/glace/api/desktop/user/AlertInbox.Action/getIsNewNotificationEnabled` | Check if new notifications are enabled.           |
| POST      | `/glaceemr_backend/glace/api/desktop/user/usertaskConfig/prepareUsertaskConfig`          | Prepare user task configuration (needs `userId`). |

### 📅 Scheduler & SSO

| 🔹 Method | 🛣️ Endpoint                                                       | 🔑 Query Params   | 📝 Description                    |
| --------- | ------------------------------------------------------------------ | ----------------- | --------------------------------- |
| GET       | `/glaceemr_backend/glace/api/desktop/user/Scheduler/getSSODetails` | `accountId=glace` | Get Single Sign-On (SSO) details. |

### 📄 XML Generation

| 🔹 Method | 🛣️ Endpoint                                                        | 🔑 Query Params                                                                                                             | 📝 Description                                   |
| --------- | ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| GET       | `/glaceemr_backend/glace/api/desktop/user/XMLGeneration/getXMLFile` | `patientId=376638`, `chartId=376973`, `encounterId=152839`, `templateId=3355`, `formId=1833`, `tabId=10`, `accountId=glace` | Get XML file for patient encounter and template. |

### 👥 RPM & Employee Info

| 🔹 Method | 🛣️ Endpoint                                                   | 🔑 Query Params | 📝 Description            |
| --------- | -------------------------------------------------------------- | --------------- | ------------------------- |
| GET       | `/glaceemr_backend/glace/api/desktop/user/rpm/findEmpNameById` | `empId=1`       | Find employee name by ID. |

### 🐛 Legacy Connect & Error Logging

| 🔹 Method | 🛣️ Endpoint                                              | 🔑 Query Params                                                                       | 📝 Description                   |
| --------- | --------------------------------------------------------- | ------------------------------------------------------------------------------------- | -------------------------------- |
| GET       | `/glaceemr_backend/glace/api/desktop/user/connect/legacy` | `url=/IpadErrorLog`, `method=POST`, `params={userName, userId, error_log, tomcatUrl}` | Send iPad error logs to backend. |

### 📋 Medical Summary

| 🔹 Method | 🛣️ Endpoint                                                                            | 📝 Description            |
| --------- | --------------------------------------------------------------------------------------- | ------------------------- |
| GET       | `/glaceemr_backend/glace/api/desktop/user/MedicalSummary.Action/getScribeConfiguration` | Get scribe configuration. |

---

