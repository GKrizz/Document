
# 📝 Referring Doctor Info Not Auto-Populating While Faxing

<img width="668" height="349" alt="image" src="https://github.com/user-attachments/assets/3e699770-18d9-45b7-8f3e-a0304707a560" />


### 📌 Case Details

| **Field**            | **Value**                                                                                                                                      |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Case#**            | 234691                                                                                                                                         |
| **Assigned To**      | Gobala Krishnan                                                                                                                                |
| **Case Owner**       | Padmanaban Guruswamy (Paddy)                                                                                                                   |
| **Account**          | Advanced Allergy & Asthma Care, LLC (Acct ID: 039323)                                                                                          |
| **Reported On**      | 28-July-2025                                                                                                                                   |
| **Module**           | Chart → Fax (Lab Orders)                                                                                                                       |
| **Product**          | GlacePremium                                                                                                                                   |
| **Status**           | Open                                                                                                                                           |
| **Priority / Type**  | Medium / Bug                                                                                                                                   |
| **Responsible Team** | Glace Billing Team                                                                                                                             |
| **Estimated Time**   | 2 hours                                                                                                                                        |
| **ETA**              | 30-July-2025                                                                                                                                   |
| **Issue Summary**    | Referring doctor information not auto-populating when faxing lab orders. Previously it was auto-filled, but now shows blank in the fax screen. |


---

# 🧾 **Case Summary**

* **Client**: Advanced Allergy & Asthma Care, LLC (Acct 039323)
* **Module**: **Chart > Faxing**
* **Issue**: When faxing **lab reports**, the **Referring Doctor info is not auto-populating**. It was working previously.
* **Assigned to**: Gobala Krishnan
* **Priority**: Medium | **Bug**
* **ETA**: 2 hours (by **7/30/2025**)

---


### 🧪 Reproduction Steps

1. Navigate to a patient chart with lab orders.
2. Attempt to fax labs using the **Fax Icon** from the labs screen.
3. Observe the **"Referring Doctor" field**:
   It is **blank**, requiring manual selection via picker (which was not the case earlier).

---

### 🔍 Root Cause

The referring doctor info was **not being passed** as part of the URL parameters to the faxing module (`FaxLeafInformation.Action`).
Specifically, `refdocName` and `reffaxNo` were either **blank** or had placeholder values like `-1`.

---

### ✅ Resolution

The fix involved **modifying the URL generation logic** to ensure all relevant referring doctor fields are properly passed to the `FaxLeafInformation.Action` request.

#### ✅ Code Fix Summary

```java
// Fixed: Appending all necessary params to request for faxing
request.setAttribute("pageHeader", 
  "FaxLeafInformation.Action?xForm=" + fileName + "&" + header +
  "&faxId=" + faxId +
  "&reffaxNo=" + reffaxNo +
  "&refdocName=" + refdocName +
  "&pharmid=" + pharmid +
  "&faxNo=" + faxNo +
  "&isAttachment=" + isAttachment +
  "&attachment=" + attachment +
  "&patientId=" + patientId +
  "&patientFileAttach=" + patientFileAttach +
  "&ids=" + ids +
  "&isPresc=" + isPresc +
  "&scanCategory=" + scanCategory +
  "&CCGroupIds=" + CCGroupIds +
  "&toList=" + URLEncoder.encode(toList, "UTF-8") +
  "&fromPlan=" + fromPlan +
  "&faxMode=" + faxNewMode
);
```

#### 🔧 Helper Method Used

```java
private String encode(String input) throws UnsupportedEncodingException {
    return URLEncoder.encode(HUtil.Nz(input, ""), "UTF-8");
}
```

---

### 🧪 Post-Fix Behavior (Tested)

* Referring doctor’s **name** and **fax number** now **auto-populate** on the fax screen.
* Manual picker is no longer required unless overridden.

---

### 🔁 Regression Tested On:

* Faxing from **Labs and Tests**
* Faxing from **SOAP/Progress Notes**
* Different scenarios (with and without referring doctor present)

---

### 🧾 Notes

* Case was reported based on screenshot and manual verification.
* Account: **Advanced Allergy & Asthma Care, LLC**
* Chart ID used during testing: `377063`
* Encounter ID: `153271`
* Patient ID: `376714`

---

### Before
<img width="1293" height="740" alt="image" src="https://github.com/user-attachments/assets/42bf66e8-d63a-474b-86a5-3d009c726482" />


### After

<img width="1294" height="703" alt="image" src="https://github.com/user-attachments/assets/76a866f4-8407-4e7f-81d9-e8ae444b8701" />


---

### ✅ Status: FIXED & VERIFIED

✅ Referring doctor details are now auto-populating correctly during faxing.
📅 Fix committed and verified on: **30-July-2025**

---
