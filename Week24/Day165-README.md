
# Project Update — Day 165 (October 30, 2025, Thursday)

---

## MODULE: HealthcareSurveyServiceImpl.java

### Code Review & Observations

| #  | Line(s)   | Issue / Observation                                  | Action / Note                                     | Status        |
| -- | --------- | ---------------------------------------------------- | ------------------------------------------------- | ------------- |
| 1  | 614       | Improper use of locals prevents internationalization | ⚠️ Needs refactor for i18n                        | **Not Done**  |
| 2  | 3058      | `SimpleDateFormat` usage                             | ⚠️ Replace with thread-safe / localized formatter | **Not Done**  |
| 3  | 3099      | `SimpleDateFormat` usage                             | ⚠️ Replace with thread-safe / localized formatter | **Not Done**  |
| 4  | 2052      | —                                                    | —                                                 | —             |
| 5  | 561       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 6  | 920       | —                                                    | —                                                 | —             |
| 7  | 3229      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 8  | 429       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 9  | 3432      | `catch` block                                        | To review exception handling                      | —             |
| 10 | 373       | Unknown context                                      | Requires review                                   | ❓ **Pending** |
| 11 | 534       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 12 | 370       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 13 | 2275      | —                                                    | —                                                 | —             |
| 14 | 3034–3050 | —                                                    | —                                                 | —             |
| 15 | 3430      | —                                                    | —                                                 | —             |
| 16 | 2073–2078 | —                                                    | —                                                 | —             |
| 17 | 3776      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 18 | 3915      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 19 | 302       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 20 | 3785      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 21 | 3211      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 22 | 474       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 23 | 2170      | —                                                    | —                                                 | —             |
| 24 | 3773–3781 | —                                                    | —                                                 | —             |
| 25 | 2412      | —                                                    | —                                                 | —             |
| 26 | 300       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 27 | 616       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 28 | 3227      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 29 | 3144      | —                                                    | —                                                 | —             |
| 30 | 658       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 31 | 3451      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 32 | 3205      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 33 | 3224      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 34 | 3906      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 35 | 279       | `catch` block                                        | To review exception handling                      | —             |
| 36 | 2162      | —                                                    | —                                                 | —             |
| 37 | 3449      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 38 | 3222      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 39 | 476       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 40 | 3434      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 41 | 3438      | `catch` block                                        | To review exception handling                      | —             |
| 42 | 3448      | `catch` block                                        | To review exception handling                      | —             |
| 43 | 3444      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 44 | 599       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 45 | 3422      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 46 | 3103      | `catch` block                                        | To review exception handling                      | —             |
| 47 | 2605–2914 | —                                                    | —                                                 | —             |
| 48 | 3213      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 49 | 2400      | —                                                    | —                                                 | —             |
| 50 | 629       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 51 | 3199      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 52 | 3203      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 53 | 613       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 54 | 598       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 55 | 3267      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 56 | 3428      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 57 | 312       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 58 | 299       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 59 | 2048      | —                                                    | —                                                 | —             |
| 60 | 3424      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 61 | 292       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 62 | 501       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 63 | 3216      | —                                                    | —                                                 | —             |
| 64 | 601       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 65 | 1129      | `catch` block                                        | To review exception handling                      | —             |
| 66 | 533       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 67 | 3258      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 68 | 3098      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 69 | 1844      | —                                                    | —                                                 | —             |
| 70 | 3193–3240 | —                                                    | —                                                 | —             |
| 71 | 428       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |
| 72 | 3436      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 73 | 2936      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 74 | 3446      | `SimpleDateFormat` usage                             | ⚠️ Fix required                                   | **Not Done**  |
| 75 | 2040      | —                                                    | —                                                 | —             |
| 76 | 3455      | `catch` block                                        | To review exception handling                      | —             |
| 77 | 627       | Improper use of locals prevents internationalization | ⚠️ Fix required                                   | **Not Done**  |

🟡 **Summary:**
Multiple instances of `SimpleDateFormat` and improper locale usage identified.
→ Plan to replace with `DateTimeFormatter` (Java 8+) and apply locale-aware formatting.
→ Catch blocks to be reviewed for meaningful logging and consistent exception hierarchy.

---

## MODULE: FHIRR4Controller.java

| # | Line(s) | Issue / Observation              | Status      |
| - | ------- | -------------------------------- | ----------- |
| 1 | 188     | `SimpleDateFormat` used directly | ⚠️ Not Done |
| 2 | 189     | `SimpleDateFormat` used directly | ⚠️ Not Done |

🟡 **Action:** Refactor with `DateTimeFormatter` for thread safety and i18n compliance.

---

## MODULE: KioskPatientServiceImpl.java

*No issues logged for this session.*

---

## MODULE: OrderEntryBean.java

| #    | Line(s) | Issue / Observation                  | Action / Note                        | Status          |
| ---- | ------- | ------------------------------------ | ------------------------------------ | --------------- |
| 1–12 | 748–812 | Improper use of locals (i18n issues) | ⚠️ To fix for proper locale handling | **Not Done**    |
| 13   | 534     | `catch` block                        | Reviewed and validated               | ✅ **Completed** |
| 14   | 832     | Improper use of locals (i18n issue)  | ⚠️ Needs correction                  | **Not Done**    |
| 15   | 528     | `catch` block                        | Reviewed and validated               | ✅ **Completed** |
| 16   | 776     | Improper use of locals (i18n issue)  | ⚠️ Needs correction                  | **Not Done**    |

🟢 **Summary:**
Most findings relate to locale-aware string/date operations.
Catch handling blocks are reviewed and approved.

---

## MODULE: ExportQDM.java

| # | Line(s) | Issue / Observation           | Status      |
| - | ------- | ----------------------------- | ----------- |
| 1 | 1185    | `Calendar.getInstance()` used | ⚠️ Not Done |
| 2 | 2116    | —                             | —           |
| 3 | 907     | `Calendar.getInstance()` used | ⚠️ Not Done |
| 4 | 372     | `Calendar.getInstance()` used | ⚠️ Not Done |
| 5 | 3661    | `Calendar.getInstance()` used | ⚠️ Not Done |
| 6 | 272     | —                             | —           |
| 7 | 2156    | Unknown context               | ❓ Pending   |
| 8 | 1058    | `Calendar.getInstance()` used | ⚠️ Not Done |

🟡 **Action:** Replace with `ZonedDateTime.now(ZoneId.of("UTC"))` or similar locale-safe API.

---
