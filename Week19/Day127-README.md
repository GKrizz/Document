

# 📅 Day 125 – September 20, 2025

## 🩺 CMS90v14 – Functional Status Assessments for Heart Failure

---

### ✅ Summary

- **Measure**: CMS90v14
- **Title**: Functional Status Assessments for Heart Failure
- **Employee**: Calvin Roy *(ID: 2444)*
- **Status**: Completed
- **Drool Changes**: ❌ *No change in drool file*

---

### ⚙️ Configuration Changes

#### ✅ Provider MIPS Configuration

```sql
-- Insert provider MIPS configuration
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
  2444, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);
````

#### ✅ Quality Measure Mapping

```sql
-- Map Quality Measure
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2444, '2025', '377'
);
```

---

### 🧪 Final Output & Validation

* ✅ **QRDA III**: PASSED <img width="586" height="528" alt="QRDA III Passed" src="https://github.com/user-attachments/assets/aa4273fd-953c-4c14-a3e8-f9c92806202b" />

* ✅ **QRDA I**: PASSED <img width="556" height="463" alt="QRDA I Passed" src="https://github.com/user-attachments/assets/919e74a9-00da-4525-9aff-da3e6b7e5584" />

---

### 🗂️ Files & References

* No drool updates for this measure.
* QRDA validations passed successfully.

---

✅ **Completed & Committed**




