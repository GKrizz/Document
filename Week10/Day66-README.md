## ✅ Interoperability Error Documentation

**Channel**: `GlenwoodSystems_CT_IB_CCDA_ADT`
**System**: MirthConnect (AMC-CCDA-CONNIE)
**Date Resolved**: July 15, 2025
**Reported By**: CRISP Interface Support

---

### 📌 Issue Summary

At least **15 errors** were identified on the channel `GlenwoodSystems_CT_IB_CCDA_ADT`, which processes CCDA documents and creates ADT messages.

#### Breakdown of Errors:

* **3 errors** due to **unmapped OID**: `2.16.840.1.113883.3.225.10.10451` (CONTEMPORARY CARE account)
* **12 errors** caused by **invalid/special characters** in CCDA documents:

  * 8 errors for one patient
  * 2 errors for a second patient
  * 2 errors for a third patient

---

### 🔍 Root Cause

1. **Unmapped OID**: The OID was not mapped at the time of message transmission, resulting in failure before interface processing.
2. **Special Characters in CDA**: CCDA payloads included unsupported or malformed characters that caused parsing failures on the receiving interface.

---

### 🧾 Reference Patients

(Note: Omit PHI details in public README; below is for internal-use or secure storage.)

* **LEAH CARMEN ACOSTA** – Acct#: 018060
* **MATTHEW NIRO** – Acct#: 9340
* **CHASE GRIGEREK** – Acct#: 018838

---

## 🛠 Resolution Steps

### ✅ Step 1: Retrieve CDA Files via SFTP

The problematic CDA files were accessed and downloaded locally for inspection.

#### **Connection Info**

* **Remote Directory:** `/Temp/Gobal`
* **Local Directory:** `/home/software/Documents/maureen_CDA_files`
* **SFTP Tool:** labs3 connection

#### **SFTP Session Log**

```bash
# Change to remote directory
sftp> cd /Temp/Gobal

# List remote files
sftp> ls -lth

# Check current remote directory
sftp> pwd
Remote working directory: /Temp/Gobal

# Check current local shell directory
sftp> !pwd
/home/software/Downloads/tomcat9/bin

# Change local directory
sftp> lcd '/home/software/Documents/maureen_CDA_files'

# Confirm local directory
sftp> !pwd
/home/software/Documents/maureen_CDA_files
```

#### **File Transfers**

```bash
# Download 1st CDA file
sftp> get OUT-07-22-2025-04-55-46-293-432.cda
# Download successful

# List files again
sftp> ls -lrth

# Download 2nd CDA file
sftp> get OUT-07-22-2025-06-10-21-699-432.cda
# Download successful
```

#### **Session Ended Unexpectedly**

```bash
client_loop: send disconnect: Broken pipe
Connection closed.
```

*⚠️ Both files were successfully downloaded before disconnection. No retransmission required.*

---

### ✅ Step 2: Analyze Medication Section via API Trace

To identify why **invalid characters** were present in the CDA, I reviewed the backend code responsible for generating the **Medication Section** of the CCDA.

---

### 🔍 Code Path Investigated

**Primary File:**
`MedicationSectionHandler.java`
**Method:**

```java
public POCDMT000040Component3 getSectionComponent(HandlerConfig config)
```

#### ✅ Core Logic Summary:

* Retrieves **active medications** using `MedicationDAO.getActiveMedications(...)`
* Depending on `EncounterId` or `FromDate/ToDate`, it builds the medication list.
* Forms CDA `Section` using:

  * `Narrative Block`: `MedicationUtil.getNarrativeBlock(...)`
  * `Coded Block`: `MedicationUtil.getCodedBlock(...)`

#### 📍 Null Flavour Fallback

If no medications are found:

```java
return getNullFlavourSection(config);
```

---

### 🔍 Data Source Traced

**DAO Layer:**
`MedicationDAO.java → getActiveMedications(...)`

* Internally calls:
  `PrescriptionModel.activePrescAndNursePrescActiveQry(...)`

* Output: `JSONArray` of prescriptions from **two sources**:

  * `current_medication` (nurse)
  * `doc_presc` (doctor)

---

### 🔍 SQL Debugging (System.out Log)

You printed out the full dynamic SQL using `System.out.println(qry.toString());` to capture the live query being executed, including:

* All active/inactive medication fields
* RXNorm codes
* Date filters based on request
* Source differentiation (`nurse` vs `doctor`)
* LOINC template and section titles for CCDA

