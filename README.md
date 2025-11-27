# 🧠 **Daily Progress Log**

> Welcome to my daily journal as a **Software Engineer** — tracking my setup, learning, and development journey.
> This log captures key milestones, technical insights, and hands-on progress in real time.<br>
> **MIPS Documentation** | [View →](Week6/MIPS_Doc.md) <br>
> **Cypress Documentation** | [View →](Cypress.md)

---

## 📅 **Week 1 Summary**

|    📆 Day | Date         | Day of Week | 🚀 Highlights                                                                                                                                                   | 📄 Link                        |
| --------: | ------------ | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| **Day 1** | May 19, 2025 | Monday      | ⚙️ Setting Up Eclipse, Tomcat, and Java on Linux<br>🌐 GWT Glace Development Environment Setup<br>🗄️ Database Setup                                            | [View →](Week1/Day1-README.md) |
| **Day 2** | May 20, 2025 | Tuesday     | 🔁 Java Servlet • 🛡️ Session Tracking • 📬 Request Management • 📝 JSP                                                                                         | [View →](Week1/Day2-README.md) |
| **Day 3** | May 21, 2025 | Wednesday   | 🌴 **Leave**                                                                                                                                                           | -                              |
| **Day 4** | May 22, 2025 | Thursday    | 📦 Cloned & Configured Projects:<br>• `glacelegacy` – Import & Config Completed✅ <br>• `glaceemr` – Import & Config Completed✅<br>• `glaceemr_ui` – Completed ✅ | [View →](Week1/Day3-README.md) |
| **Day 5** | May 23, 2025 | Friday      | 🧭 Menu Source Investigation<br>🧬 “New Patient” Navigation Flow Analysis                                                                                       | [View →](Week1/Day4-README.md) |
| **Day 6** | May 24, 2025 | Saturday    | 🔍 Identify Table Storing Patient Insurance Details                                                                                                             | [View →](Week1/Day5-README.md) |
| **Day 7** | May 25, 2025 | Sunday      | 🌴 **Leave**                                                                                                                                                           | -                              |

---

## 📅 **Week 2 Summary**

|                     📆 Day | Date                           | Day of Week             | 🚀 Highlights                                                                                                                                                                                                                                                                 | 📄 Link                                                            |
| -------------------------: | ------------------------------ | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
|                  **Day 8** | May 26, 2025                   | Monday                  | 🆔 Validated Patient Insurance ID change <br> 💾 Analyzed Insurance Save Workflow                                                                                                                                                                                             | [View →](Week2/Day6-README.md)                                     |
|                  **Day 9** | May 27, 2025                   | Tuesday                 | 🔧 Ran UI and MVC Projects  <br>📋 Investigated and updated Relationship Dropdown behavior in Edit UI <br> 🔧 Implemented Default + Auto-fill Logic                                                                                                                           | [View →](Week2/Day7-README.md)                                     |
| **Day 10** <br> **Day 11** | May 28, 2025 <br> May 29, 2025 | Wednesday <br> Thursday | 🔧 UI Modification – Dynamic Positioning of "OK" Button in Insurance Modal                                                                                                                                                                                                    | [View →](Week2/Day8-README.md) |
|                 **Day 12** | May 30, 2025                   | Friday                  | 🖨️ Integrated **After Hours Info** into **Print Appointment** screen <br> 📄 Modified backend (`PrintAppointmentAction.java`) and model (`PrintReportDetails.java`) to include "After Hours" data <br> 🧾 Updated `printAppointment.jsp` to render new section in print view | [View →](Week2/Day10-README.md)                                    |
|                 **Day 13** | May 31, 2025                   | Saturday                | 🌴 **Leave**                                                                                                                                                                                                                                                                         | -                                                                  |
|                 **Day 14** | June 1, 2025                   | Sunday                  | 🌴 **Leave**                                                                                                                                                                                                                                                                         | -                                                                  |

---

## 📅 **Week 3 Summary**

|     📆 Day | Date         | Day of Week | 🚀 Highlights   | 📄 Link                         |
| ---------: | ------------ | ----------- | --------------- | ------------------------------- |
| **Day 15** | June 2, 2025 | Monday      | 🔁 Traced Scheduler End-to-End Flow: GWT UI → REST → Controller → Service → UI | [View →](Week3/Day15-README.md) |
| **Day 16** | June 3, 2025 | Tuesday     |🟢 **Started working with New Team & Module (MIPS)** <br>🔍 Traced Save Flow in Screening Tab: Edit → Save → Service → DB  | [View →](Week3/Day16-README.md) |
| **Day 17** | June 4, 2025 | Wednesday   |⚙️ Analyzed Backend Auto-Save Logic for HEDIS CPT Codes: BMI, BP, Mammogram, Current Meds <br>✔️ Handles Insert, Update, Delete of CPTs based on Clinical Data | [View →](Week3/Day17-README.md) |
| **Day 18** | June 5, 2025 | Thursday | 🧠 Implemented Auto-Save Logic for Depression Screening CPT (G8510) <br> 📦 Integrated geteditData to extract screening result <br> 🔍 Added logic to insert if result is "Negative" & not already present <br> ⚙️ Enhanced `getCodedBlock()` to utilize a HashMap for lab parameters, avoiding redundant DB calls and improving performance in CDA XML generation. | [View →](Week3/Day18-README.md) |
| **Day 19** | June 6, 2025 | Friday      |🧩 Built narrative & coded blocks for lab results in CDA with optimized data fetching| [View →](Week3/Day19-README.md) |
| **Day 20** | June 7, 2025  | Saturday    | 🌴 **Leave**           | -                               |
| **Day 21** | June 8, 2025  | Sunday      | 🌴 **Leave**           | -                               |

