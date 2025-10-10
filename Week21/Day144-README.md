# ✅ Day 144 - CWE-755 (Stack Trace Handling)

**📅 Date:** October 9, 2025
**📌 Focus:** CWE-755 - Improper Handling of Exceptional Conditions (Stack Trace Exposure)
**📁 Summary:** Identification, review, and partial remediation of stack trace handling issues across multiple service classes in the project.

---

## 🛠️ Files Reviewed / Updated for Stack Trace Handling

The following classes were identified and/or updated to handle exceptions properly and avoid exposing stack traces (specifically related to `RateLimitExceededException`, unfiltered `getMessage()`, and raw `getResponseBodyAsString()` outputs):

### 🔍 Controllers

* `FHIRR4Controller.java`
* `PrescriptionEntryController.java`

### 💊 Service Implementations

* `PharmacyServiceImpl.java`
* `ClinicalNoteServiceImpl.java`
* `FHIRGoalsServiceImpl.java`
* `SSInboxServiceImpl.java`
* `ChartcenterServiceImpl.java` (isuue no 7, 16, 36, 43, 70)
* `BackblazeStorageServiceImpl.java`
* `CreditCardServiceImpl.java` (isuue no 11, 19, 20, 25, 41, 44, 52, 60, 65)
* `FlowsheetDesktopServiceImpl.java` (isuue no 12, 24, 26, 27, 51)
* `OrdersServiceImpl.java` (isuue no 13, 21, 31, 57)
* `RefillRequestServiceImpl.java` (isuue no 14, 45)
* `AddNewGroupServiceImpl.java`
* `ChartAndEncounterSummaryServiceImpl.java`
* `PrescriptionEntryServiceImpl.java`
* `RefillRequestEntryServiceImpl.java`
* `PlanServiceImpl.java`
* `ChargesServicesImpl.java`
* `DocumentsServiceImpl.java`
* `ImmunizationsServiceImpl.java` (isuue no 34, 38, 71)
* `LabEntriesServiceImpl.java` (isuue no 37, 69)
* `SurescriptsRefillRequestServiceImp.java`
* `PortalFormsServiceImpl.java`
* `BillingServiceImpl.java` (isuue no 50, 54)
* `PortalPaymentsServiceImpl.java` (isuue no 53, 58)
* `ImmunizationHistoryServiceImpl.java` (isuue no 55, 63)
* `QRDAQueryFactory.java`
* `PatientChartServiceImpl.java`
* `ElectronicPrescriptionServiceImpl.java`

### 🧾 Utilities

* `CryptoUtils.java`
* `ERXUtilities.java` (isuue no 47, 49, 64, 67)

### 🧪 Specifications

* `ReferralSpecification.java`
* `IVRLogSpecification.java`

---

## ⏳ Pending Items

The following files or lines are pending review and updates related to stack trace handling or exception message sanitization:

* Files: `10, 33, 35, 42, 46, 59, 61`
* Special case line: `17`

---