---

### 🧠 Observation

* Special characters likely came from fields like:

  * `doc_presc_comments`
  * `current_medication_notes`
  * `drugname`, `dosagename`, or `addi`
* These fields are later used to construct the **narrative block**, which maps to the `<text>` section of the CDA.

---

### 🔄 **Main Changes in Logic**

| Section                        | Previous Query | This Query   |
| ------------------------------ | -------------- | ------------ |
| `current_medication_is_active` | `true`         | `false`      |
| `doc_presc_is_active`          | `true`         | `false`      |
| `med_status_group`             | `'active'`     | `'inactive'` |

This ensures you're fetching **only deactivated / stopped / expired prescriptions** from both sources:

* **Nurse-entered medications**: from `current_medication`
* **Doctor prescriptions**: from `doc_presc`

---
### Query 1 :

```
select * from ( select distinct on(current_medication_id) current_medication_id  as prescId, current_medication_encounter_id  as EncounterId, current_medication_status  as statusid ,current_medication_prescribed_by as prescByID ,current_medication_is_chronic  as ischronic, format_name(COALESCE(b.emp_profile_fname,''),COALESCE(b.emp_profile_lname,''),COALESCE(b.emp_profile_mi,''),COALESCE(b.emp_profile_credentials,''),1) as orderBy,b.emp_profile_empid as orderbyid ,upper(glace_timezone(current_medication_order_on,'EDT','MM/dd/yyyy HH:MI:ss am')) as OrderDate,format_name(COALESCE(a.emp_profile_fname,''),COALESCE(a.emp_profile_lname,''),COALESCE(a.emp_profile_mi,''),COALESCE(a.emp_profile_credentials,''),1) as ModifiedBy ,upper(glace_timezone(current_medication_modified_on,'EDT','MM/dd/yyyy HH:MI:ss am')) as ModifiedDate ,  current_medication_intake as Number ,  current_medication_quantity  as qty , coalesce(current_medication_rx_name,'') as drugname , coalesce(current_medication_dosage_with_unit,'')  as dosagename,  coalesce(current_medication_unit,'')  as unitname,'' as units, coalesce(current_medication_refills,'')  as refills ,   coalesce(current_medication_route,'') as route , coalesce(current_medication_duration,'')  as duration , current_medication_lot_number as lotno,upper(glace_timezone(current_medication_expiry_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as expdate,current_medication_days as days, current_medication_frequency1 as Sch1 , case when current_medication_frequency1=drug_schedule_name then drug_schedule_desc else current_medication_frequency1 end  as Sch1_desc , current_medication_frequency2 as Sch2 ,  current_medication_form  as form , current_medication_notes  as comments, current_medication_route_id as rootid, current_medication_allow_substitution as allowsubstitution ,split_part(upper(glace_timezone(current_medication_start_date,'EDT','MM/dd/yyyy HH:MI:ss am')), ' ', 1) as startdate, upper(glace_timezone(current_medication_order_on,'EDT','MM/dd/yyyy HH:MI:ss am')) as dateOfOrder , '' as stopdate, 'f'  as toprint, 1 as medfrom,current_medication_is_overridden as overriddenflag, current_medication_med_internal_root_source as interal_source,'' as provider_name,'' as dxforrx,'' as addi,current_medication_ndc_code as ndc_code,med_status_name as status, current_medication_modified_on::date as modified_date_tosort,   '-' as trans_mode,0 as sentId,'' as sentDoctor,'' as senttime,'' as failurereason,'' as pharmacyname,chart_id as chartid,  encounter_type as typeid, 'nurse' as curroot,current_medication_is_med_sup as ismedsup  , '-' as remainingDays,1 as drugScheme , case when current_medication_is_active=true then 'Active' else 'Inactive' end  as state,current_medication_inactivated_on as enddate,current_medication_rxnorm_code as rxnormcode from  current_medication inner join encounter on current_medication_encounter_id=encounter_id  inner join chart on chart_id=encounter_chartid inner join  patient_registration on patient_registration_id=chart_patientid  and current_medication_is_active=true  and  patient_registration_id= 18060   and current_medication_is_med_sup = false left join drug_schedule on drug_schedule_name=current_medication_frequency1 and drug_schedule_id < 200000 left join emp_profile a on current_medication_modified_by=a.emp_profile_empid left join emp_profile b on current_medication_order_by=b.emp_profile_empid left join emp_profile c on current_medication_modified_by=c.emp_profile_empid inner join med_status on med_status_id=current_medication_status and trim(med_status_group) ilike 'active' union  select distinct on(doc_presc_id) doc_presc_id  as prescId ,doc_presc_encounter_id  as EncounterId,doc_presc_status  as StatusId,doc_presc_ordered_by as prescByID,doc_presc_is_chronic  as ischronic, format_name(COALESCE(b.emp_profile_fname,''),COALESCE(b.emp_profile_lname,''),COALESCE(b.emp_profile_mi,''),COALESCE(b.emp_profile_credentials,''),1) as orderBy,b.emp_profile_empid as orderbyid,upper(glace_timezone(doc_presc_ordered_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as OrderDate,format_name(COALESCE(a.emp_profile_fname,''),COALESCE(a.emp_profile_lname,''),COALESCE(a.emp_profile_mi,''),COALESCE(a.emp_profile_credentials,''),1) as ModifiedBy,upper(glace_timezone(doc_presc_last_modified_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as ModifiedDate,  doc_presc_intake  as Number,doc_presc_quantity  as qty,doc_presc_rx_name  as drugname, doc_presc_dosage_with_unit  as dosagename, COALESCE(doc_presc_unit,'')  as unitname,COALESCE(doc_presc_quantity_units,'-')  as units,doc_presc_no_of_refills  as refills , doc_presc_route  as route,doc_presc_duration  as duration,  doc_presc_lot_number as lotno,upper(glace_timezone(doc_presc_expire_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as expdate,doc_presc_days as days,doc_presc_schedule1 as Sch1,case when doc_presc_schedule1=drug_schedule_name then drug_schedule_desc else doc_presc_schedule1 end  as Sch1_desc,doc_presc_schedule2  as Sch2, doc_presc_form as form, split_part(doc_presc_comments,'~',1)  as comments,doc_presc_route_id as rootid,doc_presc_allow_substitution as allowsubstitution,split_part(upper(glace_timezone(doc_presc_start_date,'EDT','MM/dd/yyyy HH:MI:ss am')), ' ', 1) as StartDate, upper(glace_timezone(doc_presc_ordered_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as dateOfOrder,upper(glace_timezone(doc_presc_stop_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as stopdate, doc_presc_to_print_fax  as toprint, 2 as medfrom,doc_presc_is_overridden as overriddenflag, doc_presc_med_internal_root_source as interal_source,format_name(COALESCE(c.emp_profile_fname,''),COALESCE(c.emp_profile_lname,''),COALESCE(c.emp_profile_mi,''),COALESCE(c.emp_profile_credentials,''),1) as provider_name,doc_presc_dx_for_rx as dxforrx,doc_presc_addsig as addi,COALESCE(doc_presc_ndc_code,'') as ndc_code,med_status_name as status, doc_presc_last_modified_date::date as modified_date_tosort,  case when erxStatusText is not null then erxStatusText else (case when doc_presc_is_printed ='t' then 'Print'   when doc_presc_fax_sent_id is not null then 'Fax'  when doc_presc_is_e_presc_sent = '1' then 'e-Rx' else '-' end )end as trans_mode, sentId,sentDoctor,senttime,failurereason,pharmacyname,chart_id as chartid,  encounter_type as typeid, 'doctor' as curroot,doc_presc_is_med_sup as ismedsup  , case when doc_presc_days is not null and doc_presc_days!='' and doc_presc_days !~*'[^0-9^]' then case when doc_presc_no_of_refills is not null and doc_presc_no_of_refills!='' and doc_presc_no_of_refills !~*'[A-Z]' and doc_presc_no_of_refills ::int > 0 then case when ( (doc_presc_days ::int* ((doc_presc_no_of_refills::int)+1)) - datediff(4, doc_presc_ordered_date, now())) >=1 then ( (doc_presc_days ::int* ((doc_presc_no_of_refills::int)+1)) - datediff(4, doc_presc_ordered_date, now())) ::text else 'over' end when (doc_presc_no_of_refills is null or doc_presc_no_of_refills='0' or doc_presc_no_of_refills='') then ( case when ( doc_presc_days::int - datediff(4, doc_presc_ordered_date, now())) >=1 then ( doc_presc_days::int - datediff(4, doc_presc_ordered_date, now())) ::text else 'over' end) else '-' end else '-' end as remainingDays ,case when doc_presc_drug_scheme is not null then doc_presc_drug_scheme else 1 END as drugScheme, case when doc_presc_is_active=true then 'Active' else 'Inactive' end  as state,doc_presc_inactivated_on as enddate,doc_presc_rxnorm_code as rxnormcode from doc_presc inner join encounter on encounter_id = doc_presc_encounter_id  inner join chart on chart_id=encounter_chartid inner join  patient_registration on patient_registration_id=chart_patientid and doc_presc_patient_id = 18060 and doc_presc_is_active = true  and doc_presc_is_med_sup =false left join drug_schedule on doc_presc_schedule1=drug_schedule_name and drug_schedule_id < 200000 left join emp_profile a on doc_presc_last_modified_by=a.emp_profile_empid left join emp_profile b on doc_presc_last_modified_by=b.emp_profile_empid left join emp_profile c on doc_presc_provider_id=c.emp_profile_empid inner join med_status on med_status_id=doc_presc_status and trim(med_status_group) ilike 'active' left join(select ss_outbox_prescription_id as prescId,emp_profile_empid as sentId,emp_profile_fullname as sentDoctor,ss_outbox_to_id as pharm_ncpdpid,ss_outbox_actual_sent_time as actualsenttime,case when ss_outbox_actual_sent_time is not null and  (ss_outbox_status_or_error_code ='000' or ss_outbox_status_or_error_code='010' or ss_outbox_status_or_error_code='002' or ss_outbox_status_or_error_code='003') then 'eRx Success'  when ss_outbox_actual_sent_time is not null and (ss_outbox_status_or_error_code <>'000' and ss_outbox_status_or_error_code<>'010') then 'eRx Failure' end  as erxStatusText ,glace_timezone(ss_outbox.ss_outbox_sent_date_time::timestamp with time zone,'EDT','MM/dd/yyyy HH:MI:ss am')  as senttime, replace(ss_outbox.ss_outbox_pharmacy_name,'''','') as pharmacyname,ss_status_error_codes_description as failurereason from ss_outbox inner join location_details on ss_outbox_from_id= spi_locationid inner join emp_profile on emp_profile_empid = prescriber_id inner join ss_status_error_codes on ss_status_error_codes_name = ss_outbox_status_or_error_code where ss_outbox_id::int in(select max(ss_outbox_id) from ss_outbox  where ss_outbox_patient_id = 18060 group by ss_outbox_prescription_id) and ss_outbox_actual_sent_time <> 'null' and ss_status_error_codes_type_id<>9  )sub on doc_presc.doc_presc_id=sub.prescId::integer )k order by modified_date_tosort desc, drugname

```
### Query 2 :

