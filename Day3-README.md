## ✅ Day 3

---


### 🏢 **Company Commit Workflow**

| Commit Type        | Days           |
| ------------------ | -------------- |
| Beta Commit        | Mon, Tue, Thu  |
| Beta Stable Commit | Thursday       |
| Stable Commit      | Next Wednesday |

---

### 🛠️ **Environment Setup**

* ✅ Installed **Eclipse IDE** (Version 14)
* ✅ Installed **Java 8** (Required for **GWT – Google Web Toolkit**)
* ✅ Cloned repositories:

  ```
  1. glacelegacy    → Legacy project (GWT-based)
  2. glaceemr_ui    → Front-end/UI project
  3. glaceemr       → Spring-based backend project
  ```

---

### 📁 **1. Import & Configure Legacy Project**

#### 🔗 Clone from Git

1. Go to: [http://cs.glaceemr.com](http://cs.glaceemr.com)
2. Navigate to: **Projects > Project List**
3. Search and select your project
4. Copy the **SSH URL**

#### 📥 Import into Eclipse

1. Open **Eclipse**
2. `Right-click` in the Project Explorer → `Import`
3. Choose: `Git > Projects from Git` → `Clone URI`
4. Paste the SSH URL
5. Select a destination folder
6. Choose: `Import as General Project`
7. Click `Finish`

---

#### 🔧 Configure the Project

* **`webfarm.xml`**

  
  * Set local IP and context path 

  * Set:

    ```xml
    <context-path>GlaceStage</context-path>
    ```
  * Replace all IP addresses with your stage/local IP

* **`DBFarm.xml`**

  * Configure database access using local IP

  * Ensure:

    ```xml
    <context-path>GlaceStage</context-path>
    ```

#### ⚙ Tomcat Configuration

1. Go to:

   ```
   tomcat9/conf/Catalina/localhost
   ```
2. Open `GlaceStage.xml`
3. Update project path:

   * Right-click project in Eclipse → `Properties` → `Location`
   * Copy full path and replace it in the XML

#### 🏗 Build the Project (Ant)

1. Right-click `build.xml`
2. Select: `Run As > Ant Build`
3. Verify build is successful

#### 🚀 Start Tomcat Server

```bash
cd tomcat9/bin
sudo ./startup.sh
```

---

#### 🌐 Check Output

Open in browser:

```
http://localhost:8080/
```

✅ **Legacy project running successfully**

> ⚠️ *Note: Avoid editing Apache files while Tomcat is running.*

#### 🛑 Stop Tomcat Server

```bash
./shutdown.sh
```

---

### 🧱 **2. Setup GlaceEMR Project (`glaceemr`) – Maven**

#### ⚙ Convert to Maven

1. Right-click the project → `Configure` → `Convert to Maven Project`

#### 🔧 Update Configuration Files

* `applicationContext.xml`
* `pom.xml`
* `security.xml`
* `application.properties`

#### 🔨 Build with Maven

1. Right-click the project
2. Run:

   * `Maven Clean`
   * `Maven Install`

#### 🚀 Deploy WAR File

1. Right-click the project → `Run on Server`
2. Check output:

```text
Welcome to glenwoodsystems
IP: 127.0.0.1
```

✅ **Backend project deployed successfully**

---

### 🎨 **3. UI Project (`glaceemr_ui`) – GWT**

#### 📌 Entry Points

* `glaceemr.html`
* `glaceemr.java`

#### 🧪 Run GWT in Dev Mode (Jetty)

1. Allocate RAM: 6 GB
2. Bind local IP
3. Run:

   * `Maven Clean`
   * `Maven Install`
   * Start in GWT Dev Mode

#### 🌐 Check Development Output

```
Compiling glaceemr...
```
#### 🌐 Check Output

Open in browser:

```
http://localhost:8080/
```
✅ **UI displayed in browser**


