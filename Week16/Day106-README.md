# CMS149v13 - Dementia: Cognitive Assessment

**Date:** September 1, 2025
**Day:** 106
**Provider:** Cassandra Stone (Employee ID: 2432)

---

## 📄 Measure Information

* **2024 Specification:** [CMS149v12 - 2024](https://static.glaceemr.com/ECQM/2024/CMS149v12.html)
* **2025 Specification:** [CMS149v13 - 2025](https://static.glaceemr.com/ECQM/2025/CMS149v13.html)
* **eCQM Info API:** [Glace Data Gateway](https://datagateway.glaceemr.com/DataGatewayMediSpan/eCQMServices/getECQMInfoById?ids=281&reportingYear=2025)

---

## 🔧 Configuration Details

### 1. MIPS Provider Configuration

The following SQL was executed to register the provider's participation in MIPS for the 2025 reporting year.

```sql
INSERT INTO macra_provider_configuration (
  macra_provider_configuration_provider_id,
  macra_provider_configuration_reporting_year,
  macra_provider_configuration_reporting_start,
  macra_provider_configuration_reporting_end,
  macra_provider_configuration_reporting_method,
  macra_provider_configuration_report_type,
  macra_provider_configuration_hardship_exemption,
  macra_provider_configuration_aci_end,
  macra_provider_configuration_aci_start
) VALUES (
  2432, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);
```

### 2. Quality Measure Mapping

The following mapping registers the provider to report on **CMS149v13** (eCQM ID: **281**) for the 2025 calendar year:

```sql
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2432, '2025', '281'
);
```

---