```
 select * from ( select distinct on(current_medication_id) current_medication_id  as prescId, current_medication_encounter_id  as EncounterId, current_medication_status  as statusid ,current_medication_prescribed_by as prescByID ,current_medication_is_chronic  as ischronic, format_name(COALESCE(b.emp_profile_fname,''),COALESCE(b.emp_profile_lname,''),COALESCE(b.emp_profile_mi,''),COALESCE(b.emp_profile_credentials,''),1) as orderBy,b.emp_profile_empid as orderbyid ,upper(glace_timezone(current_medication_order_on,'EDT','MM/dd/yyyy HH:MI:ss am')) as OrderDate,format_name(COALESCE(a.emp_profile_fname,''),COALESCE(a.emp_profile_lname,''),COALESCE(a.emp_profile_mi,''),COALESCE(a.emp_profile_credentials,''),1) as ModifiedBy ,upper(glace_timezone(current_medication_modified_on,'EDT','MM/dd/yyyy HH:MI:ss am')) as ModifiedDate ,  current_medication_intake as Number ,  current_medication_quantity  as qty , coalesce(current_medication_rx_name,'') as drugname , coalesce(current_medication_dosage_with_unit,'')  as dosagename,  coalesce(current_medication_unit,'')  as unitname,'' as units, coalesce(current_medication_refills,'')  as refills ,   coalesce(current_medication_route,'') as route , coalesce(current_medication_duration,'')  as duration , current_medication_lot_number as lotno,upper(glace_timezone(current_medication_expiry_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as expdate,current_medication_days as days, current_medication_frequency1 as Sch1 , case when current_medication_frequency1=drug_schedule_name then drug_schedule_desc else current_medication_frequency1 end  as Sch1_desc , current_medication_frequency2 as Sch2 ,  current_medication_form  as form , current_medication_notes  as comments, current_medication_route_id as rootid, current_medication_allow_substitution as allowsubstitution ,split_part(upper(glace_timezone(current_medication_start_date,'EDT','MM/dd/yyyy HH:MI:ss am')), ' ', 1) as startdate, upper(glace_timezone(current_medication_order_on,'EDT','MM/dd/yyyy HH:MI:ss am')) as dateOfOrder , '' as stopdate, 'f'  as toprint, 1 as medfrom,current_medication_is_overridden as overriddenflag, current_medication_med_internal_root_source as interal_source,'' as provider_name,'' as dxforrx,'' as addi,current_medication_ndc_code as ndc_code,med_status_name as status, current_medication_modified_on::date as modified_date_tosort,   '-' as trans_mode,0 as sentId,'' as sentDoctor,'' as senttime,'' as failurereason,'' as pharmacyname,chart_id as chartid,  encounter_type as typeid, 'nurse' as curroot,current_medication_is_med_sup as ismedsup  , '-' as remainingDays,1 as drugScheme , case when current_medication_is_active=true then 'Active' else 'Inactive' end  as state,current_medication_inactivated_on as enddate,current_medication_rxnorm_code as rxnormcode from  current_medication inner join encounter on current_medication_encounter_id=encounter_id  inner join chart on chart_id=encounter_chartid inner join  patient_registration on patient_registration_id=chart_patientid  and current_medication_is_active=false  and  patient_registration_id= 18060   and current_medication_is_med_sup = false left join drug_schedule on drug_schedule_name=current_medication_frequency1 and drug_schedule_id < 200000 left join emp_profile a on current_medication_modified_by=a.emp_profile_empid left join emp_profile b on current_medication_order_by=b.emp_profile_empid left join emp_profile c on current_medication_modified_by=c.emp_profile_empid inner join med_status on med_status_id=current_medication_status and trim(med_status_group) ilike 'inactive' union  select distinct on(doc_presc_id) doc_presc_id  as prescId ,doc_presc_encounter_id  as EncounterId,doc_presc_status  as StatusId,doc_presc_ordered_by as prescByID,doc_presc_is_chronic  as ischronic, format_name(COALESCE(b.emp_profile_fname,''),COALESCE(b.emp_profile_lname,''),COALESCE(b.emp_profile_mi,''),COALESCE(b.emp_profile_credentials,''),1) as orderBy,b.emp_profile_empid as orderbyid,upper(glace_timezone(doc_presc_ordered_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as OrderDate,format_name(COALESCE(a.emp_profile_fname,''),COALESCE(a.emp_profile_lname,''),COALESCE(a.emp_profile_mi,''),COALESCE(a.emp_profile_credentials,''),1) as ModifiedBy,upper(glace_timezone(doc_presc_last_modified_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as ModifiedDate,  doc_presc_intake  as Number,doc_presc_quantity  as qty,doc_presc_rx_name  as drugname, doc_presc_dosage_with_unit  as dosagename, COALESCE(doc_presc_unit,'')  as unitname,COALESCE(doc_presc_quantity_units,'-')  as units,doc_presc_no_of_refills  as refills , doc_presc_route  as route,doc_presc_duration  as duration,  doc_presc_lot_number as lotno,upper(glace_timezone(doc_presc_expire_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as expdate,doc_presc_days as days,doc_presc_schedule1 as Sch1,case when doc_presc_schedule1=drug_schedule_name then drug_schedule_desc else doc_presc_schedule1 end  as Sch1_desc,doc_presc_schedule2  as Sch2, doc_presc_form as form, split_part(doc_presc_comments,'~',1)  as comments,doc_presc_route_id as rootid,doc_presc_allow_substitution as allowsubstitution,split_part(upper(glace_timezone(doc_presc_start_date,'EDT','MM/dd/yyyy HH:MI:ss am')), ' ', 1) as StartDate, upper(glace_timezone(doc_presc_ordered_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as dateOfOrder,upper(glace_timezone(doc_presc_stop_date,'EDT','MM/dd/yyyy HH:MI:ss am')) as stopdate, doc_presc_to_print_fax  as toprint, 2 as medfrom,doc_presc_is_overridden as overriddenflag, doc_presc_med_internal_root_source as interal_source,format_name(COALESCE(c.emp_profile_fname,''),COALESCE(c.emp_profile_lname,''),COALESCE(c.emp_profile_mi,''),COALESCE(c.emp_profile_credentials,''),1) as provider_name,doc_presc_dx_for_rx as dxforrx,doc_presc_addsig as addi,COALESCE(doc_presc_ndc_code,'') as ndc_code,med_status_name as status, doc_presc_last_modified_date::date as modified_date_tosort,  case when erxStatusText is not null then erxStatusText else (case when doc_presc_is_printed ='t' then 'Print'   when doc_presc_fax_sent_id is not null then 'Fax'  when doc_presc_is_e_presc_sent = '1' then 'e-Rx' else '-' end )end as trans_mode, sentId,sentDoctor,senttime,failurereason,pharmacyname,chart_id as chartid,  encounter_type as typeid, 'doctor' as curroot,doc_presc_is_med_sup as ismedsup  , case when doc_presc_days is not null and doc_presc_days!='' and doc_presc_days !~*'[^0-9^]' then case when doc_presc_no_of_refills is not null and doc_presc_no_of_refills!='' and doc_presc_no_of_refills !~*'[A-Z]' and doc_presc_no_of_refills ::int > 0 then case when ( (doc_presc_days ::int* ((doc_presc_no_of_refills::int)+1)) - datediff(4, doc_presc_ordered_date, now())) >=1 then ( (doc_presc_days ::int* ((doc_presc_no_of_refills::int)+1)) - datediff(4, doc_presc_ordered_date, now())) ::text else 'over' end when (doc_presc_no_of_refills is null or doc_presc_no_of_refills='0' or doc_presc_no_of_refills='') then ( case when ( doc_presc_days::int - datediff(4, doc_presc_ordered_date, now())) >=1 then ( doc_presc_days::int - datediff(4, doc_presc_ordered_date, now())) ::text else 'over' end) else '-' end else '-' end as remainingDays ,case when doc_presc_drug_scheme is not null then doc_presc_drug_scheme else 1 END as drugScheme, case when doc_presc_is_active=true then 'Active' else 'Inactive' end  as state,doc_presc_inactivated_on as enddate,doc_presc_rxnorm_code as rxnormcode from doc_presc inner join encounter on encounter_id = doc_presc_encounter_id  inner join chart on chart_id=encounter_chartid inner join  patient_registration on patient_registration_id=chart_patientid and doc_presc_patient_id = 18060 and doc_presc_is_active = false  and doc_presc_is_med_sup =false left join drug_schedule on doc_presc_schedule1=drug_schedule_name and drug_schedule_id < 200000 left join emp_profile a on doc_presc_last_modified_by=a.emp_profile_empid left join emp_profile b on doc_presc_last_modified_by=b.emp_profile_empid left join emp_profile c on doc_presc_provider_id=c.emp_profile_empid inner join med_status on med_status_id=doc_presc_status and trim(med_status_group) ilike 'inactive' left join(select ss_outbox_prescription_id as prescId,emp_profile_empid as sentId,emp_profile_fullname as sentDoctor,ss_outbox_to_id as pharm_ncpdpid,ss_outbox_actual_sent_time as actualsenttime,case when ss_outbox_actual_sent_time is not null and  (ss_outbox_status_or_error_code ='000' or ss_outbox_status_or_error_code='010' or ss_outbox_status_or_error_code='002' or ss_outbox_status_or_error_code='003') then 'eRx Success'  when ss_outbox_actual_sent_time is not null and (ss_outbox_status_or_error_code <>'000' and ss_outbox_status_or_error_code<>'010') then 'eRx Failure' end  as erxStatusText ,glace_timezone(ss_outbox.ss_outbox_sent_date_time::timestamp with time zone,'EDT','MM/dd/yyyy HH:MI:ss am')  as senttime, replace(ss_outbox.ss_outbox_pharmacy_name,'''','') as pharmacyname,ss_status_error_codes_description as failurereason from ss_outbox inner join location_details on ss_outbox_from_id= spi_locationid inner join emp_profile on emp_profile_empid = prescriber_id inner join ss_status_error_codes on ss_status_error_codes_name = ss_outbox_status_or_error_code where ss_outbox_id::int in(select max(ss_outbox_id) from ss_outbox  where ss_outbox_patient_id = 18060 group by ss_outbox_prescription_id) and ss_outbox_actual_sent_time <> 'null' and ss_status_error_codes_type_id<>9  )sub on doc_presc.doc_presc_id=sub.prescId::integer )k order by modified_date_tosort desc, drugname

```
---