---

## 📅 **Week 4 Summary**

|     📆 Day | Date          | Day of Week | 🚀 Highlights                                                                                                                                                                                                                                                        | 📄 Link                         |
| ---------: | ------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **Day 22** | June 9, 2025 | Monday      | 🔁 **Refactored `getNarativeBlock()` Method** to optimize lab data rendering <br>⚙️ Replaced repetitive DB calls with bulk fetch using `testDetailId` <br>📦 Stored and printed lab rows in a structured `HashMap` for debugging | [View →](Week4/Day22-README.md) |
| **Day 23** | June 10, 2025 | Tuesday     | 🩺 **Fixed Referral Letter Generation Issue:** <br>🔧 Updated backend logic in `updateReferrals()` to ensure **"Referred By"** field shows the **referring doctor (encounter owner)** instead of the logged-in employee <br>✅ Correctly mapped encounter’s service doctor to referral details | [View →](Week4/Day23-README.md) |
| **Day 24** | June 11, 2025 | Wednesday   | 📜 **Completed Audit Log in Legacy System** <br>🧠 Studied Drools Rule Syntax <br>📚 Deep Dive into MIPS: CMS Structure, Performance Categories, Eligibility Rules, DB Schema, and Reporting Layers                                                                                           | [View →](Week4/Day24-README.md) |
| **Day 25** | June 12, 2025 | Thursday    | **MIPS Performance Flow** (`MUPerformanceRateController`) | [View →](Week4/Day25-README.md) |
| **Day 26** | June 13, 2025 | Friday      | **📂 Main Controller:** `MUPerformanceRateController` deep insight | [View →](Week4/Day26-README.md) |
| **Day 27** | June 14, 2025  | Saturday    | 🌴 **Leave**           | -                               |
| **Day 28** | June 15, 2025  | Sunday      | 🌴 **Leave**           | -                               |



---

## 📅 **Week 5 Summary**

|     📆 Day | Date          | Day of Week | 🚀 Highlights                                                                                                                          | 📄 Link                         |
| ---------: | ------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **Day 29** | June 16, 2025 | Monday      | 🔍 **Explored `getPatientsSeen(...)` API:** Backend tracing and mode-based logic analysis                                              | [View →](Week5/Day29-README.md) |
| **Day 30** | June 17, 2025 | Tuesday     | 🔍 **Explored `generateAndValidateQDM(...)` API** <br> **Explored `calculateMIPSPerformance(...)` API**                                                                                                                     | [View →](Week5/Day30-README.md) |
| **Day 31** | June 18, 2025 | Wednesday   | 🧠 **GWT + Spring MVC flow:** Learned structured end-to-end interaction between GWT UI and Spring backend                              | [View →](Week5/Day31-README.md) |
| **Day 32** | June 19, 2025 | Thursday    | 🐞 **Fixed UI bug:** Resolved issue where old template data briefly flashed by resetting the view in `start()` before XML fetch        | [View →](Week5/Day32-README.md) |
| **Day 33** | June 20, 2025 | Friday      | 🌴 Took **Leave** for TC                                                                                                                     | [View →](Week5/Day33-README.md) |
| **Day 34** | June 21, 2025 | Saturday    | 🌴 **Leave**                                                                                                                                   | -                               |
| **Day 35** | June 22, 2025 | Sunday      | 🌴 **Leave**                                                                                                                                   | -                               |

---

## 📅 **Week 6 Summary**

|     📆 Day | Date          | Day of Week | 🚀 Highlights                                                                                                                                                                            | 📄 Link                         |
| ---------: | ------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **Day 36** | June 23, 2025 | Monday      | 📑 **MIPS Spec Analysis**: CMS139v13 – *Falls: Screening for Future Fall Risk*                                                                                                           | [View →](Week6/Day36-README.md) |
| **Day 37** | June 24, 2025 | Tuesday     | ✅ Completed **AuditTrail** for Spring & Legacy repos <br><br>📊 Practiced MIPS logic on **CMS139v13:** moved test patient from **❌ NOT MET** ➡️ **MET ✅** via MIPS dashboard walkthrough | [View →](Week6/Day37-README.md) |
| **Day 38** | June 25, 2025 | Wednesday   | 🩺 **CMS138v13 – Tobacco Use Screening & Cessation Intervention** <br>🔍 Explored population logic (IPP, Denominator, Exclusions)                                                        | [View →](Week6/Day38-README.md) |
| **Day 39** <br> **Day 40** | June 26, 2025 <br> June 27, 2025 | Thursday <br>   | 🛠️ Implemented **Referrals to 3 Fax Numbers** for Dr. Gaffney <br>📦 Altered DB schema (VARCHAR(25) ➡️ VARCHAR(100)) <br>🔍 Verified UI/JS and backend integration                      | [View →](Week6/Day39-README.md) |
| **Day 41** | June 28, 2025 | Saturday    | 🌴 **Leave**                                                                                                                                                                                    | -                               |
| **Day 42** | June 29, 2025 | Sunday      | 🌴 **Leave**                                                                                                                                                                                    | -                               |

---

## 📅 **Week 7 Summary**

