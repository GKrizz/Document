# Day 24 – June 11, 2025 (Wednesday)

## Tasks Completed

### Task 1: Completed Audit Log in Legacy System

* Successfully implemented and completed the audit logging feature in the legacy system.

### Task 2: Understand Drools Rule

* Studied the structure of Drools rules.
* Example syntax reviewed:

  ```drools
  rule "Rule Name"
  when
      // conditions (patterns on your facts)
  then
      // actions to perform
  end
  ```

### Task 3: Read the MIPS Documentation

## 🩺 Introduction to MIPS

```
                            ┌────────────────────────────┐
                            │      MIPS Overview (CMS)    │
                            └─────────────┬──────────────┘
                                          │
                                          ▼
                   ┌─────────────────────────────────────────┐
                   │        1. Eligible Clinicians           │
                   │  (Physicians, NPs, PAs, CNS, Groups)    │
                   └─────────────┬───────────────────────────┘
                                 │
                                 ▼
               ┌────────────────────────────────────────────┐
               │    2. Annual Data Reporting Required         │
               │  (Claims, EHR, QCDRs, CMS Web Interface)     │
               └─────────────┬────────────────────────────────┘
                             │
                             ▼
         ┌────────────────────────────────────────────────────┐
         │   3. MIPS Performance Categories (Weighted Scoring) │
         │                                                    │
         │  1. Quality (Clinical Measures, Specialty-specific) │
         │  2. Promoting Interoperability (EHR Use, Data Sharing)│
         │  3. Improvement Activities (Practice Enhancements)  │
         │  4. Cost (Medicare Spending, Efficiency)            │
         └─────────────┬──────────────────────────────────────┘
                       │
                       ▼
        ┌────────────────────────────────────────────────────┐
        │      4. Calculate MIPS Final Score (0 – 100 Points) │
        └─────────────┬──────────────────────────────────────┘
                     │
     ┌───────────────┼───────────────┬
     │               │               │              
     ▼               ▼               ▼           
┌──────────┐   ┌────────────┐   ┌────────────┐   
│ High     │   │ Neutral    │   │ Low Score  │
│ Score    │   │ Score      │   │            │  
└────┬─────┘   └─────┬──────┘   └──────┬─────┘   
     │               │                 │
     ▼               ▼                 ▼
┌─────────────┐ ┌──────────────┐ ┌──────────────────────┐
│ Bonus       │ │ No Change    │ │ Penalty on Medicare  │
│ Payment     │ │ to Payment   │ │ Reimbursement        │
└─────────────┘ └──────────────┘ └──────────────────────┘
                         │
                         ▼
       ┌──────────────────────────────────────────┐
       │    5. Feedback Reports from CMS              │
       │    (Performance Summary & Improvement Areas) │
       └─────────────────────┬────────────────────┘
                             │
                             ▼
         ┌────────────────────────────────────────┐
         │  6. Quality Improvement Implementation │
         │ (Better Care, Patient Outcomes, Value) │
         └────────────────────────────────────────┘
```

---

## 🎯 Purpose of MIPS

```
                             ┌───────────────────────────────┐
                             │      Purpose of MIPS           │
                             └─────────────┬─────────────────┘
                                           │
                                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 1. Promoting Quality Care                        │
           │ - Incentivize high-quality care                   │
           │ - Focus on patient outcomes and satisfaction     │
           └───────────────┬─────────────────────────────────┘
                           │
                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 2. Transitioning to Value-Based Care            │
           │ - Shift reimbursement from volume to value      │
           │ - Reward high-value care delivery               │
           └───────────────┬─────────────────────────────────┘
                           │
                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 3. Improving Patient Outcomes                   │
           │ - Encourage preventive care, chronic management │
           │ - Support care coordination                     │
           └───────────────┬─────────────────────────────────┘
                           │
                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 4. Reducing Healthcare Costs                    │
           │ - Promote cost-effective, efficient practices   │
           │ - Balance cost and quality                      │
           └───────────────┬─────────────────────────────────┘
                           │
                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 5. Encouraging Performance Measurement & Reporting│
           │ - Require data reporting across key measures    │
           │ - Foster transparency and accountability        │
           └───────────────┬─────────────────────────────────┘
                           │
                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 6. Supporting Continuous Quality Improvement    │
           │ - Provide feedback on performance               │
           │ - Encourage best practices and ongoing learning │
           └───────────────┬─────────────────────────────────┘
                           │
                           ▼
           ┌─────────────────────────────────────────────────┐
           │ 7. Aligning Incentives with Healthcare Goals     │
           │ - Link payments to performance metrics           │
           │ - Target goals: reduce readmissions, improve meds│
           │   enhance engagement, advance health equity      │
           └─────────────────────────────────────────────────┘
```

