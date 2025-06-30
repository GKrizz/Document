# 🧠 **Daily Progress Log**

> Welcome to my daily journal as a **Software Engineer** — tracking my setup, learning, and development journey.
> This log captures key milestones, technical insights, and hands-on progress in real time.<br>
> **MIPS Documentation** | [View →](Week6/MIPS_Doc.md) 
---

## 📅 **Week 1 Summary**

|    📆 Day | Date         | Day of Week | 🚀 Highlights                                                                                                                                                   | 📄 Link                        |
| --------: | ------------ | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| **Day 1** | May 19, 2025 | Monday      | ⚙️ Setting Up Eclipse, Tomcat, and Java on Linux<br>🌐 GWT Glace Development Environment Setup<br>🗄️ Database Setup                                            | [View →](Week1/Day1-README.md) |
| **Day 2** | May 20, 2025 | Tuesday     | 🔁 Java Servlet • 🛡️ Session Tracking • 📬 Request Management • 📝 JSP                                                                                         | [View →](Week1/Day2-README.md) |
| **Day 3** | May 21, 2025 | Wednesday   | Leave                                                                                                                                                           | -                              |
| **Day 4** | May 22, 2025 | Thursday    | 📦 Cloned & Configured Projects:<br>• `glacelegacy` – Import & Config Completed✅ <br>• `glaceemr` – Import & Config Completed✅<br>• `glaceemr_ui` – Completed ✅ | [View →](Week1/Day3-README.md) |
| **Day 5** | May 23, 2025 | Friday      | 🧭 Menu Source Investigation<br>🧬 “New Patient” Navigation Flow Analysis                                                                                       | [View →](Week1/Day4-README.md) |
| **Day 6** | May 24, 2025 | Saturday    | 🔍 Identify Table Storing Patient Insurance Details                                                                                                             | [View →](Week1/Day5-README.md) |
| **Day 7** | May 25, 2025 | Sunday      | Leave                                                                                                                                                           | -                              |

---

## 📅 **Week 2 Summary**

|                     📆 Day | Date                           | Day of Week             | 🚀 Highlights                                                                                                                                                                                                                                                                 | 📄 Link                                                            |
| -------------------------: | ------------------------------ | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
|                  **Day 8** | May 26, 2025                   | Monday                  | 🆔 Validated Patient Insurance ID change <br> 💾 Analyzed Insurance Save Workflow                                                                                                                                                                                             | [View →](Week2/Day6-README.md)                                     |
|                  **Day 9** | May 27, 2025                   | Tuesday                 | 🔧 Ran UI and MVC Projects  <br>📋 Investigated and updated Relationship Dropdown behavior in Edit UI <br> 🔧 Implemented Default + Auto-fill Logic                                                                                                                           | [View →](Week2/Day7-README.md)                                     |
| **Day 10** <br> **Day 11** | May 28, 2025 <br> May 29, 2025 | Wednesday <br> Thursday | 🔧 UI Modification – Dynamic Positioning of "OK" Button in Insurance Modal                                                                                                                                                                                                    | [View →](Week2/Day8-README.md) |
|                 **Day 12** | May 30, 2025                   | Friday                  | 🖨️ Integrated **After Hours Info** into **Print Appointment** screen <br> 📄 Modified backend (`PrintAppointmentAction.java`) and model (`PrintReportDetails.java`) to include "After Hours" data <br> 🧾 Updated `printAppointment.jsp` to render new section in print view | [View →](Week2/Day10-README.md)                                    |
|                 **Day 13** | May 31, 2025                   | Saturday                | Leave                                                                                                                                                                                                                                                                         | -                                                                  |
|                 **Day 14** | June 1, 2025                   | Sunday                  | Leave                                                                                                                                                                                                                                                                         | -                                                                  |

---

## 📅 **Week 3 Summary**

|     📆 Day | Date         | Day of Week | 🚀 Highlights   | 📄 Link                         |
| ---------: | ------------ | ----------- | --------------- | ------------------------------- |
| **Day 15** | June 2, 2025 | Monday      | 🔁 Traced Scheduler End-to-End Flow: GWT UI → REST → Controller → Service → UI | [View →](Week3/Day15-README.md) |
| **Day 16** | June 3, 2025 | Tuesday     |🟢 **Started working with New Team & Module** <br>🔍 Traced Save Flow in Screening Tab: Edit → Save → Service → DB  | [View →](Week3/Day16-README.md) |
| **Day 17** | June 4, 2025 | Wednesday   |⚙️ Analyzed Backend Auto-Save Logic for HEDIS CPT Codes: BMI, BP, Mammogram, Current Meds <br>✔️ Handles Insert, Update, Delete of CPTs based on Clinical Data | [View →](Week3/Day17-README.md) |
| **Day 18** | June 5, 2025 | Thursday | 🧠 Implemented Auto-Save Logic for Depression Screening CPT (G8510) <br> 📦 Integrated geteditData to extract screening result <br> 🔍 Added logic to insert if result is "Negative" & not already present <br> ⚙️ Enhanced `getCodedBlock()` to utilize a HashMap for lab parameters, avoiding redundant DB calls and improving performance in CDA XML generation. | [View →](Week3/Day18-README.md) |
| **Day 19** | June 6, 2025 | Friday      |🧩 Built narrative & coded blocks for lab results in CDA with optimized data fetching| [View →](Week3/Day19-README.md) |
| **Day 20** | June 7, 2025  | Saturday    | Leave           | -                               |
| **Day 21** | June 8, 2025  | Sunday      | Leave           | -                               |

---

## 📅 **Week 4 Summary**

