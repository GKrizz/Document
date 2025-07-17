# 🩺 **Referral Module Fix – Correcting Referring Doctor (Legacy View)**

## 📌 Overview

This update fixes a **critical bug** in the **Legacy View Plan screen** where the **referring doctor** shown/stored was incorrectly set to the **currently logged-in staff user**, instead of the **service doctor** who owns the encounter.

---

## 🚨 Problem Summary

| Field               | Incorrect (Before)                 | Correct (After)                                   |
| ------------------- | ---------------------------------- | ------------------------------------------------- |
| `rdoctor_From`      | Logged-in staff (e.g., test1 Test) | Encounter’s service doctor (e.g., Dr. John Smith) |
| `referreddoctor_id` | Staff user’s ID                    | Service doctor’s ID                               |

---

## ✅ Fix Summary

### 🎯 Objective:

Ensure that **referrals are attributed to the actual service doctor** of the encounter, not the staff member creating the referral.

---

## 🛠️ Files & Changes

---

### 1. **`PlanElements.java`**

#### 🔄 **Updated Logic (Inside `ADD_REFERRAL` block):**

**❌ Old (Buggy) Code:**

```java
rdoctor_From = dbUtils.newTableLookUp(..., "emp_profile", "emp_profile_loginid=" + currentUserId);
referreddoctor_id = dbUtils.newTableLookUp(..., "emp_profile", "emp_profile_loginid=" + currentUserId);
```

**✅ New (Correct) Code:**

```java
String serviceDoctorId = dbUtils.newTableLookUp("encounter_service_doctor", "encounter", "encounter_id=" + encounterId);

rdoctor_From = dbUtils.newTableLookUp(
    "coalesce(emp_profile_lname,'')||' '||coalesce(emp_profile_fname,'')",
    "emp_profile",
    "emp_profile_empid=" + serviceDoctorId
);
referreddoctor_id = serviceDoctorId;
```

#### 📝 Additional Enhancements:

* Added **debug print statements** to help trace values like:

  * `referring physician name`
  * `service doctor ID`
  * `generated referral query`
  * `final JSON output`

---

### 2. **`ReferralModel.java`**

#### 🖨️ **Enhanced Fax Number Formatting (SQL-Level)**

Replaces plain unformatted fax output with conditional formatting:

```sql
SELECT string_agg(
    CASE 
        WHEN length(fax) = 10 AND fax ~ '^[0-9]+$' 
        THEN '(' || substring(fax,1,3) || ') ' || substring(fax,4,3) || '-' || substring(fax,7,4) 
        ELSE fax 
    END, ' , '
)
FROM unnest(string_to_array(referral_details_rdoctor_faxno, '$$')) AS fax
```

---

### 3. **`ReferralSection.jsp`**

#### 🧼 Cleaned up Fax Display

**❌ Old JSP (over-formatting, potential double-formatting):**

```jsp
<%= HUtil.FormatPhoneNumber(rModel.getFaxNumber()) %>
```

**✅ New JSP:**

```jsp
<%= rModel.getFaxNumber() %>
```

OR

```jsp
<%= HUtil.Nz(r_docfax, "") %>
```

---

### 4. **`ReferralDetails.jsp`**

#### 📞 Simplified Phone/Fax Formatting

**Old:**

```jsp
<Td><%=HUtil.FormatPhoneNumber(HUtil.Nz(r_docfax,""),session)%></Td>
```

**Updated To:**

```jsp
<Td><%= r_docfax %></Td>
```

---

## 🔍 Debug Output Example (Post-Fix)

When running the referral insert, your console should now show:

```
>>> [DEBUG] Inside ADD_REFERRAL block
>>> [DEBUG] Referring Physician (From): Dr. John Smith
>>> [DEBUG] Referring Physician ID: 101
>>> [DEBUG] Referred To: Mahesh Bajaj
>>> [DEBUG] Referral check query: SELECT ...
>>> [DEBUG] Insert Referral Query: INSERT INTO referral_details ...
>>> [DEBUG] New Referral ID: 2228
>>> [DEBUG] Final Response JSON: {"newRefId":"2228"}
```

---

## ✅ Outcome

| Feature                      | Before (Bug)                | After (Fix)                   |
| ---------------------------- | --------------------------- | ----------------------------- |
| Referring doctor name        | Staff user                  | Actual service doctor         |
| Referral `referreddoctor_id` | Staff user’s ID             | Encounter doctor’s ID         |
| Display in UI                | Incorrect                   | Accurate                      |
| Fax Number Display           | Not formatted or duplicated | Single clean formatted output |

---

## 📦 Files Touched

* `PlanElements.java`
* `ReferralModel.java`
* `ReferralSection.jsp`
* `ReferralDetails.jsp`

---

## 📚 Notes

* `currentUserId` should **never be used** to infer the referring doctor unless staff actually owns the encounter.
* `encounter.encounter_service_doctor` is now the **source of truth** for referral attribution.
* This change applies only to **Legacy View Plan Screen** (`planRequestMode == 62 / ADD_REFERRAL`).

---

## ✍️ Author / Developer Notes

* Always verify who owns the medical data interaction before saving anything to `referral_details`.
* When displaying identifiers like `rdoctor_From` or `r_docFax`, formatting should be clean, consistent, and delegated to backend **wherever possible**.