---

## 🔄 MIPS Participation Process

```
                          ┌─────────────────────────────┐
                          │      1. Eligibility Check   │
                          │ - Confirm clinician/practice│
                          │   meets CMS criteria        │
                          │ - Based on clinician type & │
                          │   Medicare service volume   │
                          └───────────────┬─────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────────┐
                          │  2. Understand Performance  │
                          │     Categories              │
                          │ - Quality                   │
                          │ - Promoting Interoperability│
                          │ - Improvement Activities    │
                          │ - Cost                      │
                          └───────────────┬─────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────────┐
                          │  3. Data Collection &       │
                          │     Reporting               │
                          │ - Use EHR, QCDRs, CMS tools │
                          │ - Ensure accuracy & timely  │
                          └───────────────┬─────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────────┐
                          │  4. Performance Evaluation  │
                          │ - CMS scores each category  │
                          │ - Calculate final MIPS score│
                          │   (0-100 points)            │
                          └───────────────┬─────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────────┐
                          │  5. Payment Adjustment      │
                          │ - Positive, neutral, or     │
                          │   negative payment based on │
                          │   final score               │
                          │ - Applies two years later   │
                          └───────────────┬─────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────────┐
                          │  6. Feedback & Improvement  │
                          │ - Receive CMS performance   │
                          │   reports                   │
                          │ - Identify strengths &      │
                          │   areas for improvement     │
                          └───────────────┬─────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────────┐
                          │  7. Continued Participation │
                          │ - Report annually           │
                          │ - Stay updated on changes   │
                          │ - Maintain/improve scores   │
                          └─────────────────────────────┘
```

---

## MIPS Eligibility and Reporting Summary

| Situation                                                      | Action                                                                      |
| -------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Not MIPS eligible + Opt-in = YES + Certified EHR**           | Submit 90 days of Promoting Interoperability (PI) data or check exemptions. |
| **Not MIPS eligible + Opt-in = YES + No/Unsure Certified EHR** | Confirm with EMR vendor for exemptions.                                     |
| **Not MIPS eligible + Opt-in = NO**                            | No reporting required.                                                      |
| **Eligible + Small Practice (≤15 clinicians) + EHR**           | Apply for small practice exemption (score reweight).                        |
| **Eligible + Small Practice + No EHR**                         | Apply for small practice PI exemption (score reweight).                     |
| **Eligible + Large Practice + Certified EHR**                  | Submit 90 days of PI data or check exemptions.                              |
| **Eligible + Large Practice + No/Unsure Certified EHR**        | Check with EMR vendor for exemptions.                                       |

---

## MIPS & APM (Alternative Payment Model) Relationship

| Question                                                     | Outcome                                              |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| **Not in any APM**                                           | Subject to **MIPS** (reporting, penalties).          |
| **In APM but NOT Advanced APM**                              | Follow **MIPS path** with some rewards.              |
| **In Advanced APM with sufficient patients**                 | **Excluded from MIPS**, get fee updates and rewards. |
| **In Advanced APM with insufficient patients or low volume** | Subject to **MIPS** unless first year or low-volume. |

---

## Database Table Summary

| Table Name                                 | Purpose / Description                                                                                  |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `quality_measures_patient_entries`         | Stores each patient’s entries for configured quality measures status.                                  |
| `quality_measures_provider_mapping`        | Maintains providers' configured measures for every year (which quality measures are assigned).         |
| `macra_measures_rate`                      | Contains rate or scoring details for MACRA measures.                                                   |
| `macra_provider_configuration`             | Stores provider-specific configurations for MACRA/MIPS participation.                                  |
| `mips_notification`                        | Stores notifications or alerts related to MIPS for providers.                                          |
| `mips_scores`                              | Maintains MIPS final scores and details, used for generating PDF reports.                              |
| `mips_weightage`                           | Stores the weightage assigned to each MIPS performance category (Quality, PI, IA, Cost) for all years. |
| `quality_measures_patient_entries_history` | History table for patient entries, storing previous years’ data for auditing/tracking.                 |
| `macra_configuration`                      | Contains MIPS/MACRA program configurations, like thresholds and rules for each year.                   |
| `ia_measures`                              | Documentation related to Improvement Activities (IA) for each provider.                                |
| `pi_exclusion`                             | Configuration table for Promoting Interoperability (PI) exclusions.                                    |
| `pi_exclusion_details`                     | Stores details of PI exclusion applied for each provider.                                              |

