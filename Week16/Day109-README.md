

### 1. **Identify Patients by Principal Doctor**

You start by selecting patients who have a specific **principal doctor** (e.g., `'2435'`):

```sql
SELECT
  patient_registration_id,
  patient_registration_last_name,
  patient_registration_first_name,
  patient_registration_address1,
  patient_registration_address2,
  patient_registration_city
FROM patient_registration
WHERE patient_registration_principal_doctor = '2435';
```

**Result:** List of patients linked to that doctor.

---

### 2. **Filter Patients by First Name**

You refine this list to only patients whose first names are in a given set:

```sql
SELECT patient_registration_id, patient_registration_last_name, patient_registration_first_name, patient_registration_address1, patient_registration_address2, patient_registration_city
FROM patient_registration
WHERE patient_registration_principal_doctor = '2435'
  AND patient_registration_first_name IN ('Bobby', 'Kristina', 'Rosemary');
```

**Result:** Filtered list of patients under the same doctor with those first names.

---

### 3. **Find Encounters for Those Patients in a Date Range**

Using patient IDs from the filtered list, you find all encounters for those patients within a given date range (`2025`):

```sql
SELECT DISTINCT encounter_service_doctor, encounter_date
FROM chart
INNER JOIN encounter ON chart.chart_id = encounter.encounter_chartid
WHERE chart.chart_patientid IN (3276631, 3276626, 3276620, 3276612, 3276610, 3276606)
  AND encounter.encounter_date BETWEEN '2025-01-01' AND '2025-12-31';
```

**Result:** Encounter dates and doctors for those patients in the year 2025.

---

### 4. **Detailed Join to Get Patient and Encounter Info for a Specific Doctor**

You join three tables (`chart`, `encounter`, and `patient_registration`) to get detailed info about patients and their encounters with a **specific service doctor** (e.g., `223`) during 2025:

```sql
SELECT DISTINCT
  pr.patient_registration_id,
  pr.patient_registration_first_name AS fn,
  pr.patient_registration_last_name AS ln,
  e.encounter_service_doctor,
  e.encounter_date
FROM chart c
JOIN encounter e ON c.chart_id = e.encounter_chartid
JOIN patient_registration pr ON c.chart_patientid = pr.patient_registration_id
WHERE c.chart_patientid IN (3276631, 3276626, 3276620, 3276612, 3276610, 3276606)
  AND e.encounter_date BETWEEN '2025-01-01' AND '2025-12-31'
  AND e.encounter_service_doctor = 223;
```

**Result:** Patient and encounter details filtered by doctor 223.

---

### 5. **Get Doctor's Name from Employee Profile**

Finally, you query the employee profile to get the full name of the service doctor:

```sql
SELECT emp_profile_empid, emp_profile_fname, emp_profile_lname
FROM emp_profile
WHERE emp_profile_empid = 223;
```

**Result:** Doctor's full name (e.g., Saranya Saranya).

---





<img width="1279" height="500" alt="image" src="https://github.com/user-attachments/assets/369581fa-14cd-487c-b1e5-488667bea1e2" />
