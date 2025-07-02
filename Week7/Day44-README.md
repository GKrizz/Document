# 🛠️ GlaceEMR Project Setup Guide

**Date:** Day 44 – July 01, 2025 (Tuesday) <br>
**Tasks:** Configure Legacy, Backend, and UI Projects

---

## 📁 1. Import & Configure Legacy Project (`glacelegacy`)

### 🔗 Clone from Git

1. Visit: [http://cs.glaceemr.com](http://cs.glaceemr.com)
2. Navigate to: `Projects > Project List`
3. Search and select your project
4. Copy the **SSH URL**

### 📥 Import into Eclipse

1. Open **Eclipse**
2. Right-click in the **Project Explorer** → `Import`
3. Select: `Git > Projects from Git` → `Clone URI`
4. Paste the **SSH URL**
5. Set your destination folder
6. Choose: `Import as General Project`
7. Click **Finish**

### 🔧 Configure Project Files

#### `webfarm.xml`

```xml
<context-path>GlaceMaster</context-path>
```

> Replace all IP addresses with your **local or staging IP**.

#### `DBFarm.xml`

```xml
<context-path>GlaceMaster</context-path>
```

> Configure database access using your **local IP**.

---

### ⚙ Tomcat Configuration

1. Navigate to:

   ```
   tomcat9/conf/Catalina/localhost
   ```

2. Open `GlaceMaster.xml`

3. Update the project path:

   * Right-click project → `Properties` → `Location`
   * Copy the full path and update in the XML file

---

### 🏗 Build Project with Ant

1. Right-click `build.xml`
2. Choose: `Run As > Ant Build`
3. Ensure the build completes successfully

---

### 🚀 Start Tomcat Server

```bash
cd tomcat9/bin
sudo ./startup.sh
```

### 🌐 Access the Application

Open your browser and go to:

```
http://localhost:8080/
```

✅ **Legacy project is running successfully**

> ⚠️ **Avoid editing Apache files while Tomcat is running.**

### 🛑 Stop Tomcat Server

```bash
./shutdown.sh
```

---

## 🧱 2. Backend Project Setup (`glaceemr`) – Maven

### ⚙ Convert to Maven Project

1. Right-click the project → `Configure` → `Convert to Maven Project`

### 🔧 Update Configuration Files

* `applicationContext.xml`
* `pom.xml`
* `security.xml`
* `application.properties`

#### 🔁 Annotation Processing Setup

1. Right-click the project → `Build Path` → `Configure Build Path`
2. Navigate to: `Java Compiler > Annotation Processing`
3. Set the directory:

   ```
   target/generated-sources
   ```

---

### 🔨 Build with Maven

1. Right-click project → `Run As` → `Maven Clean`
2. Then → `Run As` → `Maven Install`

---

### 🚀 Deploy WAR File

1. Right-click project → `Run on Server`
2. Choose **Tomcat**, set path → Click `Finish`

#### 🔍 Open in Browser

```
http://192.168.2.241/glaceemr_backend/
```

Expected Output:

```text
Welcome To Glenwood Services !
IP : 192.168.2.241
```

✅ **Backend project deployed successfully**

---

## 🎨 3. UI Project (`glaceemr_ui`) – GWT

### 📌 Entry Points

* `glaceemr.html`
* `glaceemr.java`

---

### 🧪 Run GWT in Dev Mode (Jetty)

1. Allocate **8 GB RAM**
2. Bind to your **local IP**
3. Set WAR file location
4. Run:

   * `Maven Clean`
   * `Maven Install`
   * Right-click → `Run As` → `GWT Dev Mode (Jetty)`

---

### 🌐 Development Output

```
Compiling glaceemr...
```

---

### 🔍 Test in Browser

#### Main URL:

```
http://192.168.2.241/glaceemr.html
```

#### Jetty Dev Mode:

```
http://192.168.2.241:8888/glaceemr.html
```

#### Default Localhost (Tomcat):

```
http://localhost:8080/
```

✅ **UI displayed successfully in browser**

---

> For any environment-specific issues or build errors, refer to the internal documentation or consult your team lead.

---

