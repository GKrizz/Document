# Clinical Data Debugging – README

## 📌 Purpose

This document explains how clinical data (QDM, Lab Tests, Assessments, Procedures, etc.) is fetched, processed, and debugged in the legacy code. It also shows where debug logs have been added to trace execution and database queries.

---

## 🔄 Flow Overview

1. **Date Range Logging**

   ```java
   System.out.println("Date range: " + date1 + " to " + date2);
   ```

   Ensures queries are filtered for the correct measurement period.

2. **Clinical Data Fetching**

   ```java
   List<ClinicalDataQDM> clinicalDataQDM = qdmData.getClinicalDataQDM(
       em, considerProvider, providerId, patientID,
       snomedCodesForCNM, loincCodesForCNM, true,
       date1, date2, requestObj
   );
   System.out.println("[DEBUG] ClinicalDataQDM list size: " + 
       (clinicalDataQDM != null ? clinicalDataQDM.size() : 0));
   ```

3. **Diagnostic Study**

   ```java
   System.out.println("_______Diagnostic Study_______");
   System.out.println("Diagnostic Study code list: " + codeList);
   System.out.println("Number of Diagnostic Study entries fetched from CNM: " + diagnosticStudy.size());
   ```

4. **Laboratory Test**

   ```java
   System.out.println("_______ Laboratory Test________");
   System.out.println("Number of LabTest QDM entries: " + labTests.size());
   ```

5. **Assessment**

   ```java
   System.out.println("_______ Assessment __________");
   System.out.println("Number of Assessment entries fetched from CNM: " + 
       (riskAssessment != null ? riskAssessment.size() : 0));
   ```

6. **Procedures**

   ```java
   System.out.println("_______ Procedures __________");
   System.out.println("Procedure CodeList: " + codeList);
   System.out.println("HCPCS Codes: " + hcpcsCodes);
   System.out.println("CPT Codes: " + cptCodes);
   System.out.println("Number of ProcBasedOnCPT entries: " + procBasedOnCPT.size());
   System.out.println("Number of HCPCS Procedures: " + hcpcsProcedures.size());
   System.out.println("Number of Procedures from CNM: " + procFromCNM.size());
   ```

   * Combines **Procedure**, **Allergy/Intolerance,Procedure**, **CPT**, **HCPCS**, and **CNM** sources.

---

## 🛠 Debugging Checklist

* ✅ Check if `clinicalDataQDM` is empty → issue with query or date range.
* ✅ Verify **code lists** (SNOMED, LOINC, CPT, HCPCS) are populated.
* ✅ Compare fetched entries across **CNM**, **Lab Tests**, **Diagnostic Study**, **Assessment**, and **Procedures**.
* ✅ If counts = 0, ensure the **database mapping** and **QDM categories** are correct.

---

## 🔍 Example Debug Output

```
Date range: 2024-01-01 to 2024-12-31
[DEBUG] ClinicalDataQDM list size: 0
_______Diagnostic Study_______
Diagnostic Study code list: 60515-4,72531-7,79069-1,79071-7,79101-2,82688-3
Number of Diagnostic Study entries fetched from CNM: 0
_______ Laboratory Test________
Number of LabTest QDM entries: 1
_______ Assessment __________
Number of Assessment entries fetched from CNM: 0
_______ Procedures __________
Procedure CodeList: 12345,67890
HCPCS Codes: [G0101,G0121]
CPT Codes: [45378,45380]
Number of ProcBasedOnCPT entries: 2
Number of HCPCS Procedures: 1
Number of Procedures from CNM: 3
```


