# 📅 Day 22 – June 9, 2025 (Monday)

## ✅ Task: Refactor and Optimize Lab Results Narrative Block

### 🔍 Goal:

Improve performance and readability of the `getNarativeBlock()` method used for generating lab results in a tabular format. Focus on minimizing database calls and structuring the data more efficiently for debugging and maintenance.

---

### ✅ **Changes Implemented**

#### 🔁 Replaced Per-Row DB Fetching with Bulk Fetch

* **Before:** Parameters were fetched for each `LabResults` record using individual DB calls inside a loop.
* **After:** Extracted all unique `testDetailId`s and fetched all related parameters in **one batch query**, improving efficiency.

```java
// Before: One DB call per lab result
labResultList.get(labCount).setParameters(labResultDAO.getParameterList(testId, config.getDbUtils()));

// After: Single DB call
Map<String, List<LabParameters>> parameterMap = labResultDAO.getAllParametersByTestDetailIds(testDetailIds, dbUtils);
```

#### 🧠 Cached Results in a HashMap for Debugging

* Constructed a `labDataMap` to capture all rows grouped by test ID and lab name.
* Outputted the map for debug verification using `System.out.println()` at the end of the method.

---

### 🆚 Difference Between Old and New Implementation

| Feature                | Old Method                        | New Method (Optimized)                      |
| ---------------------- | --------------------------------- | ------------------------------------------- |
| **Parameter Fetching** | Inside loop, 1 DB call per result | Single bulk query outside loop              |
| **Performance**        | Slower with larger datasets       | Faster, fewer DB round-trips                |
| **Debug Output**       | Added manually                    | Stored in `labDataMap` and printed once     |
| **Code Clarity**       | Traditional, verbose              | Modern Java (streams, lambdas, clean scope) |
| **String Safety**      | `.contentEquals()` (prone to NPE) | Safer `.equals()` usage                     |

---

### 📌 Summary

The refactored method:

* Minimizes database load
* Improves performance significantly
* Enhances code readability
* Makes debugging easier with structured log output

---
