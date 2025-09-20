# 📄 CMS771v6 – IPSS or AUA-SI Symptom Score Change 6–12 Months After BPH Diagnosis

**Measure ID:** 476
**Employee Name:** Gail Vargas
**Employee ID:** 2442
📅 **Date:** September 20, 2025 (Day 125)

---

## 🛠️ Summary of Updates

### 1. ✅ No Change in Drools Logic

* There were **no modifications** to Drools rules for this update.
* **Measure Logic File:** `CMS771v5.drl` remains unchanged.

---

### 2. 🧠 Java Component Update

* **File Modified:** `PatientDataSectionHandler.java`
* **Method Affected:** `getPhysicalExamComponent`

> Minor logic handling improvements or optimizations were applied in the Java component that supports data extraction for the measure.

---

## 🧾 Database Configuration

### 🔧 MIPS Provider Configuration

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
  2442, 2025, '2025-01-01', '2025-12-31', 2, 2, 'f', '2025-12-31', '2025-01-01'
);
```

### 📌 Quality Measure Mapping

```sql
INSERT INTO quality_measures_provider_mapping (
  quality_measures_provider_mapping_provider_id,
  quality_measures_provider_mapping_reporting_year,
  quality_measures_provider_mapping_measure_id
) VALUES (
  2442, '2025', '476'
);
```

---

## ✅ Final Output & Validation

### ✔️ QRDA III: **PASSED**

<img width="762" height="584" alt="QRDA III Passed" src="https://github.com/user-attachments/assets/691f7aac-fb95-4ba7-a769-ab48a5456997" />

---

### ✔️ QRDA I: **PASSED**

<img width="564" height="474" alt="QRDA I Passed" src="https://github.com/user-attachments/assets/9b7831b8-6395-4f6c-9176-cbcf99ac994c" />

---
