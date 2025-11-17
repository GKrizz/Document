# 📌 PortalPaymentsServiceImpl.java — Code Review Progress

**Date:** November 10, 2025
**Day:** Monday

---

## 🧾 Summary

This document tracks the code review and refactoring progress for **PortalPaymentsServiceImpl.java**, mainly focusing on:

✔ Replacing **`String.getBytes()`** with **`getBytes(StandardCharsets.UTF_8)`**
✔ Fixing **String comparison issues (`==` or `!=`)**
⚠ Pending items
🔁 Duplicate entries noted

---

## 📋 Review Log

| No. | Line / ID | Issue / Note                | Status          |
| --- | --------- | --------------------------- | --------------- |
| 1   | 1648      | String.getBytes             | ✔ completed     |
| 2   | 2751      | String.getBytes             | ✔ completed     |
| 3   | 1719      | String.getBytes             | ✔ completed     |
| 4   | 2741      | String.getBytes             | ✔ completed     |
| 5   | 2736      | String.getBytes             | ✔ completed     |
| 6   | 3025      | pending                     | ⏳ pending       |
| 7   | 3694      | ❗ String comparison (==/!=) | ✔ completed     |
| 8   | 2497      | String.getBytes             | ✔ completed     |
| 9   | 1724      | String.getBytes             | ✔ completed     |
| 10  | 2648      | String.getBytes             | ✔ completed     |
| 11  | 2487      | String.getBytes             | ✔ completed     |
| 12  | 3066      | String.getBytes             | ✔ completed     |
| 13  | 2497      | String.getBytes             | ✔ completed     |
| 14  | 2409      | String.getBytes             | ✔ completed     |
| 15  | 2648      | String.getBytes             | ✔ completed     |
| 16  | 2736      | String.getBytes (duplicate) | ✔ completed     |
| 17  | 2645      | String.getBytes             | ✔ completed     |
| 18  | 2492      | String.getBytes             | ✔ completed     |
| 19  | 3411      | pending                     | ⏳ pending       |
| 20  | 2487      | String.getBytes (duplicate) | ✔ completed     |
| 21  | 1455      | Use StandardCharsets.UTF_8  | ✔ completed     |
| 22  | 2730      | String.getBytes             | ✔ completed     |
| 23  | 1623      | String.getBytes             | ✔ completed     |
| 24  | 3063      | String.getBytes             | ✔ completed     |
| 25  | 1569-1595 | pending                     | ⏳ pending       |
| 26  | 2481      | String.getBytes             | ✔ completed     |
| 27  | 2733      | String.getBytes             | ✔ completed     |
| 28  | 2746      | String.getBytes             | ✔ completed     |
| 29  | 4014      | needs re-check              | 🧐 review again |
| 30  | 2753      | String.getBytes             | ✔ completed     |
| 31  | 2502      | String.getBytes             | ✔ completed     |
| 32  | 3125      | String.getBytes             | ✔ completed     |
| 33  | 4009      | Calendar.getInstance        | ℹ no change     |
| 34  | 3591      | String.getBytes             | ✔ completed     |
| 35  | 881       | DecimalFormat               | 📝 check usage  |
| 36  | 2661      | String.getBytes             | ✔ completed     |
| 37  | 2751      | String.getBytes (duplicate) | ✔ completed     |
| 38  | 2730      | String.getBytes (duplicate) | ✔ completed     |
| 39  | 2398-2537 | pending                     | ⏳ pending       |
| 40  | 3060      | String.getBytes             | ✔ completed     |
| 41  | 3100      | ❗ String comparison         | ✔ completed     |
| 42  | 2494      | String.getBytes             | ✔ completed     |
| 43  | 2409      | String.getBytes (duplicate) | ✔ completed     |
| 44  | 2479      | String.getBytes             | ✔ completed     |
| 45  | 2645      | String.getBytes             | ✔ completed     |
| 46  | 2538      | String.getBytes             | ✔ completed     |
| 47  | 2502      | String.getBytes (duplicate) | ✔ completed     |
| 48  | 2489      | String.getBytes             | ✔ completed     |
| 49  | 2538      | String.getBytes (duplicate) | ✔ completed     |
| 50  | 2479      | String.getBytes (duplicate) | ✔ completed     |
| 51  | 2743      | String.getBytes             | ✔ completed     |
| 52  | 2484      | String.getBytes             | ✔ completed     |
| 53  | 2407      | String.getBytes             | ✔ completed     |
| 54  | 3587      | String.getBytes             | ✔ completed     |
| 55  | 1645      | String.getBytes             | ✔ completed     |
| 56  | 3718      | ❗ String comparison         | ✔ completed     |
| 57  | 2728      | String.getBytes             | ✔ completed     |
| 58  | 1459      | pending                     | ⏳ pending       |
| 59  | 1724      | String.getBytes (duplicate) | ✔ completed     |
| 60  | 2738      | String.getBytes             | ✔ completed     |
| 61  | 3587      | String.getBytes (duplicate) | ✔ completed     |
| 62  | 2489      | String.getBytes (duplicate) | ✔ completed     |
| 63  | 1624      | String.getBytes             | ✔ completed     |
| 64  | 2504      | String.getBytes             | ✔ completed     |
| 65  | 3125      | String.getBytes (duplicate) | ✔ completed     |
| 66  | 3066      | String.getBytes (duplicate) | ✔ completed     |
| 67  | 2661      | String.getBytes (duplicate) | ✔ completed     |
| 68  | 2481      | String.getBytes (duplicate) | ✔ completed     |
| 69  | 2753      | String.getBytes (duplicate) | ✔ completed     |
| 70  | 2738      | String.getBytes (duplicate) | ✔ completed     |
| 71  | 2728      | String.getBytes (duplicate) | ✔ completed     |
| 72  | 3412      | String.getBytes             | ✔ completed     |
| 73  | 2743      | String.getBytes (duplicate) | ✔ completed     |
| 74  | 2733      | String.getBytes (duplicate) | ✔ completed     |
| 75  | 2748      | String.getBytes             | ✔ completed     |
| 76  | 3063      | String.getBytes (duplicate) | ✔ completed     |
| 77  | 2746      | String.getBytes (duplicate) | ✔ completed     |
| 78  | 815       | DecimalFormat               | 📝 review       |
| 79  | 2499      | String.getBytes             | ✔ completed     |
| 80  | 2494      | String.getBytes (duplicate) | ✔ completed     |
| 81  | 177       | pending                     | ⏳ pending       |
| 82  | 2787      | String.getBytes             | ✔ completed     |

---

## ✅ Replacement Standard Example

```java
stringValue.getBytes(StandardCharsets.UTF_8)

new String(byteArray, StandardCharsets.UTF_8);
```

---
