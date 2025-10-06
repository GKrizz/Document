# 📅 Day 135 — September 30, 2025 (Tuesday)

## 📌 CWEs Reviewed & Remediated

### 🛑 CWE-400 — Uncontrolled Resource Consumption

### ⚠️ CWE-664 — Improper Control of a Resource Through its Lifetime

**✅ Total Instances Fixed: 15**

Refactored file handling and stream management across 15 files to prevent resource leaks, file locks, and excessive resource consumption.
Used `try-with-resources` to ensure proper automatic closure of streams.

---

## 🧰 Fix Strategy

To address CWE-400 and CWE-664, the following improvements were made:

* Introduced `try-with-resources` in all file I/O operations
* Ensured proper cleanup of resources (e.g., `InputStream`, `OutputStream`, `BufferedReader`, `ZipOutputStream`)
* Avoided silent failures or hanging file handles

---

## 🗂️ Files Reviewed & Updated

| #  | File Name                                 | Fix Summary                                               |
| -- | ----------------------------------------- | --------------------------------------------------------- |
| 1  | `CareQualityServiceImpl.java`             | `BufferedWriter` with try-with-resources                  |
| 2  | `DocumentsController.java`                | `DataInputStream`, `ServletOutputStream` wrapped properly |
| 3  | `CodeGeneratorUtility.java`               | `FileInputStream`, `FileOutputStream`, `ZipOutputStream`  |
| 4  | `CdaValidatorServiceImpl.java`            | `BufferedWriter`, `BufferedInputStream` usage             |
| 5  | `DirectMailController.java`               | `BufferedReader` refactored                               |
| 6  | `FileUploadAndRetrieveController.java`    | `BufferedOutputStream` added for image write              |
| 7  | `DirectMailServiceImpl.java`              | `InputStream` usage handled                               |
| 8  | `EmailDocumentServiceImpl.java`           | `BufferedWriter` used for XML writing                     |
| 9  | `OpenProblemController.java`              | `BufferedOutputStream` used for file upload               |
| 10 | `MeasureCalcServiceImpl.java`             | File writing and zip stream handling                      |
| 11 | `HealthcareSurveyServiceImpl.java`        | Combined `BufferedReader` and `BufferedWriter`            |
| 12 | `CodeGeneratorUtility.java`               | Added missing zip stream closure                          |
| 13 | `CdaValidatorServiceImpl.java`            | Buffered read with proper exception handling              |
| 14 | `MeasureCalcServiceImpl.java`             | Proper zip stream management                              |
| 15 | `CareQualityServiceImpl.java` (2nd entry) | Another instance of `BufferedWriter` updated              |

---

## 🔧 Code Snippet Examples

### `CareQualityServiceImpl.java`

```java
try (BufferedWriter writingFile = new BufferedWriter(new FileWriter(file1))) {
    writingFile.write(data.toString());
}
```

---

### `DocumentsController.java`

```java
try (DataInputStream disForPdf = new DataInputStream(new FileInputStream(pdfFile))) {
    disForPdf.readFully(pdfFileData);
}

response.setContentType("application/pdf");
response.setContentLength(pdfFileData.length);

try (ServletOutputStream outputStream = response.getOutputStream()) {
    outputStream.write(pdfFileData);
    outputStream.flush();
}
```

---

### `HealthcareSurveyServiceImpl.java`

```java
try (
    BufferedReader readingFile = new BufferedReader(new FileReader(newName));
    BufferedWriter writingFile = new BufferedWriter(new FileWriter(file))
) {
    // Copying logic here
}
```

---

