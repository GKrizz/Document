# Referral Letter Generation Issue – "Referred By" Field

**🗓️ Date:** Day 23 – June 10, 2025 (Tuesday)

---

## 🔍 Issue Summary

**Feature:** Referral Letter Generation
**Issue:** Incorrect Display in "Referred By" Field

---

## 🔧 Description

When generating a referral letter using the DIRECT module in **GlacePremium**, the **"Referred By"** field is incorrectly showing the **employee's name** instead of the **referring doctor's name**.

---

## ✅ Expected Behavior

The **"Referred By"** field should display **only the referring doctor’s name**, as they are the originator of the referral.

---

## 🐞 Actual Behavior

The system is displaying the **employee’s name** (possibly the user generating the referral) in place of the referring **doctor’s name**.

---

## 📌 Suggested Fix

Ensure the system correctly maps and displays the **doctor associated with the referral** in the **"Referred By"** field, rather than the logged-in user or employee.

---

# Backend Code Trace & Fix Guide (Eclipse)

Thanks for sharing the request trace — very helpful! Below is a step-by-step guide for tracing and modifying the backend Java code in Eclipse, so the **"Referred By"** field pulls the **encounter owner (doctor)** instead of the logged-in employee/staff.

---

## 🔍 Goal

**Modify backend logic** so that the "Referred By" field pulls the **encounter owner (doctor)**, not the logged-in user (staff).

---

## 🧭 Step-by-Step Guide for Tracing Code in Eclipse

### Step 1: Identify the Trigger Point

* The endpoint of interest is likely:

  ```
  /glaceemr_backend/api/desktop/user/Plan/updateReferrals
  ```

* This method updates/saves the referral, so start by locating this method.

### Step 2: Locate the Java Class for This Endpoint

* In Eclipse:

  1. Press `Ctrl + Shift + R` to open the **file search**.
  2. Search for `updateReferrals`.
  3. Find the Java file containing the method, often in a controller class like `PlanController.java`.

---

## Your Current Implementation Problem

You're currently retrieving the logged-in user's name to set the **"Referred By"** field:

```java
String empLoginId = TennantContextHolder.getRequestcontext().getLoginId();
EmployeeProfile getUserName = employeeService.findByUserNameIgnoreCase(empLoginId);
referralObj.setreferral_details_rdoctor_from(getUserName.getEmpProfileFullname());
```

This results in the employee’s name showing instead of the referring doctor’s.

---

## ✅ Corrected `updateReferrals` Method Snippet


```java
public String updateReferrals(Integer encounterId, Integer patientId, String empName, Integer empId,
		Integer chartId, String reviewedDate, String dxCode,String refId) throws Exception {

	ReferralDetails referralObj = new ReferralDetails();
	// 🔧 CHANGED: Removed default assignment of empLoginId from session
	String empLoginId = "-1"; // placeholder to override if needed

	// ✅ ADDED: Get Encounter and retrieve the assigned doctor (owner)
	Encounter encData = encounterRepository.findbyEncounterId(encounterId);
	Integer serviceDoctorID = encData.getEncounterServiceDoctor() != null ? encData.getEncounterServiceDoctor().intValue() : -1;

	if (serviceDoctorID != -1) {
		// ✅ ADDED: Get doctor profile from service doctor ID
		EmployeeDataBean empProfile = employeeService.findEmployeeById(serviceDoctorID);
		empId = empProfile.getEmpId();
		empLoginId = empProfile.getEmpLoginId() != null ? empProfile.getEmpLoginId().toString() : "-1";

		// ✅ ADDED: Set the doctor's full name as the "Referred By"
		EmployeeProfile getUserName = employeeService.findByUserNameIgnoreCase(empLoginId);
		referralObj.setreferral_details_rdoctor_from(getUserName.getEmpProfileFullname()); // This sets "Referred By"
	}

	// 🔧 REMOVED: No longer using staff loginId to set "Referred By"
	/*
	if (empLoginId != null && !empLoginId.equalsIgnoreCase("-1")) {
		EmployeeProfile getUserName = employeeService.findByUserNameIgnoreCase(empLoginId);
		referralObj.setreferral_details_rdoctor_from(getUserName.getEmpProfileFullname());
	}
	*/

	referralObj.setreferral_details_chartid(chartId);
	referralObj.setreferral_details_encounterid(encounterId);
	referralObj.setreferral_details_ord_on(HUtil.getCurrTimeStamp());
	referralObj.setreferral_details_rdoctor_to(empName);

	JSONObject rdcotorDetails = new JSONObject();
	rdcotorDetails = commonPickerService.getReferralData(refId,"", "", "", "", "", "", "-1","","","", "", "-1", "", "", "", "0", "2",false);
	JSONArray details = (JSONArray) rdcotorDetails.get("Data");

	if (details != null && details.length() > 0) {
		JSONObject data = (JSONObject) details.get(0);

		referralObj.setreferral_details_rdoctor_spec(data.get("col0") != null ? data.get("col0").toString() : "");
		referralObj.setreferral_details_rdoctor_address(data.get("address") != null ? data.get("address").toString().replace(",", "") : "");
		referralObj.setreferral_details_rdoctor_faxno(data.get("col4") != null ? data.get("col4").toString() : "");

		String r_docPhone = data.get("col5").toString();
		String othDocPhno = "";
		if (r_docPhone.contains("$$")) {
			String[] othDocPh = r_docPhone.split("\\$\\$");
			for (int k = 0; k < othDocPh.length; k++) {
				othDocPhno += othDocPh[k] + ",";
			}
			if (othDocPhno.endsWith(","))
				othDocPhno = othDocPhno.substring(0, othDocPhno.length() - 1);
			if (othDocPhno.startsWith(","))
				othDocPhno = r_docPhone.substring(2, othDocPhno.length() + 1);
			r_docPhone = othDocPhno;
		}
		referralObj.setreferral_details_rdoctor_phno(r_docPhone);
		referralObj.setReferralReferredtoid(Integer.parseInt(data.get("id").toString()));
	}

	referralObj.setReferralOrderOn(HUtil.getCurrTimeStamp());
	referralObj.setreferral_details_isactive(-1);
	referralObj.setreferral_details_patientid(patientId);

	// ✅ OPTIONAL: You could set this to the doctor's loginId instead of staff
	referralObj.setReferreddoctorLoginid(serviceDoctorID); // 🔧 CHANGED to use doctor's ID

	referralObj.setreferral_details_status(1);
	referralObj.setReferralOrderBy(empId); // 🔧 CHANGED to use doctor’s ID
	referralObj.setReferralById(empId);     // 🔧 CHANGED to use doctor’s ID
	referralObj.setReferralReviewedOn(HUtil.getCurrTimeStamp());

	if (!dxCode.equals("-1"))
		referralObj.setreferral_details_dxcode(dxCode);

	referralDetailsRepository.saveAndFlush(referralObj);
	return "success";
}
```

---



## ✅ Summary of Changes

| Change                                    | Description                                            |
| ----------------------------------------- | ------------------------------------------------------ |
| Retrieve `serviceDoctorID` from Encounter | Get the actual doctor assigned to the encounter.       |
| Fetch doctor profile by ID                | Access the doctor’s full profile data.                 |
| Set `"Referred By"` to doctor’s name      | Use doctor’s full name instead of logged-in user.      |
| Remove usage of logged-in employee ID     | Avoid setting referral owner as logged-in staff.       |
| Set referral author IDs to doctor         | Ensure referral is attributed correctly to the doctor. |

---

