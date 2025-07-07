# 📄 Day 46 - Case: `bud fixed and AuditTrail_nextRelease`

**Date:** July 3, 2025 (Thursday)

---

## ✅ Summary of Changes & Fixes

### 1. **Bug Fix: Referring Doctor Fax Number Formatting**

* Resolved the inconsistent formatting of `referring_doctor_fax_number` field.
* Replaced `$$` delimiter with formatted 10-digit fax numbers.
* Applied `string_agg` and `unnest` to handle multiple fax entries cleanly.

### 2. **DB Schema Update**

```sql
ALTER TABLE referring_doctor
ALTER COLUMN referring_doctor_fax_number TYPE VARCHAR(100);
```

* Increased the length of the fax number column to accommodate multiple entries.

---

## 🔍 SQL Query Enhancements

### Previous Logic:

```sql
replace(coalesce(referring_doctor_fax_number,''), '$$', ' , ') as col2
```

### Updated and Improved Fax Formatting Logic:

```sql
(
  select string_agg(
    case 
      when length(fax) = 10 then 
        substring(fax,1,3)||'-'||substring(fax,4,3)||'-'||substring(fax,7,4)
      else fax 
    end, ' , '
  )
  from unnest(string_to_array(referring_doctor_fax_number, '$$')) as fax
) as col2
```

### Improved Query Snippet:

```sql
select_query.append(" coalesce(specialisation_referring_name,'') as col0,
case when referring_doctor_type = 1 then coalesce(referring_doctor_prefix,'')||' '|| 
coalesce(referring_doctor_firstname,'')||' '|| 
coalesce(referring_doctor_lastname,'')||' '|| 
coalesce(referring_doctor_credential,'') 
else COALESCE(practice_name, '') END as col1 ,

(
  select string_agg(
    case 
      when length(fax) = 10 then 
        substring(fax,1,3)||'-'||substring(fax,4,3)||'-'||substring(fax,7,4)
      else fax 
    end, ' , '
  )
  from unnest(string_to_array(referring_doctor_fax_number, '$$')) as fax
) as col2,

case when referring_doctor_phoneno <> '' then 
  coalesce(substring(referring_doctor_phoneno,0,4),'')||'-'||coalesce(substring(referring_doctor_phoneno,4,3),'')||'-'||coalesce(substring(referring_doctor_phoneno,7,4),'')  
else coalesce(referring_doctor_phoneno,'') end as col3,

referring_doctor_uniqueid as id,
coalesce(referring_doctor_address,'')||', '||coalesce(referring_doctor_city,'')||', '||coalesce(billing_config_table_lookup_desc,'')||', '||coalesce(referring_doctor_zip,'') as col4,
direct_email_address as col5,
referring_doctor_credential as col6,
referring_doctor_description as col7");
```

---

## 🌐 HTTP GET Endpoint Observed

* **URL**:
  `http://localhost:8080/GlaceMaster/jsp/chart/leafmodel/LeafModelFrame.Action`

* **Sample Query Parameters**:

  * `patientId`: 376638
  * `chartId`: 376973
  * `encounterId`: 152839
  * `DOS`: 05/16/2025 02:28:54 AM
  * `formId`: 1833
  * `leafId`: 57595
  * `isReportNeeded`: 1
  * `serviceDoctorId`: 1

---

## 🔐 Secure File Transfer (SFTP)

* **Server**: `ftp.glaceemr.com`
* **Port**: `8444`
* **User**: `glenwood`
* **Remote Directory**: `/Temp/Mounika`
* **Password**:

  ```
  Gl3nW00d@$3curE$FTP!
  ```

> 🛡️ *Ensure the password is rotated periodically and stored securely (e.g., in a secret manager).*

---

## 🧾 Notes

* Offsets and limits used in queries for pagination.
* Query ensures active (`referring_doctor_isactive is true`) doctors only.
* Consider indexing `referring_doctor_isactive`, `referring_doctor_speciality_id`, and `referring_doctor_state` for performance.

---
