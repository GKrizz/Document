
# Day 117 – September 12, 2025 (Friday)

## Overview

This update focuses on cardiac surgery procedure tracking with CPT codes and relevant encounter data queries.

---

## Procedures Documented

Two key cardiac surgery CPT codes are included:

| CPT Code | Description     | Status    | Effective Time (Start - End)        |
| -------- | --------------- | --------- | ----------------------------------- |
| 92920    | Cardiac Surgery | Completed | 2014-11-13 08:00 - 2024-11-12 09:00 |
| 33510    | Cardiac Surgery | Completed | 2016-09-14 08:00 - 2016-09-14 11:15 |

Each procedure includes:

* Procedure template IDs for standardization.
* Status code indicating completion.
* QDM (Quality Data Model) rank attribute for priority.

---

## SQL Queries

Two SQL queries demonstrate how to fetch encounter and CPT service details:

### Query 1: Encounters by Doctor and Patient with Specific CPT Codes

```sql
SELECT encounter0_.encounter_id AS col_0_0_,
       cpt3_.cpt_cptcode AS col_1_0_,
       encounter0_.encounter_created_date AS col_2_0_,
       encounter0_.encounter_closed_date AS col_3_0_
FROM encounter encounter0_
INNER JOIN chart chart1_ ON encounter0_.encounter_chartid = chart1_.chart_id
INNER JOIN service_detail servicedet2_ ON chart1_.chart_patientid = servicedet2_.service_detail_patientid
INNER JOIN cpt cpt3_ ON servicedet2_.service_detail_cptid = cpt3_.cpt_id
WHERE encounter0_.encounter_service_doctor IN (2436)
  AND chart1_.chart_patientid = 3276829
  AND cpt3_.cpt_cptcode IN ('33510', '92920')
  AND DATE(encounter0_.encounter_created_date) = DATE(servicedet2_.service_detail_dos);
```

### Query 2: Encounters for a Patient with Matching Dates

```sql
SELECT encounter0_.encounter_id,
       encounter0_.encounter_created_date,
       servicedet2_.service_detail_dos,
       cpt3_.cpt_cptcode
FROM encounter encounter0_
JOIN chart chart1_ ON encounter0_.encounter_chartid = chart1_.chart_id
JOIN service_detail servicedet2_ ON chart1_.chart_patientid = servicedet2_.service_detail_patientid
JOIN cpt cpt3_ ON servicedet2_.service_detail_cptid = cpt3_.cpt_id
WHERE chart1_.chart_patientid = 3276836
  AND DATE(encounter0_.encounter_created_date) = DATE(servicedet2_.service_detail_dos);
```

---
