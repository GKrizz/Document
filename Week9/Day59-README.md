
## 🔍 Summary of What This Code Is Doing

### ✅ This logic runs when **adding a new referral** from the **Legacy View** Plan screen.
### file name : PlanElements.java 
### ❗️Where the bug is happening:

```java
rdoctor_From = dbUtils.newTableLookUp(
    "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
    "emp_profile",
    "emp_profile_loginid=" + currentUserId
);
```

### 🔴 This sets `rdoctor_From` to:

> **The currently logged-in user (staff)**
> ❌ NOT the **Service Doctor** from the encounter.

---

## 🛠️ What Should Happen

* `rdoctor_From` should be the **Service Doctor name** (who owns the encounter).
* But you’re using the **logged-in user** (`currentUserId`) — which may be **a staff user** filling the referral form, not the actual physician.

---

## ✅ How to Fix

### 🔄 Replace this line:

```java
rdoctor_From = dbUtils.newTableLookUp(
    "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
    "emp_profile",
    "emp_profile_loginid=" + currentUserId
);
```

### ✅ With this:

```java
String serviceDoctorId = dbUtils.newTableLookUp(
    "encounter_service_doctor",
    "encounter",
    "encounter_id=" + encounterId
);

rdoctor_From = dbUtils.newTableLookUp(
    "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
    "emp_profile",
    "emp_profile_empid=" + serviceDoctorId
);

referreddoctor_id = serviceDoctorId; // Also update this to store correct referring doctor
```

---

## ✅ Also Update the `referreddoctor_id`

```java
referreddoctor_id = serviceDoctorId;
```

Instead of:

```java
referreddoctor_id = dbUtils.newTableLookUp("emp_profile_empid","emp_profile","emp_profile_loginid="+currentUserId);
```

Because:

* `referreddoctor_id` should reflect the **referring physician’s internal ID**, not the staff user.

---

## 🔁 Then, the rest of your insert logic will work as expected:

* `rdoctor_From` → Service doctor name
* `rdoctor_To` → Specialist being referred to
* Database will store it correctly
* Legacy and modern view will show consistent values

---

## ✅ Final Result

| Field               | Value Source             | Before       | After               |
| ------------------- | ------------------------ | ------------ | ------------------- |
| `rdoctor_From`      | Referring physician name | ❌ Staff user | ✅ Service Doctor    |
| `referreddoctor_id` | Referring physician ID   | ❌ Staff ID   | ✅ Service Doctor ID |

---


## ✅ Add Debug Statements

Here's your updated block with debug prints added:

```java
else if(planRequestMode == PlanConstants.ADD_REFERRAL){
    System.out.println(">>> [DEBUG] Inside ADD_REFERRAL block");

    String r_docSpeciality="", r_docAddress="", r_docPhone="", r_docFax="", getReferralQry="", getPatientReferral="", rdoctor_To="", rdoctor_From="", lastName="", firstName="", referreddoctor_id="", referral_referredtoid="";
    
    int refPhyId = Integer.parseInt(HUtil.Nz(request.getParameter("refPhyId"), "-1"));
    String dx = HUtil.Nz(request.getParameter("dx"), "");

    System.out.println(">>> [DEBUG] refPhyId: " + refPhyId);

    if(refPhyId == -1){
        lastName = HUtil.Nz(request.getParameter("lName"), "");
        firstName = HUtil.Nz(request.getParameter("fName"), "");
    } else {
        lastName = dbUtils.newTableLookUp("referring_doctor_lastname", "referring_doctor", "referring_doctor_uniqueid=" + refPhyId);
        firstName = dbUtils.newTableLookUp("referring_doctor_firstname", "referring_doctor", "referring_doctor_uniqueid=" + refPhyId);
    }

    rdoctor_To = firstName + " " + lastName;
    System.out.println(">>> [DEBUG] Referred To: " + rdoctor_To);

    rdoctor_From = dbUtils.newTableLookUp(
        "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
        "emp_profile",
        "emp_profile_loginid=" + currentUserId
    );
    referreddoctor_id = dbUtils.newTableLookUp("emp_profile_empid", "emp_profile", "emp_profile_loginid=" + currentUserId);
    System.out.println(">>> [DEBUG] Referring Physician (From): " + rdoctor_From);
    System.out.println(">>> [DEBUG] Referring Physician ID: " + referreddoctor_id);

    if(refPhyId == -1) {
        referral_referredtoid = dbUtils.newTableLookUp("referring_doctor_uniqueid", "referring_doctor", "referring_doctor_lastname like '" + lastName + "' and referring_doctor_firstname like '" + firstName + "'");
    } else {
        referral_referredtoid = refPhyId + "";
    }
    System.out.println(">>> [DEBUG] Referral ReferredToId: " + referral_referredtoid);

    getPatientReferral = PlanQueryFactory.getPatientReferralQry(encounterId, patientId, rdoctor_From, referreddoctor_id, referral_referredtoid);
    System.out.println(">>> [DEBUG] Referral check query: " + getPatientReferral);

    JSONArray PatientReferralDetails = dbUtils.executeQueryToJSONArray(getPatientReferral, true);

    try {
        lock.lock();
        String referralId = "-1";
        if(PatientReferralDetails.length() == 0){
            String rdcotorDetailsQry = "";
            if(refPhyId == -1)
                rdcotorDetailsQry = PlanQueryFactory.getReferringDrDetailsQry(lastName, firstName);
            else
                rdcotorDetailsQry = PlanQueryFactory.getReferringDrDetailsQry(referral_referredtoid);						

            System.out.println(">>> [DEBUG] Referring doctor details query: " + rdcotorDetailsQry);

            JSONArray rdcotorDetails = dbUtils.executeQueryToJSONArray(rdcotorDetailsQry, true);

            JSONObject rDetails = new JSONObject();
            if(rdcotorDetails.length() > 0){
                rDetails = rdcotorDetails.getJSONObject(0); 
                r_docSpeciality = rDetails.getString("speciality");
                r_docAddress = rDetails.getString("address");
                r_docPhone = rDetails.getString("phoneno");
                r_docFax = rDetails.getString("fax");

                System.out.println(">>> [DEBUG] Referring doctor specialty: " + r_docSpeciality);
                System.out.println(">>> [DEBUG] Referring doctor phone: " + r_docPhone);
                System.out.println(">>> [DEBUG] Referring doctor fax: " + r_docFax);

                String othDocPhno = "";
                if(r_docPhone.contains("$$")){
                    String[] othDocPh = r_docPhone.split("\\$\\$");
                    for(int k = 0; k < othDocPh.length; k++){  
                        othDocPhno += othDocPh[k] + ",";
                    }
                    if (othDocPhno.endsWith(",")) 
                        othDocPhno = othDocPhno.substring(0, othDocPhno.length() - 1);
                    if(othDocPhno.startsWith(","))
                        othDocPhno = r_docPhone.substring(2, othDocPhno.length() + 1);
                    r_docPhone = othDocPhno;
                }

                String referralQry = PlanQueryFactory.insertNewReferralQuery(
                    templateId, encounterId, patientId, chartId, rdoctor_From, rdoctor_To,
                    r_docSpeciality, r_docAddress, r_docPhone, r_docFax,
                    currentUserId, referreddoctor_id, referral_referredtoid, dx
                );

                System.out.println(">>> [DEBUG] Insert Referral Query: " + referralQry);

                try {
                    referralId = dbUtils.executeAndGetGeneretedKey(referralQry, "referral_details_refid") + "";
                    System.out.println(">>> [DEBUG] New Referral ID: " + referralId);
                } catch(Exception e){
                    System.out.println(">>> [ERROR] Failed to insert referral: " + e.getMessage());
                    referralId = "-1";
                }
            }
        }

        JSONObject res = new JSONObject();
        res.put("newRefId", referralId);
        PlanJSONString = res.toString();
        request.setAttribute("JSONString", PlanJSONString);

        System.out.println(">>> [DEBUG] Final Response JSON: " + PlanJSONString);

        lock.unlock();
    } catch(Exception e){
        lock.unlock();
        System.out.println(">>> [ERROR] Exception inside ADD_REFERRAL block: " + e.getMessage());
    }
}
```