### Issue Summary:

The `doc_presc_intake` field contained invalid, non-printable ASCII character `CHR(2)` (`\x02`), caused by entering `1\2` instead of `1/2`. This corrupted data led to CCDA interface validation errors.

---

### Verification Queries and Results:

#### 1. Check for fraction-like strings in `current_medication_intake`:

```sql
SELECT DISTINCT current_medication_intake 
FROM current_medication 
WHERE current_medication_intake ~ '\d+/\d+';
```

*Returns all distinct intakes with fraction formats like `1/2`.*

---

#### 2. Find corrupted records in `doc_presc` for specific patients (with `\x02` present):

```sql
SELECT DISTINCT doc_presc_intake, doc_presc_patient_id, doc_presc_id
FROM doc_presc
WHERE doc_presc_intake LIKE '%' || CHR(2) || '%'
  AND doc_presc_patient_id IN ('018838', '9340', '018060');
```

| doc\_presc\_intake | doc\_presc\_patient\_id | doc\_presc\_id |
| ------------------ | ----------------------- | -------------- |
| 1\x02tab           | 18060                   | 47177          |
| 1\x02Tab           | 9340                    | 58005          |
| 1\x02tsp           | 18838                   | 47012          |

---

#### 3. Find all records with invalid `\x02` in `doc_presc_intake`:

