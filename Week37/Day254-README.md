# Value Set OID Processing via SFTP (2026)

## 🔐 SFTP Connection Details


| Parameter        | Value              |
| ---------------- | ------------------ |
| Protocol         | SFTP               |
| Server           | `ftp.glaceemr.com` |
| Port             | `8444`             |
| Username         | `glenwood`         |
| Remote Base Path | `/Temp/Mounika`    |

---

## 🔗 Step 1: Connect to SFTP Server

```bash
sftp -oPort=8444 glenwood@ftp.glaceemr.com
```

**Password:** 

```
Gl3nW00d@$3curE$FTP!
```
---

## 📂 Step 2: Navigate to Value Set Directory

```bash
cd /Temp
cd Mounika
cd "Value Set 2026"
```

### Verify location

```bash
pwd
```

Expected:

```
/Temp/Mounika/Value Set 2026
```

---

## 📄 Step 3: List Available Value Set Files

```bash
ls -lth
```

Files include:

* `*_unique_vs_*.xlsx.zip`  → **Value Set OIDs (Primary)**
* `*_cms_*.xlsx.zip`        → Code mappings
* `*_category_*.xlsx.zip`   → Measure categories

---

## 💻 Step 4: Set Local Download Directory

```bash
lpwd
lcd /home/software/Documents
```

---

## ⬇️ Step 5: Download All Value Set ZIP Files

```bash
mget *.zip
```

✔ Downloads all Value Set–related ZIP files
✔ Safe to re-run if needed

---

## 🚪 Step 6: Exit SFTP

```bash
bye
```

---

## 📁 Step 7: Organize Files Locally

### Create a dedicated folder

```bash
mkdir -p /home/software/Documents/value_set_2026
```

### Move downloaded ZIP files

```bash
cd /home/software/Documents
mv *.xlsx.zip value_set_2026/
```

### Verify

```bash
ls -lh value_set_2026
```

---

## 📦 Step 8: Extract ZIP Files

```bash
cd value_set_2026
unzip '*.zip'
```

✔ All Excel files (`.xlsx`) will be extracted in the same directory

---

## 📊 Step 9: Identify Primary Value Set OID Files

These files contain **unique Value Set OIDs** and must be processed first:

```text
ec_only_unique_vs_20250508.xlsx
hospip_only_unique_vs_20250508.xlsx
hospop_only_unique_vs_20250508.xlsx
ec_hospip_hospop_unique_vs_20250508.xlsx
```

---