|     📆 Day | Date          | Day       | 🚀 Highlights    | 📄 Link                         |
| ---------: | ------------- | --------- | ---------------- | ------------------------------- |
| **Day 43** | June 30, 2025 | Monday    | 🛠️ **Configuration Page Bug Fixes** & 🩺 **Referring Physician Search Picker Updates** | [View →](Week7/Day43-README.md) |
| **Day 44** | July 1, 2025  | Tuesday   | 🧱 **Master** <br> ⚙️ **Legacy** Project Setup,<br> 🔨 **Spring** Backend Configuration,<br> 🎨 **UI** Dev Mode Execution| [View →](Week7/Day44-README.md) |
| **Day 45** | July 2, 2025  | Wednesday | 🛠️ **Assigned Chart Page/Desktop Issues**: Release of Records Toolbar, Progress Notes, TeleHealth Failures, New Encounter Import, and More | [View →](Week7/Day45-README.md) |
| **Day 46** | July 3, 2025  | Thursday  | 	🧩 **Referring Doctor Fax Format Fix** <br> 🔍 Audit Trail Prep <br> 🛠️ LeafModel Chart Call Review m <br> 🔐 SFTP File Transfer Setup | [View →](Week7/Day46-README.md) |
| **Day 47** | July 4, 2025  | Friday    | 🧠**SOAP Tab Load Flow Analysis:** LeafModelFrame → NotesPreview → TabDetail | [View →](Week7/Day47-README.md) |
| **Day 48** | July 5, 2025  | Saturday  | **🌴 **Leave****        | -                               |
| **Day 49** | July 6, 2025  | Sunday    | **🌴 **Leave****        | -                               |

---

## 📅 **Week 8 Summary**


|     📆 Day | Date          | Day       | 🚀 Highlights    | 📄 Link                         |
| ---------: | ------------- | --------- | ---------------- | ------------------------------- |
| **Day 50** | July 7, 2025  | Monday    | 🔍 Exploring Tab Load Mechanism → **Custom1 Tab** | [View →](Week8/Day50-README.md) |
| **Day 51** | July 8, 2025  | Tuesday   | *🛠️ **Fax** logic fix, **Fax Pages List** , DB schema patch | [View →](Week8/Day51-README.md) |
| **Day 52** | July 9, 2025  | Wednesday |⚙️ **Practice Settings** – Enable Custom XML Desktop View & Switch Link | [View →](Week8/Day52-README.md) |
| **Day 53** | July 10, 2025 | Thursday  |  Explored **XML API flows** (/getXMLFile, /addShortcut, /fetchShortCut, /saveXMLTab) and investigated **lab results** not display issue. | [View →](Week8/Day53-README.md) |
| **Day 54** | July 11, 2025 | Friday    | XML API Endpoints | [View →](Week8/Day54-README.md) |
| **Day 55** | July 12, 2025 | Saturday  | 🌴 **Leave**     | -                               |
| **Day 56** | July 13, 2025 | Sunday    | 🌴 **Leave**     | -                               |


---

## 📅 **Week 9 Summary**

|     📆 Day | Date          | Day       | 🚀 Highlights       | 📄 Link                         |
| ---------: | ------------- | --------- | ------------------- | ------------------------------- |
|         |               |         | **Chart Page/Desktop Issues__and__New Window Chart View**| [Issues →](Week9/Chat_and_desktop_Issues-README.md) |
| **Day 57** | July 14, 2025 | Monday    | 🛠️ Worked on issues in Tandon Stage (Chart/Desktop):<br>✅ Fixed Progress Note **shortcut code display bug**<br>✅ Debugged **Import dropdown issue** | [View →](Week9/Day57-README.md) |
| **Day 58** | July 15, 2025 | Tuesday   | 🐞 **Case Completed** => **Case Number**: **233445**<br>✴️ Implemented multi-fax broadcast feature for referring physicians<br>✴️ Updated UI and backend to support multiple fax numbers<br>✴️ Tested and verified fax dispatch functionality | [View →](Week9/Day58-README.md)|
| **Day 59** | July 16, 2025 | Wednesday | ✅ Refactored referral logic to show **Service Doctor** as "Referred By"<br>✅ Debug/debug print statements for legacy CNM view | [View →](Week9/Day59-README.md) |
| **Day 60** | July 17, 2025 | Thursday  | 🧠 **Case**# **234293** (Assigned)<br>✅ Identified and fixed bug showing **staff name instead of Service Doctor** in legacy view referrals<br>🛠️ Updated Java logic in `PlanElements.java`, verified debug logs | [View →](Week9/Day60-README.md) |
| **Day 61** | July 18, 2025 | Friday    | ✅ Updated the assigned **Case**# **234293** in Testing | [View →](Week9/Day61-README.md) |
| **Day 62** | July 19, 2025 | Saturday  | 🌴 **Leave**        | -                               |
| **Day 63** | July 20, 2025 | Sunday    | 🌴 **Leave**        | -                               |
                      

---

## 📅 Week 10 Summary

|     📆 Day | Date          | Day       | 🚀 Highlights                        | 📄 Link                                                |
| ---------: | ------------- | --------- | ------------------------------------ | ------------------------------------------------------ |
| **Day 64** | July 21, 2025 | Monday    | Analysed Chart Page/Desktop XML issues and New Window Chart View             | [View →](Week10/Day64-README.md)                       |
| **Day 65** | July 22, 2025 | Tuesday   | **SFTP** Session             | [View →](Week10/Day65-README.md)                       |
| **Day 66** | July 23, 2025 | Wednesday | 🧭 **Module:** Direct (CDA Interface) <br> 🛠️ Fixed invalid `\x02` character in `doc_presc_intake` values             | [View →](Week10/Day66-README.md)                       |
| **Day 67** | July 24, 2025 | Thursday  | 🛠️  **New Window Chart View – Headache Questionnaire** <br> 🛠️ Fixed **bottom date picker** was **partially hidden** in the New Window Chart View.            | [View →](Week10/Day67-README.md)                       |
| **Day 68** | July 25, 2025 | Friday    | **Flow of Working** — Saving Data on Tab Click (e.g., Plan Tab)             | [View →](Week10/Day68-README.md)                       |
| **Day 69** | July 26, 2025 | Saturday  | 🌴 **Leave**                         | -                                                      |
| **Day 70** | July 27, 2025 | Sunday    | 🌴 **Leave**                         | -                                                      |

