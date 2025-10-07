# Project Security & Resource Management Update

**Date:** October 6, 2025 (Monday)
**Status:** Pending commit of all changes

---

## Summary of Work Done & Pending Verification

| CWE ID           | Description                                                   | Instances Fixed | Instances Pending Verification |
| ---------------- | ------------------------------------------------------------- | --------------- | ------------------------------ |
| CWE-117, CWE-93  | Log Injection                                                 | 2               | 0                              |
| CWE-20           | Invalid Public Access                                         | 3               | 1                              |
| CWE-252, CWE-754 | Missing Checks                                                | 3               | 0                              |
| CWE-352          | Cross-site Request Forgery (CSRF)                             | 8               | 0                              |
| CWE-390          | Catching Generic Exceptions                                   | 6               | 112                            |
| CWE-400, CWE-664 | Uncontrolled Resource Consumption & Improper Resource Control | 100             | Need to verify                 |

---

## Focus: CWE-400 & CWE-664 — Resource Management Fixes

**Total Instances: ~100**

### Files & Key Fix Points

| File                                   | Line(s)                                    | Fix Details / Notes                                                                                                             |
| -------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `CdaValidatorServiceImpl.java`         | 16, 18, 19, 27, 29, 91                     | Added `try-with-resources` (e.g., `FileWriter`, `BufferedReader`, `PrintWriter`) and methods like `compressCDAFiles()` verified |
| `CDAGenerationServiceImpl.java`        | 23, 33                                     | Added resource handling using try-with-resources for `Writer` and stream operations                                             |
| `MeasureCalcServiceImpl.java`          | 24, 42, 44, 62, 65, 77, 78, 80, 88, 95, 96 | BufferedWriter usage with proper closure in multiple locations                                                                  |
| `RefillRequestServiceImpl.java`        | 26, 28                                     | File writing and reading wrapped in try-with-resources                                                                          |
| `CodeGeneratorUtility.java`            | 30                                         | Zip streams and file streams properly closed using try-with-resources                                                           |
| `FileUploadAndRetrieveController.java` | 31, 48, 85                                 | Buffered streams fixed; verification needed on catch block handling at lines 149 and 744                                        |
| `QRDAServiceImpl.java`                 | 35                                         | BufferedOutputStream wrapped properly                                                                                           |
| `MUPerformanceRateServiceImpl.java`    | 36                                         | BufferedWriter with try-with-resources                                                                                          |

---
