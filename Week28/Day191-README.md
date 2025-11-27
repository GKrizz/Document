
# 📅 Day 191 — November 25, 2025

## 🚨 AWV Questionnaire XML – 404 Error Investigation

**Environment:** `dev2.glaceemr.com`
**Issue:** Backend failing to load XML template (`AWVQuestionnaire.xml`) for D2DesktopNew

---

# 🧩 1. Problem Summary

When calling:

```
curl -k "https://dev2.glaceemr.com:444/D2DesktopNew/jsp/chart/LeafTool/AWVQuestionnaire.xml"
```

Tomcat returns:

```
HTTP Status 404 – Not Found
Message: /D2DesktopNew/jsp/chart/LeafTool/AWVQuestionnaire.xml
```

**NSLookup:**

```
dev2.glaceemr.com → 172.16.24.24
```

Conclusion so far:

> 🔴 The file isn’t found **because the entire D2DesktopNew webapp is not deployed** on the Tomcat serving port **444**.

---

# 🔍 2. Investigation Details

## **2.1 – Check if D2DesktopNew exists under Tomcat 9.0.19**

```
ls -l /usr/share/tomcat/apache-tomcat-9.0.19/webapps
# → No such file or directory
```

```
find /usr/share/tomcat/apache-tomcat-9.0.19 -name "AWVQuestionnaire.xml"
# → Nothing
```

👉 **Tomcat 9.0.19 is not the running instance.**

---

## **2.2 – Actual running Tomcat**

```
/usr/share/tomcat/apache-tomcat-9.0.12
```

```
ls -l /usr/share/tomcat/apache-tomcat-9.0.12/webapps
→ Only ROOT and Manager
→ No D2DesktopNew
```

This explains the 404.

---

## **2.3 – Tomcat Contexts Deployed**

Context files under:

```
/usr/share/tomcat/apache-tomcat-9.0.12/conf/Catalina/localhost/
```

Here we see:

```
-rwxrwxrwx ... D2DesktopNew.xml (Nov 19 08:09)
```

### Content:

```xml
<Context path="/D2DESKTOPNEW"
         docBase="/var/version/D2glacelegacy_stagenew"
         reloadable="true"
         privileged="true"
         crossContext="true">
```

🛑 **Important:** The deployed context path is:

```
/D2DESKTOPNEW  (ALL CAPS)
```

But the system is calling:

```
/D2DesktopNew  (CamelCase)
```

**Tomcat treats these as DIFFERENT context paths.**

---

# 🗂 3. Verification of Actual XML File on Disk

```
cd /var/version/D2glacelegacy_stagenew
ll jsp/chart/LeafTool/AWVQuestionnaire.xml
```

Result:

```
-rwxrwxrwx root root 128946 Jul 1 04:16 AWVQuestionnaire.xml
```

✔ The file **exists on disk**
❌ It is **not accessible via HTTPS**, because the webapp context is mismatched.

---

# 📡 4. Backend Also Fails via Spring Service

```
curl -k "https://dev2.glaceemr.com:450/glaceemr_backend_test/api/desktop/user/XMLGeneration/getXMLFile?...&accountId=d2desktopnew"
→ HTTP 404 Not Found
```

Again, backend calls SSO:

```
https://sso.glaceemr.com/TestSSOAccess?accountId=d2desktopnew
```

The SSO response includes:

```
"Glace_tomcat_URL": "http://dev2.glaceemr.com:444/D2DesktopNew"
```

🛑 This URL is wrong because:

**There is no context deployed at `/D2DesktopNew` on port 444.**

---

# 🧨 Root Cause

### **Mismatch between Tomcat context path and SSO configuration**

| Source                   | Value           |
| ------------------------ | --------------- |
| **Tomcat Context Path**  | `/D2DESKTOPNEW` |
| **SSO & backend expect** | `/D2DesktopNew` |
| **Result**               | 404 Not Found   |

Thus:

### 🔴 Tomcat receives request for:

```
/D2DesktopNew/jsp/.../AWVQuestionnaire.xml
```

### 🔥 But the deployed app is:

```
/D2DESKTOPNEW
```

Tomcat cannot route to the app → returns **404**, leading to:

```
java.io.FileNotFoundException: https://dev2.... D2DesktopNew/... .xml
```

---

# 🧪 5. Verification Commands (Recommended to Run)

### Valid path (uppercase):

```
curl -I "https://dev2.glaceemr.com:444/D2DESKTOPNEW/jsp/chart/LeafTool/AWVQuestionnaire.xml"
```

### Invalid path (current SSO config, CamelCase):

```
curl -I "https://dev2.glaceemr.com:444/D2DesktopNew/jsp/chart/LeafTool/AWVQuestionnaire.xml"
```

Expected:

* **FIRST** returns HTTP 200
* **SECOND** returns HTTP 404

---

# 🛠 6. Fix Options

### ✔ Fix SSO entry

Update:

```
"Glace_tomcat_URL": "http://dev2.glaceemr.com:444/D2DesktopNew"
```

To:

```
"Glace_tomcat_URL": "http://dev2.glaceemr.com:444/D2DESKTOPNEW"
```

### OR

### ✔ Fix Tomcat context file name

Rename:

```
/D2DESKTOPNEW  →  /D2DesktopNew
```

(by editing `D2DesktopNew.xml`)

---

# 📌 Summary

| Item                            | Result                                                           |
| ------------------------------- | ---------------------------------------------------------------- |
| File exists on disk             | ✔ `/var/version/D2glacelegacy_stagenew/.../AWVQuestionnaire.xml` |
| Webapp deployed?                | ✔ but at `/D2DESKTOPNEW`                                         |
| SSO config correct?             | ❌ points to `/D2DesktopNew`                                      |
| Tomcat 444 serving correct app? | ❌ cannot find D2DesktopNew                                       |
| Backend errors explained?       | ✔ FileNotFound due to wrong context path                         |

---