---

## 📅 Week 11 Summary

|     📆 Day | Date          | Day       | 🚀 Highlights                        | 📄 Link                                                |
| ---------: | ------------- | --------- | ------------------------------------ | ------------------------------------------------------ |
| **Day 71** | July 28, 2025 | Monday    | 🛠️ **New Window Chart View - IM Progress Notes** <br> Plan data not saved         | [View →](Week11/Day71-README.md)                       |
| **Day 72** | July 29, 2025 | Tuesday   |  🔹 **Case# 234691** (Assigned) <br>   🛠️ **Faxing Labs** - Referring Doctor Info Not Auto-Populating   <br> Bug fixed by properly encoding URL parameters      | [View →](Week11/Day72-README.md)                       |
| **Day 73** | July 30, 2025 | Wednesday | 🛠️ **New Window Chart View** -  **Doctor Incident** <br>  Data not saved            | [View →](Week11/Day73-README.md)                       |
| **Day 74** | July 31, 2025 | Thursday  | **OpenFaxPickerAction.java** and **OpenFaxPicker.jsp**             |  [View →](Week11/Day74-README.md)                       |
| **Day 75** | August 1, 2025 | Friday   | 🔄 Content to be updated             | [View →](Week11/Day75-README.md)                       |
| **Day 76** | August 2, 2025 | Saturday | 🌴 **Leave**                         | -                                                      |
| **Day 77** | August 3, 2025 | Sunday   | 🌴 **Leave**                         | -                                                      |

---

## 📅 Week 12 Summary

|     📆 Day | Date            | Day       | 🚀 Highlights    | 📄 Link                          |
| ---------: | --------------- | --------- | ---------------- | -------------------------------- |
| **Day 78** | August 4, 2025  | Monday    | 🛠️ **IM Progress Notes** – PE Data Missing in Modern View | [View →](Week12/Day78-README.md) |
| **Day 79** | August 5, 2025  | Tuesday   | 🧾 **Legacy XML to HTML Transformation** via XSLT | [View →](Week12/Day79-README.md) |
| **Day 80** | August 6, 2025  | Wednesday | 🧾 **Lab Investigation Utility** – `getDiscreteInvestigationDetail` | [View →](Week12/Day80-README.md) |
| **Day 81** | August 7, 2025  | Thursday  | 🧾 **XML → HTML Transformation Flow** (Legacy, with Debugging Guide) | [View →](Week12/Day81-README.md) |
| **Day 82** | August 8, 2025  | Friday    |  **Clinical Data Debugging** – QDM, Lab, Assessment, Procedure Trace Logs | [View →](Week12/Day82-README.md) |
| **Day 83** | August 9, 2025  | Saturday  | 🌴 **Leave**     | -                                |
| **Day 84** | August 10, 2025 | Sunday    | 🌴 **Leave**     | -                                |

---

## 📅 Week 13 Summary

|     📆 Day | Date            | Day       | 🚀 Highlights    | 📄 Link                          |
| ---------: | --------------- | --------- | ---------------- | -------------------------------- |
| **Day 85** | August 11, 2025 | Monday    | 📄 CMS**130**v13 – README Draft for Colorectal Cancer Screening Measure | [View →](Week13/Day85-README.md) |
| **Day 86** | August 12, 2025 | Tuesday   | CMS**125**v13 Rule Validation – Frailty, AIFrailLTCF, and LTCF Exclusion Logic | [View →](Week13/Day86-README.md) |
| **Day 87** | August 13, 2025 | Wednesday | **CMS125v13 — Breast Cancer** Screening Logic & Drools Rules | [View →](Week13/Day87-README.md) |
| **Day 88** | August 14, 2025 | Thursday  | **API Analysis:** Generate and Validate QDM | [View →](Week13/Day88-README.md) |
| **Day 89** | August 15, 2025 | Friday    | 🌴 **Leave**     | -                                |
| **Day 90** | August 16, 2025 | Saturday  | 🌴 **Leave**     | -                                |
| **Day 91** | August 17, 2025 | Sunday    | 🌴 **Leave**     | -                                |

---

## 📅 Week 14 Summary

|     📆 Day | Date            | Day       | 🚀 Highlights                                                         | 📄 Link                          |
| ---------: | --------------- | --------- | --------------------------------------------------------------------- | -------------------------------- |
| **Day 92** | August 18, 2025 | Monday    | ✅ **CMS125v13** — Denominator Exclusion (Advanced Illness)            | [View →](Week14/Day92-README.md) |
| **Day 93** | August 19, 2025 | Tuesday   | ✅ **Colorectal Cancer Screening** — Spec Diff Analysis (2024 vs 2025) | [View →](Week14/Day93-README.md) |
| **Day 94** | August 20, 2025 | Wednesday | ✅ **Breast Cancer Screening (CMS125v13)** — Drools Rule Fix           | [View →](Week14/Day94-README.md) |
| **Day 95** | August 21, 2025 | Thursday  | ✅ **Tandon XML Issues (ROS & PE)** — SOAP Rendering Fix               | [View →](Week14/Day95-README.md) |
| **Day 96** | August 22, 2025 | Friday    | ✅ **Cypress** — MIPS Provider Config & CMS2v14 Validation             | [View →](Week14/Day96-README.md) |
| **Day 97** | August 23, 2025 | Saturday  | 🌴 **Leave**                                                          | -                                |
| **Day 98** | August 24, 2025 | Sunday    | 🌴 **Leave**                                                          | -                                |