### Table Relationships (simplified):

```
                  +-----------------------------+
                  |     macra_configuration     |
                  |  (MACRA yearly settings)     |
                  +-------------┬---------------+
                                │
             +------------------┼--------------------+
             │                  │                    │
+---------------------------+ +-----------------------+ +--------------------+
| quality_measures_provider_mapping | mips_weightage       | macra_measures_rate  |
| (Provider measures per year)      | (MIPS scoring weight) | (MACRA measure rates)|
+---------------┬------------------+-----------------------+---------------------+
                │
+---------------┼----------------------------------+
|               │                                  |
| +-------------------------------+   +------------------------------+
| | quality_measures_patient_entries|   |quality_measures_patient_entries_history|
| | (Patient entries for measures) |   | (History of patient entries)            |
| +-------------------------------+   +------------------------------+

+-------------------+                  +----------------------+
|   ia_measures     |                  |   pi_exclusion        |
| (Improvement Activities)             | (PI exclusion config) |
+--------┬----------+                  +-----------┬----------+
         │                                     │
         │                                     │
         │                           +-------------------------+
         │                           | pi_exclusion_details     |
         │                           | (Applied PI exclusions)  |
         │                           +-------------------------+

+-------------------+
|  mips_scores      |
| (Final MIPS scores)|
+-------------------+

+-------------------+
|  mips_notification |
| (MIPS alerts)      |
+-------------------+
```

---

## Software Architecture Layers

| Layer                      | Purpose                                | Example Files / Packages                  |
| -------------------------- | -------------------------------------- | ----------------------------------------- |
| **Legacy (Old MVC + JSP)** | UI + old action handling               | `jsp/`, `src/com/glenwood/hcare/actions/` |
| **Models (Entities)**      | Represent DB tables / business objects | `models/` (e.g., `MIPSScores.java`)       |
| **Repositories**           | DB interaction (CRUD)                  | `repositories/`                           |
| **Controllers**            | HTTP request handling                  | `controllers/`                            |
| **Services**               | Business logic, data processing        | `services/chart/MIPS/`                    |

### Legacy vs Spring (New)

| Legacy (Old)                           | Spring (New)                                    |
| -------------------------------------- | ----------------------------------------------- |
| UI Layer: JSP + JavaScript             | UI Layer: Thymeleaf / SPA + Controller APIs     |
| Controller Layer: Java Action Classes  | Controller Layer: Spring MVC REST Controllers   |
| Service Layer: Embedded in Actions     | Service Layer: Services & Implementations       |
| Data Access Layer: JDBC/ORM in Actions | Data Access Layer: Spring Data JPA Repositories |
| Domain Models: POJOs or Old Models     | Domain Models: JPA Entities                     |
| Configuration: Java Config, XML, JSP   | Configuration: Spring Boot Config Classes       |

---

## 2024 Quality Measure Tables

| Table Name                                        | Purpose / Description                                                                                              |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `quality_measures_2024`                           | Contains the list and definitions of quality measures for 2024 (clinical performance metrics).                     |
| `quality_measures_submission_method_2024`         | Tracks allowed submission methods (claims, registry, EHR) for each quality measure in 2024.                        |
| `ecqm_specifications_2024`                        | Holds detailed electronic clinical quality measure (eCQM) specifications for 2024, including logic and value sets. |
| `additional_valuesets_2024`                       | Contains extra value sets (code sets) used by 2024 quality measures to identify clinical concepts.                 |
| `mips_benchmark_2024`                             | Stores benchmark data for MIPS measures for 2024, used to calculate relative performance scores.                   |
| `mips_improvement_activities_2024`                | Lists improvement activities eligible for MIPS scoring in 2024, including categories and weights.                  |
| `2024_claims_individual_measures_single_source`   | Holds individual measure data sourced from claims submissions for 2024 (used for quality reporting).               |
| `2024_registry_individual_measures_single_source` | Holds individual measure data sourced from registry submissions for 2024.                                          |

---
