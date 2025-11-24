# D2DesktopNew – Reference Guide

**Purpose:**
This document is a complete reference for D2DesktopNew deployment, backend, Tomcat/Catalina setup, and database queries related to LeafTool XML generation.

---

## 1. Tomcat / Catalina Contexts

**Catalina context files location:**

```bash
cd /usr/share/tomcat/apache-tomcat-9.0.12/conf/Catalina/localhost/
ls -lrth
```

**Example context files:**

* `D2DesktopNew.xml` – Main D2DesktopNew context
* `D2Desktop.xml` – Old / other desktop version
* `glaceemr_backend_test.xml` – Backend test context

**Typical XML inside a context file:**

```xml
<Context path="/D2DesktopNew" docBase="/var/version/D2glacelegacy_stagenew" reloadable="true">
    <Parameter name="accountID" value="d2desktopnew" override="false"/>
    <Parameter name="Version" value="v6.1.1" override="false"/>
</Context>
```

**Notes:**

* `path` = URL path for Tomcat
* `docBase` = folder where version JSPs, XMLs, and frontend files reside
* `reloadable="true"` allows hot reload of JSPs

---

## 2. Version Directory Structure

```bash
cd /var/version/D2glacelegacy_stagenew
ll jsp/chart/LeafTool/
```

Example files:

* `HeadacheQuestionnaireEaseTN.xml`
* Other LeafTool XML templates

**Notes:**

* All LeafTool templates live under `jsp/chart/LeafTool/`
* Version folder corresponds to `docBase` in Tomcat context

---

## 3. Backend & URLs

### 3.1 Backend API endpoints

Example XMLGeneration endpoint:

```http
GET https://dev2.glaceemr.com:450/glaceemr_backend_test/d2desktopnew/api/desktop/user/XMLGeneration/getXMLFile
```

Query parameters:

* `patientId=2975`
* `chartId=2088`
* `encounterId=2995`
* `templateId=4367`
* `formId=2779`
* `tabId=11`
* `accountId=d2desktopnew`

### 3.2 Frontend URLs

* `https://dev2.glaceemr.com:444/D2DesktopNew` – Main desktop
* `https://dev2.glaceemr.com:450/demonewdesktop` – GWT desktop
* `https://dev2.glaceemr.com:451/glacemobile` – Apache mobile

**Notes:**

* 404 or connection reset can happen if the Tomcat context is missing, misconfigured, or docBase path is wrong.

---

## 4. Database Queries

**LeafTool / XML Template Queries:**

1. Check Leaf Library by templateId:

```sql
SELECT * 
FROM leaf_library 
WHERE leaf_library_soaptemplate_id = 4367;
```

2. Find XML version for a template:

```sql
SELECT * 
FROM leaf_xml_version 
WHERE leaf_xml_version_xmlurl ILIKE '%HeadacheQuestionnaireEaseTN%';
```

```sql
SELECT * 
FROM leaf_xml_version 
WHERE leaf_xml_version_version LIKE '%HeadacheQuestionnaireEaseTN%';
```

3. Check patient tab data:

```sql
SELECT *
FROM leaf_tab_patient
WHERE leaf_tab_patient_encounter_id   = 154152
  AND leaf_tab_patient_tab_library_id = 4367
  AND leaf_tab_patient_isactive       = 't';
```

4. Join version and XML tables:

```sql
SELECT 
    v.leaf_xml_version_id,
    v.leaf_xml_version_xmlurl,
    v.leaf_xml_version_version
FROM leaf_xml_version v
JOIN leaf_version lv
      ON lv.leaf_version_xml_version_id = v.leaf_xml_version_id
WHERE lv.leaf_version_tab_id = 4367;
```

5. Verify tab library:

```sql
SELECT * 
FROM tab_library 
WHERE tab_library_id = 4367;
```

6. Alternate XML lookup:

```sql
SELECT v.*
FROM leaf_xml_version v
LEFT JOIN leaf_version lv
       ON lv.leaf_version_xml_version_id = v.leaf_xml_version_id
WHERE lv.leaf_version_tab_id = 1835;
```

---

## 5. Testing / Troubleshooting

### 5.1 Check Tomcat Ports

```bash
sudo lsof -i :8005   # Catalina shutdown port
sudo lsof -i :8080   # HTTP
sudo lsof -i :8443   # HTTPS
```

### 5.2 Restart Tomcat

```bash
cd /usr/share/tomcat/apache-tomcat-9.0.12/bin/
sudo ./shutdown.sh
sudo ./startup.sh
```

### 5.3 Test Endpoint

```bash
curl -I "https://dev2.glaceemr.com:444/D2DesktopNew/jsp/chart/LeafTool/HeadacheQuestionnaireEaseTN.xml"
```

**Notes:**

* `404` = context misconfigured or file missing
* `Connection reset by peer` = backend server closed connection (firewall, Tomcat crash, SSL issues)

---


## 7. Access Flow – From Tomcat to LeafTool XML

This section documents the exact steps to locate and verify LeafTool XML templates for `D2DesktopNew`.

### 7.1 Step 1: Go to Tomcat context folder

```bash
cd /usr/share/tomcat/apache-tomcat-9.0.12/conf/Catalina/localhost/
ls -lrth
```

* Lists all deployed context XMLs for Catalina.
* Look specifically for `D2DesktopNew.xml`.

### 7.2 Step 2: View context XML

```bash
cat D2DesktopNew.xml
```

* Confirms the `docBase` (version folder) for `D2DesktopNew`.
* Example content:

```xml
<Context path="/D2DesktopNew" docBase="/var/version/D2glacelegacy_stagenew" reloadable="true">
    <Parameter name="accountID" value="d2desktopnew" override="false"/>
    <Parameter name="Version" value="v6.1.1" override="false"/>
</Context>
```

### 7.3 Step 3: Go to version folder

```bash
cd /var/version/D2glacelegacy_stagenew
```

* This is the `docBase` defined in the context XML.

### 7.4 Step 4: Locate the LeafTool XML

```bash
ll jsp/chart/LeafTool/HeadacheQuestionnaireEaseTN.xml
```

* Lists the specific template used by the API or frontend.
* Make sure the file exists and has proper permissions.

---

### 7.6 Step 5: Edit / View Another LeafTool XML (`Plan.xml`)

```bash
vi /var/version/D2glacelegacy_stagenew/jsp/chart/LeafTool/Plan.xml
```

* Use `vi` to view or edit the `Plan.xml` template.
* This file is in the same LeafTool folder as `HeadacheQuestionnaireEaseTN.xml`.

**Folder context:**

```bash
/var/version/D2glacelegacy_stagenew/jsp/chart/LeafTool/
├── HeadacheQuestionnaireEaseTN.xml
├── Plan.xml
└── (other templates)
```