---

## 📅 Week 15 Summary


| 📆 Day      | Date            | Day       | 🚀 Highlights                                                                | 📄 Link                           |
| ----------- | --------------- | --------- | ---------------------------------------------------------------------------- | --------------------------------- |
| **Day 99**  | August 25, 2025 | Monday    | ✅ **Cypress Validation** <br> **CMS125v13** *Breast Cancer Screening*             | [View →](Week15/Day99-README.md)  |
| **Day 100** | August 26, 2025 | Tuesday   | ✅ **QDM Data Validation** & **Backend QRDA Logic** – Comparison with Cypress | [View →](Week15/Day100-README.md) |
| **Day 101** | August 27, 2025 | Wednesday | 🌴 **Leave**                                                                 | [View →](Week15/Day101-README.md) |
| **Day 102** | August 28, 2025 | Thursday  | 🗂️ **SFTP File Transfers** & *Scientific Notation Audit in ECQM Data*       | [View →](Week15/Day102-README.md) |
| **Day 103** | August 29, 2025 | Friday    | ✅ **CMS154v13** – *Passed 2025 eCQM <br>Test: Appropriate Treatment for URI*     | [View →](Week15/Day103-README.md) |
| **Day 104** | August 30, 2025 | Saturday  | 🌴 **Leave**                                                                 | -                                 |
| **Day 105** | August 31, 2025 | Sunday    | 🌴 **Leave**                                                                 | -                                 |


---

## 📅 Week 16 Summary

| 📆 Day      | Date              | Day       | 🚀 Highlights                                                                                                                              | 📄 Link                           |
| ----------- | ----------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------- |
| **Day 106** | September 1, 2025 | Monday    | ✅ **CMS149v13 Dementia: Cognitive Assessment** <br> • MIPS Provider Setup <br> • Risk Assessment Updates <br> • QRDA III Validation Passed | [View →](Week16/Day106-README.md) |
| **Day 107** | September 2, 2025 | Tuesday   | ✅ **CMS149v13 Dementia: Cognitive Assessment** <br> • Continued Updates and Fixes <br> • QRDA I Validation Passed                          | [View →](Week16/Day107-README.md) |
| **Day 108** | September 3, 2025 | Wednesday | ✅ **CMS159v13: Depression Remission at Twelve Months**                                                                                                                        | [View →](Week16/Day108-README.md) |
| **Day 109** | September 4, 2025 | Thursday  | ✅ **CMS159v13: Depression Remission at Twelve Months**                                                                                                                         | [View →](Week16/Day109-README.md) |
| **Day 110** | September 5, 2025 | Friday    | ⏳ **CMS145v13 – Coronary Artery Disease (CAD) (Pending)**                                                                                                                         | [View →](Week16/Day110-README.md) |
| **Day 111** | September 6, 2025 | Saturday  | 🌴 **Leave**                                                                                                                               | -                                 |
| **Day 112** | September 7, 2025 | Sunday    | 🌴 **Leave**                                                                                                                               | -                                 |

---

## 📅 Week 17 Summary


| 📆 Day      | Date               | Day       | 🚀 Highlights      | 📄 Link                           |
| ----------- | ------------------ | --------- | ------------------ | --------------------------------- |
| **Day 113** | September 8, 2025  | Monday    | ⏳ **CMS145v13 – Coronary Artery Disease (CAD) (Pending)** | [View →](Week17/Day113-README.md) |
| **Day 114** | September 9, 2025  | Tuesday   | ✅ **🏥 Superbill – Add CPT II Codes** | [View →](Week17/Day114-README.md) |
| **Day 115** | September 10, 2025 | Wednesday | ✅ **🏥 Superbill – Add CPT II Codes** | [View →](Week17/Day115-README.md) |
| **Day 116** | September 11, 2025 | Thursday  | ✅ **UI & Backend:** Added CPT II code auto-populate button and logic in Superbill | [View →](Week17/Day116-README.md) |
| **Day 117** | September 12, 2025 | Friday    | Cardiac Surgery Procedures (CPT 92920, 33510) and Encounter Queries included | [View →](Week17/Day117-README.md) |
| **Day 118** | September 13, 2025 | Saturday  | 🌴 **Leave**       | -                                 |
| **Day 119** | September 14, 2025 | Sunday    | 🌴 **Leave**       | -                                 |

---

## 📅 Week 18 Summary


| 📆 Day      | Date               | Day       | 🚀 Highlights      | 📄 Link                           |
| ----------- | ------------------ | --------- | ------------------ | --------------------------------- |
| **Day 120** | September 15, 2025 | Monday    | ✅ CPT II Auto-Populate committed; CMS145v13 pending QRDA III | [View →](Week18/Day120-README.md) |
| **Day 121** | September 16, 2025 | Tuesday   | 🌴 **Leave**       | [View →](Week18/Day121-README.md) |
| **Day 122** | September 17, 2025 | Wednesday | ✅**CMS137v13 – Initiation and Engagement of Substance Use Disorder Treatment** | [View →](Week18/Day122-README.md) |
| **Day 123** | September 18, 2025 | Thursday  | 🔄 *To be updated* | [View →](Week18/Day123-README.md) |
| **Day 124** | September 19, 2025 | Friday    | ✅**CMS56v13 – Functional Status Assessment for Total Hip Replacement** | [View →](Week18/Day124-README.md) |
| **Day 125** | September 20, 2025 | Saturday  | ✅**CMS771v6 – IPSS or AUA-SI Symptom Score Change 6–12 Months After BPH Diagnosis** | [View →](Week18/Day125-README.md)                                  |
| **Day 126** | September 21, 2025 | Sunday    | 🌴 **Leave**       | -                                 |