```sql
SELECT DISTINCT doc_presc_intake, doc_presc_patient_id, doc_presc_id
FROM doc_presc
WHERE doc_presc_intake LIKE '%' || CHR(2) || '%';
```

*Result matches above 3 rows.*

---

#### 4. Confirm corrected values after update:

```sql
SELECT doc_presc_id, doc_presc_intake, doc_presc_patient_id
FROM doc_presc
WHERE doc_presc_id IN (47177, 58005, 47012);
```

| doc\_presc\_id | doc\_presc\_intake | doc\_presc\_patient\_id |
| -------------- | ------------------ | ----------------------- |
| 47012          | 1/2 tsp            | 18838                   |
| 47177          | 1/2 tab            | 18060                   |
| 58005          | 1/2 Tab            | 9340                    |

---

### Summary of Fix:

* Backed up affected rows before update.
* Replaced `CHR(2)` (`\x02`) with `/` in `doc_presc_intake`.
* Validated data to ensure CCDA interface compliance.
* Confirmed `doc_presc_intake` values now use proper fractions like `1/2`.

---

---

# 📄 README: Medication Intake Field Correction – `doc_presc`

**Date of Fix:** July 23, 2025
**Reported by:** Gobala Krishnan B 
**Environment:** Production
**Related Tables:** `doc_presc`