---

## ✅ What to Check in Console

You should now see lines like:

```
>>> [DEBUG] Inside ADD_REFERRAL block
>>> [DEBUG] Referring Physician (From): Dr. John Smith
>>> [DEBUG] Referred To: Mahesh Bajaj
>>> [DEBUG] Referral check query: SELECT ...
>>> [DEBUG] Insert Referral Query: INSERT INTO referral_details ...
>>> [DEBUG] New Referral ID: 2228
```

---

## 🔚 Next Step


* If the referring physician is still **staff instead of doctor**, we’ll replace the `currentUserId` logic with the **service doctor ID** (as explained earlier).

---

## 🧩 **Problem Recap**

In the **legacy view**, under **"Referred By"**, you're seeing:

* ❌ The **staff name** (e.g., *test1 Test*)
* ✅ But you expect the **Service Doctor’s name** (e.g., *Dr. John Smith*)

---

## 🔍 **Root Cause**

In your original code block (for `planRequestMode == 62` / `ADD_REFERRAL`), the name under `referral_details_rdoctor_from` (i.e., "Referred By") was set using:

```java
rdoctor_From = dbUtils.newTableLookUp(
    "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
    "emp_profile",
    "emp_profile_loginid=" + currentUserId
);
referreddoctor_id = dbUtils.newTableLookUp("emp_profile_empid", "emp_profile", "emp_profile_loginid=" + currentUserId);

```

### ❗Problem with this:

* `currentUserId` is the **logged-in user** (a staff member, scribe, or nurse).
* But the real **referring physician** should be the **Service Doctor** assigned to the encounter.

---

## 🛠️ **What We Changed**

### ✅ **Step 1: Get the correct referring doctor (Service Doctor) ID**

We fetched it from the `encounter` table:

```java
String serviceDoctorId = dbUtils.newTableLookUp(
    "encounter_service_doctor",
    "encounter",
    "encounter_id=" + encounterId
);
```

### ✅ **Step 2: Get the Service Doctor's full name using `empid`**

```java
rdoctor_From = dbUtils.newTableLookUp(
    "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
    "emp_profile",
    "emp_profile_empid=" + serviceDoctorId
);
```

> This ensures the name is pulled from the **physician**, not the **staff**.

### ✅ **Step 3: Update `referreddoctor_id`**

```java
referreddoctor_id = serviceDoctorId;
```

> So the ID stored in `referral_by_id` is also the **service doctor**, not the staff.

---

## 📊 **Before vs After**

| Field                           | **Before (Incorrect)**    | **After (Correct)**           |
| ------------------------------- | ------------------------- | ----------------------------- |
| `referral_details_rdoctor_from` | `test1 Test` (Staff name) | `Dr. John Smith` (Service Dr) |
| `referral_by_id`                | `1` (Staff ID)            | `10` (Service Doctor ID)      |

---

## ✅ **Result**

Now, in the **legacy UI** or any downstream system:

* The **Referred By** field will show the **actual service doctor**'s name.
* Not the name of the staff user who submitted the referral.

---

