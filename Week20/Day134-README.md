
# 📅 Day 134 — September 29, 2025 (Monday)


## 📌 CWEs Reviewed & Remediated

### 🔒 CWE-20 — Improper Input Validation (4 instances)

Validated external inputs in several service classes to prevent null values, invalid IDs, and path traversal attacks.

**Affected Files:**
1. `DirectMailServiceImpl.java`
   - ✅ Checked and sanitized `SharedFolderPath2` to prevent path traversal.
2. `MessageServiceImpl.java`
   - ✅ Validated `categoryIds` to prevent null and negative values.
3. `AlertInboxServiceImpl.java`
   - ✅ Validated `encounterIdList` before database usage.
4. `EmailDocumentServiceImpl.java`
   - ✅ Ensured `edirectIds` is not null or empty before processing.

---

### ⚠️ CWE-252, 754 — Missing Check of Return Value / Missing Error Handling (3 instances)

Ensured `File.createNewFile()` is properly checked to prevent silent file overwrites.

**Affected Files:**
1. `OpenProblemController.java`
2. `MUPerformanceRateServiceImpl.java` (2 instances)

**Fix Example:**
```java
boolean fileCreated = file.createNewFile();
if (!fileCreated) {
    throw new IOException("File already exists: " + file.getAbsolutePath());
}
````

---

### 🌐 CWE-352 — Cross-Site Request Forgery (CSRF) (8 instances)

Identified multiple publicly accessible endpoints using `@RequestMapping`. CSRF protection should be verified on these endpoints.

**Affected Controllers:**

* `InvestigationController.java`
* `SurescriptsController.java` (5+ endpoints)

---

### 🧯 CWE-390 — Catching Exceptions Without Action (112 instances)

Refactored or reviewed multiple instances of exception handling where exceptions were logged but not properly acted upon or rethrown.

**Examples:**

* `LoggingUtils.java`
* `IDMeServiceImpl.java`
* `FHIRServiceImplementation.java`
* `ICD10ServiceImpl.java`

**Fix Example:**

```java
catch (JsonProcessingException e) {
    GlaceLogger.LogException(e);
    throw new RuntimeException("Failed to process JSON", e);
}
```

---

## ✅ Improvements Summary

| CWE ID(s)            | Description                             | Instances |
| -------------------- | --------------------------------------- | --------- |
| CWE-20               | Improper Input Validation               | 4         |
| CWE-252, 754         | Missing Return Check / Error Handling   | 3         |
| CWE-327, 328, 326... | (Handled separately, not part of today) | -         |
| CWE-352              | Cross-Site Request Forgery              | 8         |
| CWE-390              | Catching Exceptions Without Action      | 112       |

---


## 📂 Files Touched

* `DirectMailServiceImpl.java`
* `MessageServiceImpl.java`
* `AlertInboxServiceImpl.java`
* `EmailDocumentServiceImpl.java`
* `OpenProblemController.java`
* `MUPerformanceRateServiceImpl.java`
* `InvestigationController.java`
* `SurescriptsController.java`
* `LoggingUtils.java`
* `IDMeServiceImpl.java`
* `FHIRServiceImplementation.java`
* `ICD10ServiceImpl.java`

---