---

## ✅ Description

This correction addresses a data entry issue where a non-printable ASCII character (`CHR(2)` or `\x02`) appeared in the `doc_presc_intake` field, due to users typing `1\2` instead of `1/2`.

This malformed character caused:

* **CCDA interface validation failures**
* **User interface rendering issues**
* **Incorrect dosage interpretation in downstream systems**

---

## 🔎 Root Cause

* Users entered backslash (`\`) in medication intake like `1\2 tab`
* The backslash was interpreted as a control character (`CHR(2)`), resulting in invalid values stored as `1\x02tab`, `1\x02Tab`, `1\x02tsp`

---

## 🛠️ Action Taken

### 1. Backed Up Affected Records

```sql
\copy (
  SELECT * 
  FROM doc_presc 
  WHERE doc_presc_id IN (47177, 58005, 47012)
) TO 'doc_presc_maureen_bkp_july_23_2025.csv' WITH CSV HEADER;
```

### 2. Performed Targeted Data Updates

```sql
UPDATE doc_presc SET doc_presc_intake = '1/2 tab' WHERE doc_presc_id = 47177;
UPDATE doc_presc SET doc_presc_intake = '1/2 Tab' WHERE doc_presc_id = 58005;
UPDATE doc_presc SET doc_presc_intake = '1/2 tsp' WHERE doc_presc_id = 47012;
```

---

## 🧪 Post-Fix Validation

* Confirmed values render correctly in UI
* CCDA interface tests passed
* No side effects observed on related workflows

---

## 📁 Backup Details

* **Backup File:** `doc_presc_maureen_bkp_july_23_2025.csv`
* **Location:** \[Insert storage path if applicable]
* **Record Count:** 3
* **Prescription IDs:** 47177, 58005, 47012

---