|     📆 Day | Date          | Day of Week | 🚀 Highlights                                                                                                                                                                                                                                                        | 📄 Link                         |
| ---------: | ------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **Day 22** | June 9, 2025 | Monday      | 🔁 **Refactored `getNarativeBlock()` Method** to optimize lab data rendering <br>⚙️ Replaced repetitive DB calls with bulk fetch using `testDetailId` <br>📦 Stored and printed lab rows in a structured `HashMap` for debugging | [View →](Week4/Day22-README.md) |
| **Day 23** | June 10, 2025 | Tuesday     | 🩺 **Fixed Referral Letter Generation Issue:** <br>🔧 Updated backend logic in `updateReferrals()` to ensure **"Referred By"** field shows the **referring doctor (encounter owner)** instead of the logged-in employee <br>✅ Correctly mapped encounter’s service doctor to referral details | [View →](Week4/Day23-README.md) |
| **Day 24** | June 11, 2025 | Wednesday   | 📜 **Completed Audit Log in Legacy System** <br>🧠 Studied Drools Rule Syntax <br>📚 Deep Dive into MIPS: CMS Structure, Performance Categories, Eligibility Rules, DB Schema, and Reporting Layers                                                                                           | [View →](Week4/Day24-README.md) |
| **Day 25** | June 12, 2025 | Thursday    | **MIPS Performance Flow** (`MUPerformanceRateController`) | [View →](Week4/Day25-README.md) |
| **Day 26** | June 13, 2025 | Friday      | **📂 Main Controller:** `MUPerformanceRateController` deep insight | [View →](Week4/Day26-README.md) |
| **Day 27** | June 14, 2025  | Saturday    | Leave           | -                               |
| **Day 28** | June 15, 2025  | Sunday      | Leave           | -                               |



---

## 📅 **Week 5 Summary**

|     📆 Day | Date          | Day of Week | 🚀 Highlights                                                                                                                          | 📄 Link                         |
| ---------: | ------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **Day 29** | June 16, 2025 | Monday      | 🔍 **Explored `getPatientsSeen(...)` API:** Backend tracing and mode-based logic analysis                                              | [View →](Week5/Day29-README.md) |
| **Day 30** | June 17, 2025 | Tuesday     | 🔍 **Explored `generateAndValidateQDM(...)` API** <br> **Explored `calculateMIPSPerformance(...)` API**                                                                                                                     | [View →](Week5/Day30-README.md) |
| **Day 31** | June 18, 2025 | Wednesday   | 🧠 **GWT + Spring MVC flow:** Learned structured end-to-end interaction between GWT UI and Spring backend                              | [View →](Week5/Day31-README.md) |
| **Day 32** | June 19, 2025 | Thursday    | 🐞 **Fixed UI bug:** Resolved issue where old template data briefly flashed by resetting the view in `start()` before XML fetch        | [View →](Week5/Day32-README.md) |
| **Day 33** | June 20, 2025 | Friday      | Took Leave for TC                                                                                                                     | [View →](Week5/Day33-README.md) |
| **Day 34** | June 21, 2025 | Saturday    | Leave                                                                                                                                   | -                               |
| **Day 35** | June 22, 2025 | Sunday      | Leave                                                                                                                                   | -                               |

---

## 📅 **Week 6 Summary**

|     📆 Day | Date          | Day of Week | 🚀 Highlights                                                                                                                                                                            | 📄 Link                         |
| ---------: | ------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **Day 36** | June 23, 2025 | Monday      | 📑 **MIPS Spec Analysis**: CMS139v13 – *Falls: Screening for Future Fall Risk*                                                                                                           | [View →](Week6/Day36-README.md) |
| **Day 37** | June 24, 2025 | Tuesday     | ✅ Completed **AuditTrail** for Spring & Legacy repos <br><br>📊 Practiced MIPS logic on **CMS139v13:** moved test patient from **❌ NOT MET** ➡️ **MET ✅** via MIPS dashboard walkthrough | [View →](Week6/Day37-README.md) |
| **Day 38** | June 25, 2025 | Wednesday   | 🩺 **CMS138v13 – Tobacco Use Screening & Cessation Intervention** <br>🔍 Explored population logic (IPP, Denominator, Exclusions)                                                        | [View →](Week6/Day38-README.md) |
| **Day 39** <br> **Day 40** | June 26, 2025 <br> June 27, 2025 | Thursday <br>   | 🛠️ Implemented **Referrals to 3 Fax Numbers** for Dr. Gaffney <br>📦 Altered DB schema (VARCHAR(25) ➡️ VARCHAR(100)) <br>🔍 Verified UI/JS and backend integration                      | [View →](Week6/Day39-README.md) |
| **Day 41** | June 28, 2025 | Saturday    | Leave                                                                                                                                                                                    | -                               |
| **Day 42** | June 29, 2025 | Sunday      | Leave                                                                                                                                                                                    | -                               |

---

## 📅 **Week 7 Summary**

|     📆 Day | Date          | Day       | 🚀 Highlights    | 📄 Link                         |
| ---------: | ------------- | --------- | ---------------- | ------------------------------- |
| **Day 43** | June 30, 2025 | Monday    | *(To be filled)* | [View →](Week7/Day43-README.md) |
| **Day 44** | July 1, 2025  | Tuesday   | *(To be filled)* | [View →](Week7/Day44-README.md) |
| **Day 45** | July 2, 2025  | Wednesday | *(To be filled)* | [View →](Week7/Day45-README.md) |
| **Day 46** | July 3, 2025  | Thursday  | *(To be filled)* | [View →](Week7/Day46-README.md) |
| **Day 47** | July 4, 2025  | Friday    | *(To be filled)* | [View →](Week7/Day47-README.md) |
| **Day 48** | July 5, 2025  | Saturday  | **Leave**        | -                               |
| **Day 49** | July 6, 2025  | Sunday    | **Leave**        | -                               |

---

