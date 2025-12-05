

# **Day201 - Code Review Summary**

---

## **1️⃣ DirectMailServiceImpl.java**

| Line(s)  | Issue / Note                       | Status           |
| -------- | ---------------------------------- | ---------------- |
| 371      | GS-Tech Static config campaign     | No change needed |
| 1189     | catch                              | Needs review     |
| 583      | `new String` usage                 | Needs review     |
| 352-389  | Duplicate code → extract to method | No change needed |
| 461      | catch                              | Needs review     |
| 317      | `new String` usage                 | Needs review     |
| 935-949  | Duplicate code → extract to method | No change needed |
| 613      | SimpleDateFormat                   | No change needed |
| 621      | Confirm `mkdirs()` success         | Completed        |
| 538      | GS-Tech Static config campaign     | No change needed |
| 459      | GS-Tech Static config campaign     | No change needed |
| 805      | catch                              | Needs review     |
| 612      | SimpleDateFormat                   | No change needed |
| 473      | GS-Tech Static config campaign     | No change needed |
| 827      | catch                              | Needs review     |
| 901-1088 | `JSONObject.getString(...)`        | No change needed |
| 820      | SimpleDateFormat                   | No change needed |
| 391      | catch                              | Needs review     |
| 357      | GS-Tech Static config campaign     | No change needed |
| 487      | GS-Tech Static config campaign     | No change needed |
| 323      | SimpleDateFormat                   | No change needed |
| 728      | `==` or `!=` comparisons           | Needs review     |
| 189-229  | `JSONObject.getString(...)`        | No change needed |
| 192-202  | Duplicate code → extract to method | No change needed |
| 780      | `new String` usage                 | Needs review     |
| 1213     | SimpleDateFormat                   | No change needed |
| 505      | Cyclomatic complexity              | No change needed |
| 708      | SimpleDateFormat                   | No change needed |
| 1230     | catch                              | Needs review     |
| 406      | GS-Tech Static config campaign     | No change needed |
| 1212     | SimpleDateFormat                   | No change needed |
| 253      | `String.getBytes`                  | Needs review     |
| 170      | Cyclomatic complexity              | No change needed |
| 711      | `==` or `!=` comparisons           | Needs review     |

---

## **2️⃣ SchedulerListViewBean.java**

| Line(s) | Issue / Note                       | Status           |
| ------- | ---------------------------------- | ---------------- |
| 279     | SimpleDateFormat                   | No change needed |
| 401     | SimpleDateFormat                   | No change needed |
| 172-180 | Duplicate code → extract to method | No change needed |
| 286     | SimpleDateFormat                   | No change needed |
| 164     | SimpleDateFormat                   | No change needed |
| 400     | SimpleDateFormat                   | No change needed |
| 170     | SimpleDateFormat                   | No change needed |
| 160-167 | Duplicate code → extract to method | No change needed |
| 394     | SimpleDateFormat                   | No change needed |
| 285     | SimpleDateFormat                   | No change needed |
| 320     | Cyclomatic complexity              | No change needed |
| 94      | Cyclomatic complexity              | No change needed |

---

## **3️⃣ KioskPatientServiceImpl.java**

| Line(s) | Issue / Note                                                    | Status           |
| ------- | --------------------------------------------------------------- | ---------------- |
| 1817    | SimpleDateFormat                                                | No change needed |
| 2927    | catch                                                           | Needs review     |
| 572     | SimpleDateFormat                                                | No change needed |
| 2081    | SimpleDateFormat                                                | No change needed |
| 1244    | catch                                                           | Needs review     |
| 335     | Calendar.getInstance                                            | No change needed |
| 2282    | `==` or `!=` comparisons                                        | Needs review     |
| 2102    | `==` or `!=` comparisons                                        | Needs review     |
| 2135    | `==` or `!=` comparisons                                        | Needs review     |
| 898–903 | `JSONObject.getString(...)`                                     | No change needed |
| 2680    | catch                                                           | Needs review     |
| 2588    | SimpleDateFormat                                                | No change needed |
| 1277    | `==` or `!=` comparisons                                        | Needs review     |
| 1071    | Calendar.getInstance                                            | No change needed |
| 1141    | catch                                                           | Needs review     |
| ...     | (Other lines follow similar pattern, mainly `catch` or `==/!=`) | ...              |
| 3672    | Confirm `mkdirs()` success                                      | Completed        |

> Many other lines involve `Calendar.getInstance`, `SimpleDateFormat`, duplicate code, or catch blocks. Most `SimpleDateFormat` and duplicate code do **not require changes**.

---

## **4️⃣ AlertInboxServiceImpl.java**

| Line(s) | Issue / Note                      | Status           |
| ------- | --------------------------------- | ---------------- |
| 2780    | `==` or `!=`                      | Completed        |
| 162     | Tight coupling with other classes | No change needed |
| 3201    | Hardcoded locale literals         | No change needed |
| 336     | Cyclomatic complexity             | No change needed |

---

## **5️⃣ PortalLoginServiceImpl.java**

| Line(s) | Issue / Note         | Status           |
| ------- | -------------------- | ---------------- |
| 1405    | Calendar.getInstance | No change needed |
| 1082    | InputStreamReader    | Completed        |
| 1130    | InputStreamReader    | Completed        |
| 1119    | String.getBytes      | Pending          |

---