---

## 📅 Week 19 Summary

| 📆 Day      | Date               | Day       | 🚀 Highlights      | 📄 Link                           |
| ----------- | ------------------ | --------- | ------------------ | --------------------------------- |
| **Day 127** | September 22, 2025 | Monday    | ✅**CMS90v14 – Functional Status Assessments for Heart Failure** | [View →](Week19/Day127-README.md) |
| **Day 128** | September 23, 2025 | Tuesday   | ✅**Superbill** - Show "**Auto Populate**" Button Based on HEDIS Configuration| [View →](Week19/Day128-README.md) |
| **Day 129** | September 24, 2025 | Wednesday | ✅**CMS146v13 - Appropriate Testing for Pharyngitis** | [View →](Week19/Day129-README.md) |
| **Day 130** | September 25, 2025 | Thursday  | **Amazon code guru** | [View →](Week19/Day130-README.md) |
| **Day 131** | September 26, 2025 | Friday    | **Amazon code guru :** <br>🧭 **Reviewed Generic Exception Handling <br> ✅ CWE-117 (Log Injection)** | [View →](Week19/Day131-README.md) |
| **Day 132** | September 27, 2025 | Saturday  | 🌴 **Leave**       | -                                 |
| **Day 133** | September 28, 2025 | Sunday    | 🌴 **Leave**       | -                                 |

---

## 📅 Week 20 Summary

| 📆 Day      | Date               | Day       | 🚀 Highlights | 📄 Link                           |
| ----------- | ------------------ | --------- | ------------- | --------------------------------- |
| **Day 134** | September 29, 2025 | Monday    | **Amazon code guru :** <br> **CWE-20 - Invalid public <br> CWE-252,754 - Missing check [NO CHANGE] <br> CWE-352 - Cross-site <br>CWE-390 - Catching**             | [View →](Week20/Day134-README.md) |
| **Day 135** | September 30, 2025 | Tuesday   | **Amazon code guru :** <br> **CWE-390 - Catching <br>CWE-400,664**             | [View →](Week20/Day135-README.md) |
| **Day 136** | October 1, 2025    | Wednesday |  🌴 **Leave**             | -  |
| **Day 137** | October 2, 2025    | Thursday  |  🌴 **Leave**             | -  |
| **Day 138** | October 3, 2025    | Friday    |  🌴 **Took Leave** for saraswathi pooja            | -  |
| **Day 139** | October 4, 2025    | Saturday  |   🌴 **Leave**            | -  |
| **Day 140** | October 5, 2025    | Sunday    |  🌴 **Leave**             | -  |

---

## 📅 Week 21 Summary

| 📆 Day      | Date             | Day       | 🚀 Highlights | 📄 Link                           |
| ----------- | ---------------- | --------- | ------------- | --------------------------------- |
| **Day 141** | October 6, 2025  | Monday    |**CWE-400,664**               | [View →](Week21/Day141-README.md) |
| **Day 142** | October 7, 2025  | Tuesday   |**CWE-400,664**               | [View →](Week21/Day142-README.md) |
| **Day 143** | October 8, 2025  | Wednesday |**CWE-400,664** - committed in dev2               | [View →](Week21/Day143-README.md) |
| **Day 144** | October 9, 2025  | Thursday  |**CWE-755 (Stack Trace Handling)**               | [View →](Week21/Day144-README.md) |
| **Day 145** | October 10, 2025 | Friday    | **CWE-755 (Stack Trace Handling)** - committed in dev2              | [View →](Week21/Day145-README.md) |
| **Day 146** | October 11, 2025 | Saturday  | 🌴 **Leave**  | -                                 |
| **Day 147** | October 12, 2025 | Sunday    | 🌴 **Leave**  | -                                 |

---


## 📅 Week 22 Summary

|    📆 Day   | Date             | Day       | 🚀 Highlights                                                                                                                                                                                            | 📄 Link                           |
| :---------: | :--------------- | :-------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------- |
| **Day 148** | October 13, 2025 | Monday    | **Amazon code guru :** <br>🧩 **CWE-611 XML Security Audit** — Hardened XML parsing/generation across 60+ service files, fixed XXE issues, reviewed ClinicalNoteServiceImpl, ClinicalElementsServiceImpl, and XMLGenerator modules. | [View →](Week22/Day148-README.md) |
| **Day 149** | October 14, 2025 | Tuesday   | ⚙️ Follow-up fixes & regression testing for XML modules · Validated API endpoints · Finalized secure document builder configs.                                                                           | [View →](Week22/Day149-README.md) |
| **Day 150** | October 15, 2025 | Wednesday | 🧪 QA and integration testing · Verified Tomcat stage deployment · Validated FHIR and QRDA service responses.                                                                                            | [View →](Week22/Day150-README.md) |
| **Day 151** | October 16, 2025 | Thursday  | 🔧 Code cleanup and documentation · Prepared commit summaries · Synced next-release modules.                                                                                                             | [View →](Week22/Day151-README.md) |
| **Day 152** | October 17, 2025 | Friday    | 🚀 Release readiness check · Verified pending APIs · Final cross-service XML validation complete.                                                                                                        | [View →](Week22/Day152-README.md) |
| **Day 153** | October 18, 2025 | Saturday  | 🌴 **Leave**                                                                                                                                                                                             | -                                 |
| **Day 154** | October 19, 2025 | Sunday    | 🌴 **Leave**                                                                                                                                                                                             | -                                 |


---

## 📅 Week 23 Summary

