

# Project Security & Resource Management Update

**Date:** October 7, 2025 (Day 142) <br>
**Status:** In-progress — pending review and verification

---

## Overview: CWE-400 & CWE-664 Resource Management Fixes

This report outlines the current state of resource management fixes (primarily via try-with-resources) across multiple files. Many files have already been updated; some require verification or clarification on next steps, especially regarding catch block logic.

---

## Files Reviewed & Status Notes

| File Name                                | Notes / Status                                                                                                      |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **CodeGeneratorUtility.java**            | Lines 38, 40, 59 — Already completed or no changes needed                                                           |
| **KioskPatientDetailsController.java**   | Lines 37, 72, 84 — Proper try-with-resources added; verify catch block on line 84 (`responseBean.setSuccess(true)`) |
| **CdaValidatorServiceImpl.java**         | Lines 43, 46, 61, 63, 73-75, 92 — Changes already done; no further action needed                                    |
| **ExportQRDAServiceImpl.java**           | Line 45 — BufferedWriter usage wrapped properly                                                                     |
| **RefillRequestServiceImpl.java**        | Lines 47, 54, 60, 83, 89, 99 — Proper try-with-resources; lines 83 marked no change needed                          |
| **FileUploadAndRetrieveController.java** | Lines 69, 79, 82 — Mostly completed; some verification pending                                                      |
| **DirectUtility.java**                   | Lines 49, 71 — Changes done or no action needed                                                                     |
| **DirectMailController.java**            | Lines 41 (check catch block), 50, 64, 90 — Mostly done; need catch block review on 41                               |
| **EmailDocumentServiceImpl.java**        | Lines 17, 51, 52, 55, 58, 67, 86, 100 — Mostly done; 67 pending                                                     |
| **AmendmentsServiceImpl.java**           | Lines 53, 93 — Mostly done; no changes needed                                                                       |
| **CDAGenerationServiceImpl.java**        | Lines 56, 57 — Mostly done; no changes needed                                                                       |
| **MeasureCalcServiceImpl.java**          | Lines 66, 77, 78, 80, 88, 95 — Mostly done; no changes needed                                                       |
| **HealthcareSurveyServiceImpl.java**     | Line 68 — Done; no changes needed                                                                                   |
| **OpenProblemController.java**           | Line 70 — FileOutputStream wrapped in try-with-resources                                                            |
| **GWTAuthController.java**               | Line 76 — BufferedOutputStream usage wrapped                                                                        |
| **EMeasureUtils.java**                   | Lines 81, 94 — FileWriter wrapped properly                                                                          |
| **DocumentsServiceImpl.java**            | Line 87 — DataInputStream usage wrapped                                                                             |
| **MUPerformanceRateServiceImpl.java**    | Line 96 — Done; no changes needed                                                                                   |
| **MultipartUtility.java**                | Line 97 — FileInputStream wrapped                                                                                   |
| **TestXDR.java**                         | Line 98 — FileInputStream wrapped                                                                                   |
| **EMPTY.java**                           | No content                                                                                                          |

---

## Additional Notes & To-Dos

* **Catch blocks requiring review:**

  * `KioskPatientDetailsController.java` line 84 — `responseBean.setSuccess(true);` inside catch block needs verification.
  * `DirectMailController.java` line 41 — Determine if catch block should throw exception or handle differently.

* **Pending verification:**

  * `EmailDocumentServiceImpl.java` line 67 — Pending fix or verification.
  * Multiple files noted as "already done" where no further change seems necessary.

* **Generic exception catching:**

  * `DirectMailServiceImpl.java` and `DirectMailController.java` need review of generic exception handling (7 and 8 instances respectively).

---
