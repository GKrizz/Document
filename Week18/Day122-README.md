## **CMS137v13 – Initiation and Engagement of Substance Use Disorder Treatment**

#### **Summary of Changes: 2024 → 2025**

| Change Type | Item                                                                              | 2024          | 2025         |
| ----------- | --------------------------------------------------------------------------------- | ------------- | ------------ |
| **Removed** | `Initial Hospital Observation Care`<br>(2.16.840.1.113883.3.464.1003.101.12.1002) | ✅ Present     | ❌ Removed    |
| **Removed** | `Online Assessments`<br>(2.16.840.1.113883.3.464.1003.101.12.1089)                | ✅ Present     | ❌ Removed    |
| **Added**   | `Virtual Encounter`<br>(2.16.840.1.113883.3.464.1003.101.12.1089)                 | ❌ Not Present | ✅ Added      |
| **Renamed** | `Payer` → `Payer Type`                                                            | `Payer`       | `Payer Type` |

---

### **Code Definitions**

**2024:**

* `valueset "Initial Hospital Observation Care"` (OID: `2.16.840.1.113883.3.464.1003.101.12.1002`)
* `Encounter, Performed: Online Assessments` using `"Online Assessments"` (OID: `2.16.840.1.113883.3.464.1003.101.12.1089`)
* `Patient Characteristic Payer: Payer` using `"Payer"` (OID: `2.16.840.1.114222.4.11.3591`)

**2025:**

* `valueset "Virtual Encounter"` (OID: `2.16.840.1.113883.3.464.1003.101.12.1089`)
* `Patient Characteristic Payer: Payer Type` using `"Payer Type"` (OID: `2.16.840.1.114222.4.11.3591`)

---

### 🔎 Notes

* ✅ The **same value set OID** `2.16.840.1.113883.3.464.1003.101.12.1089` was used in both years:

  * **2024 Label:** `"Online Assessments"`
  * **2025 Label:** `"Virtual Encounter"`
  * ➤ This is **not a code change**, but a **label/alias change**.
  * 📌 This may affect interpretation or logic **if the label influences documentation or system mapping**.

---