| 📆 Day      | Date             | Day       | 🚀 Highlights | 📄 Link                           |
| ----------- | ---------------- | --------- | ------------- | --------------------------------- |
| **Day 155** | October 20, 2025 | Monday    | 🌴 **Leave**  | -                                 |
| **Day 156** | October 21, 2025 | Tuesday   | 🌴 **Took Leave** for Diwali  | -                                 |
| **Day 157** | October 22, 2025 | Wednesday | **Amazon code guru :** <br> Fix **Apache Xalan** IllegalArgumentException for **accessExternalDTD** by forcing JDK TransformerFactory | [View →](Week23/Day157-README.md) |
| **Day 158** | October 23, 2025 | Thursday  |  **Amazon code guru :** <br> **DirectEmail.java** <br> **HtmlTransformer.java** <br> **XmlGenerationServiceImpl.java**            | [View →](Week23/Day158-README.md) |
| **Day 159** | October 24, 2025 | Friday    | **Amazon code guru :** <br> 🧾 Fixed all **XLS issues** (577 total) <br> Reviewed **CWE-20** input validation <br> Ensured safe XML parser config (`disallow-doctype-decl`)   | [View →](Week23/Day159-README.md) |
| **Day 160** | October 25, 2025 | Saturday  | 🌴 **Leave**  | -                                 |
| **Day 161** | October 26, 2025 | Sunday    | 🌴 **Leave**  | -                                 |

---

## 📅 Week 24 Summary

| 📆 Day      | Date             | Day       | 🚀 Highlights                                                                                                                                                                         | 📄 Link                           |
| ----------- | ---------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| **Day 162** | October 27, 2025 | Monday    |**Amazon code guru :** <br>  Finalized updates in **EmailDocument** and **XmlGenerationServiceImpl**; moved changes to **testing + master**.                                                                       | [View →](Week24/Day162-README.md) |
| **Day 163** | October 28, 2025 | Tuesday   | **Amazon code guru :** <br> Reviewed and refactored **EmailDocumentServiceImpl** — focused on charset handling, exception logging, and endpoint testing.                                                          | [View →](Week24/Day163-README.md) |
| **Day 164** | October 29, 2025 | Wednesday | **Amazon code guru :** <br> Updated **FHIREncounterServiceImpl**, **SSORequester**, and **RxHistoryServiceImpl** — refined exception handling and reviewed i18n.                                                  | [View →](Week24/Day164-README.md) |
| **Day 165** | October 30, 2025 | Thursday  | **Amazon code guru :** <br> Performed large-scale review in **HealthcareSurveyServiceImpl**, **OrderEntryBean**, **FHIRR4Controller**, and **ExportQDM** for `SimpleDateFormat`, i18n, and exception consistency. | [View →](Week24/Day165-README.md) |
| **Day 166** | October 31, 2025 | Friday    | **Amazon code guru :** <br> 📚 Studied **Playwright** (E2E testing) and **React** fundamentals; explored integration for UI automation.                                                                           | [View →](Week24/Day166-README.md) |
| **Day 167** | November 1, 2025 | Saturday  | 🌴 **Leave**                                                                                                                                                                          | –                                 |
| **Day 168** | November 2, 2025 | Sunday    | 🌴 **Leave**                                                                                                                                                                          | –                                 |

---

## 📅 Week 25 Summary


| 📆 Day      | Date             | Day       | 🚀 Highlights | 📄 Link                           |
| ----------- | ---------------- | --------- | ------------- | --------------------------------- |
| **Day 169** | November 3, 2025 | Monday    | **Amazon code guru :** <br> Updated multiple services and utilities: <br>• Fixed string encoding issues in **HttpConnectionUtils.java** and **ExportHTMLAsPdf.java** <br>• Replaced `==` with `.equals()` in **OrdersServiceImpl.java** <br>• Reviewed i18n issues in **BillingConfigServiceImpl.java** <br>• Updated REST `@RequestMapping` in **FHIREncounterServiceImpl**, **HealthcareSurveyServiceImpl**, **OrderEntryBean**, and **ExportQDM** <br>• Improved exception handling and logging across **SSORequester**, **HealthcareSurveyServiceImpl**, and **OrderEntryBean**              | [View →](Week25/Day169-README.md) |
| **Day 170** | November 4, 2025 | Tuesday   | Finalized and reviewed **GWT ↔ React integration documentation**:<br>• Consolidated Activity–Place lifecycle with React render flow (`ReactInterop`, `PrescriptionActivityR`)<br>• Added **data-exchange section** (Java ↔ JS via JSInterop)<br>• Included **architecture and control-flow diagrams**<br>• Optimized **README structure** for clarity and onboarding              | [View →](Week25/Day170-README.md) |
| **Day 171** | November 5, 2025 | Wednesday |Continued addressing **CodeGuru** findings:<br>• Ensured UTF-8 encoding in **HttpConnectionUtils.java**, **ExportHTMLAsPdf.java**, and **EncounterServiceImpl.java**<br>• Verified secure digest handling in **MD5.java** (no change needed)<br>• Updated multiple encoding and directory handling issues in **RefillRequestServiceImpl.java** (14 completed, 10 pending)<br>• Standardized string comparison using `.equals()` in **OrdersServiceImpl.java**               | [View →](Week25/Day171-README.md) |
| **Day 172** | November 6, 2025 | Thursday  | Reviewed and fixed encoding and i18n issues:<br>• Verified **ObjectFactory.java** warnings as false positives (no code changes required)<br>• Reviewed **ImmunizationServiceImpl.java** – safe use of `Calendar.getInstance`, duplicate code noted<br>• Enforced UTF-8 in **PaymentTransactionExecutionServiceImpl.java** (`InputStreamReader`, `String.getBytes()`)<br>• Updated **PortalPaymentsServiceImpl.java** – UTF-8 Base64 encoding, fixed string comparison (`==` → `.equals()`)              | [View →](Week25/Day172-README.md) |
| **Day 173** | November 7, 2025 | Friday    |               | [View →](Week25/Day173-README.md) |
| **Day 174** | November 8, 2025 | Saturday  | 🌴 **Leave**  | –                                 |
| **Day 175** | November 9, 2025 | Sunday    | 🌴 **Leave**  | –                                 |

