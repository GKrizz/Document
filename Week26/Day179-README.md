# 📌 **Day 179 — Code Review & Refactoring Log**

**Date:** November 12, 2025
**Focus:** String comparison fixes, charset usage, exception handling, date formatting checks, and file/directory validations.

---

## 🗂️ Files Reviewed

---

## **1️⃣ ClinicalElementsServiceImpl.java**

| No. | Line  | Issue / Code Reference          | Status                      |
| --- | ----- | ------------------------------- | --------------------------- |
| 1   | 11113 | ⚠ String comparison (`== / !=`) | ✔ No change needed          |
| 2   | 8285  | DecimalFormat                   | ✔ No change needed          |
| 3   | 13978 | String.getBytes                 | ❗ Needs fix                 |
| 4   | 28155 | DateTimeFormatter.ofPattern     | ✔ No change needed          |
| 5   | 10145 | caught                          | ✔ Valid                     |
| 6   | 2861  | ⚠ String comparison (`== / !=`) | 🔍 Review / Fix recommended |
| 7   | 25982 | String.getBytes                 | ❗ Needs fix                 |
| 8   | 10099 | indexOf() search                | ℹ Logic validation required |
| 9   | 11131 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 10  | 11118 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 11  | 4688  | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 12  | 14966 | DecimalFormat                   | ✔ No change                 |
| 13  | 11156 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 14  | 10125 | String.getBytes                 | ❗ Needs fix                 |
| 15  | 11043 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 16  | 1885  | String.getBytes                 | ❗ Needs fix                 |
| 17  | 28066 | DateTimeFormatter.ofPattern     | ✔ No change                 |
| 18  | 1895  | String.getBytes                 | ❗ Needs fix                 |
| 19  | 14499 | DecimalFormat                   | ✔ No change                 |
| 20  | 11109 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 21  | 10832 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 22  | 1693  | caught                          | ✔ Valid                     |
| 23  | 19882 | String.getBytes                 | ❗ Needs fix                 |
| 24  | 10143 | String.getBytes                 | ❗ Needs fix                 |
| 25  | 27843 | DateTimeFormatter.ofPattern     | ✔ No change                 |
| 26  | 13262 | String.getBytes                 | ❗ Needs fix                 |
| 27  | 1871  | caught                          | ✔ Valid                     |
| 28  | 10103 | String.getBytes                 | ❗ Needs fix                 |
| 29  | 11170 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 30  | 11095 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 31  | 20032 | String.getBytes                 | ❗ Needs fix                 |
| 32  | 9058  | DecimalFormat                   | ✔ No change                 |
| 33  | 11030 | ⚠ String comparison (`== / !=`) | 🔍 Review                   |
| 34  | 10341 | caught                          | ✔ Valid                     |
| 35  | 10184 | indexOf() search                | ℹ Verify purpose/edge cases |
| 36  | 14939 | DecimalFormat                   | ✔ No change                 |

---

## **2️⃣ PointClickCareServiceImpl.java**

| No. | Line | Issue / Code Item                  | Status            |
| --- | ---- | ---------------------------------- | ----------------- |
| 1   | 5540 | catch                              | ✔                 |
| 2   | 4697 | catch                              | ✔                 |
| 3   | 4146 | catch                              | ✔                 |
| 4   | 3099 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 5   | 3967 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 6   | 3806 | catch                              | ✔                 |
| 7   | 3101 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 8   | 4619 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 9   | 5106 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 10  | 2509 | catch                              | ✔                 |
| 11  | 5421 | catch                              | ✔                 |
| 12  | 1181 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 13  | 3434 | catch                              | ✔                 |
| 14  | 121  | Class references 146 other classes | ✔ Not an issue    |
| 15  | 6048 | GS-Tech static config campaign     | 🔍 Review context |
| 16  | 5522 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 17  | 5601 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 18  | 1875 | catch                              | ✔                 |
| 19  | 4881 | mkdirs() success validation        | ✔ Completed       |
| 20  | 4273 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 21  | 5850 | DateTimeFormatter.ofPattern        | ✔ No change       |
| 22  | 5187 | catch                              | ✔                 |
| 23  | 5641 | catch                              | ✔                 |
| 24  | 2041 | catch                              | ✔                 |
| 25  | 4482 | catch                              | ✔                 |

---

## **3️⃣ SchedulerServiceImpl.java**

| No.   | Line    | Issue / Code                | Status                  |
| ----- | ------- | --------------------------- | ----------------------- |
| 1–5   | Various | Calendar.getInstance        | ✔ No change             |
| 6     | 9790    | DateTimeFormatter.ofPattern | ✔ No change             |
| 7–8   | Various | Calendar.getInstance        | ✔ No change             |
| 9     | 8241    | mkdir success verification  | ✔ Completed             |
| 10–12 | Various | Calendar.getInstance        | ✔ No change             |
| 13    | 2470    | Hardcoded locale literal    | ✔ Allowed               |
| 14    | 6657    | Calendar.getInstance        | ✔ No change             |
| 15    | 10658   | ZoneId.systemDefault        | ✔ No change             |
| 16    | 9257    | mkdir success verification  | ✔ Completed             |
| 17–21 | Various | Calendar.getInstance        | ✔ No change             |
| 22    | 2373    | catch                       | ⚠ Not located, re-check |
| 23–24 | Various | Calendar.getInstance        | ✔ No change             |

---

## **4️⃣ LabResultsServiceImpl.java**

| No.      | Line                               | Issue          | Status |
| -------- | ---------------------------------- | -------------- | ------ |
| Multiple | SimpleDateFormat                   | ✔ No change    |        |
| Many     | catch blocks                       | ✔ Valid        |        |
| 4        | Class references 122 other classes | ✔ Not an issue |        |

---

## 📌 Next Steps / Actions Needed

| Type                        | Count         | Action                                          |
| --------------------------- | ------------- | ----------------------------------------------- |
| `String.getBytes` usage     | ~10 locations | Replace with `getBytes(StandardCharsets.UTF_8)` |
| String comparison (`==/!=`) | ~12 items     | Convert to `.equals()` or `.equalsIgnoreCase()` |
| indexOf usage               | 2 items       | Validate null safety + alternative methods      |
| Unknown entries             | 1–2 items     | Investigate code context                        |
| mkdir checks                | Completed     | No further action                               |

---

### ✔ Recommended Fix Pattern

```java
if (myString != null && myString.equals("value")) { … }

byte[] bytes = input.getBytes(StandardCharsets.UTF_8);
```

---