---

## 📅 Week 26 Summary
| 📆 Day      | Date              | Day       | 🚀 Highlights                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 📄 Link                           |
| ----------- | ----------------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| **Day 183** | November 17, 2025 | Monday    | **Amazon CodeGuru:** <br>🛠 Reviewed and updated multiple services: <br>• `portalRecoverUserPasswordServiceImpl.java` – verified `InputStreamReader`, `Calendar.getInstance`, `IOUtils.toString` usage <br>• `PlanSpecification.java` – fixed unsafe `String` comparison <br>• `SchedulerConfigServiceImpl.java` – corrected unsafe comparisons & cyclomatic complexity <br>• `CareQualityServiceImpl.java` – analyzed JSON handling, catch blocks, `String.getBytes`, etc. | [View →](Week27/Day183-README.md) |
| **Day 184** | November 18, 2025 | Tuesday   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | [View →](Week27/Day184-README.md) |
| **Day 185** | November 19, 2025 | Wednesday |                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | [View →](Week27/Day185-README.md) |
| **Day 186** | November 20, 2025 | Thursday  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | [View →](Week27/Day186-README.md) |
| **Day 187** | November 21, 2025 | Friday    | **mClinic Fax Referral Issue:** <br>📠 Fax transmission from Lancaster office sends only first **3 pages**. <br>🔍 Confirmed issue occurs inside mClinic fax workflow. <br>🔧 Logged as an internal fax-processing defect.                                                                                                                                                                                                                                                  | [View →](Week27/Day187-README.md) |
| **Day 188** | November 22, 2025 | Saturday  | 🌴 **Leave**                                                                                                                                                                                                                                                                                                                                                                                                                                                                | –                                 |
| **Day 189** | November 23, 2025 | Sunday    | 🌴 **Leave**                                                                                                                                                                                                                                                                                                                                                                                                                                                                | –                                 |


## 📅 Week 27 Summary

| 📆 Day      | Date              | Day       | 🚀 Highlights | 📄 Link |
| ----------- | ----------------- | --------- | ------------- | ------- |
| **Day 183** | November 17, 2025 | Monday    |  **Amazon code guru :** <br> 🛠 Reviewed and updated multiple services: <br>• `portalRecoverUserPasswordServiceImpl.java` – verified `InputStreamReader` usage, `Calendar.getInstance` checks, `IOUtils.toString` usage <br>• `PlanSpecification.java` – fixed unsafe `String` comparison (`== / !=`) <br>• `SchedulerConfigServiceImpl.java` – fixed unsafe `String` comparisons, validated cyclomatic complexity <br>• `CareQualityServiceImpl.java` – pending fixes in multiple `catch` blocks, some JSON handling lines, and `String.getBytes`; confirmed `SimpleDateFormat` and static config campaigns      | [View →](Week27/Day183-README.md) |
| **Day 184** | November 18, 2025 | Tuesday   |               | [View →](Week27/Day184-README.md) |
| **Day 185** | November 19, 2025 | Wednesday |               | [View →](Week27/Day185-README.md) |
| **Day 186** | November 20, 2025 | Thursday  |               | [View →](Week27/Day186-README.md) |
| **Day 187** | November 21, 2025 | Friday    |               | [View →](Week27/Day187-README.md) |
| **Day 188** | November 22, 2025 | Saturday  | 🌴 **Leave**  | – |
| **Day 189** | November 23, 2025 | Sunday    | 🌴 **Leave**  | – |


---
| 📆 Day      | Date              | Day       | 🚀 Highlights                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | 📄 Link                           |
| ----------- | ----------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------- |
| **Day 190** | November 24, 2025 | Monday    | **XML Template Not Loading – D2DesktopNew** <br>🔎 Investigated missing XML files under Tomcat context.<br>🛠 Identified mismatch between expected path and deployed version.                                                                                                                                                                                                                                                                                                                                                                          | [View →](Week28/Day190-README.md) |
| **Day 191** | November 25, 2025 | Tuesday   | **Major Discovery: D2DesktopNew Not Deployed on Tomcat** <br>🔥 `/D2DesktopNew` returning 404 because the Tomcat running on dev2 is **9.0.12**, not **9.0.19**. <br>📌 No `D2DesktopNew` webapp exists under `/usr/share/tomcat/apache-tomcat-9.0.12/webapps`. <br>📌 Real app is deployed under `/var/version/D2glacelegacy_stagenew`. <br>⚠ SSO returns `https://dev2.glaceemr.com:444/D2DesktopNew`, but Tomcat is actually serving `/D2DESKTOPNEW` (uppercase mismatch). <br>📄 Root cause: **context-path mismatch + missing deployment folder**. | [View →](Week28/Day191-README.md) |
| **Day 192** | November 26, 2025 | Wednesday |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | [View →](Week28/Day192-README.md) |
| **Day 193** | November 27, 2025 | Thursday  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | [View →](Week28/Day193-README.md) |
| **Day 194** | November 28, 2025 | Friday    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | [View →](Week28/Day194-README.md) |
| **Day 195** | November 29, 2025 | Saturday  | 🌴 **Leave**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | –                                 |
| **Day 196** | November 30, 2025 | Sunday    | 🌴 **Leave**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | –                                 |

---

