# ✅ Day 3

---

### 🏢 **Company Commit Workflow**

| Commit Type            | Days           |
| ---------------------- | -------------- |
| **Beta Commit**        | Mon, Tue, Thu  |
| **Beta Stable Commit** | Thursday       |
| **Stable Commit**      | Next Wednesday |

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

### 📁 **1. Import & Configure Legacy Project (`glacelegacy`)**

#### 🔗 Clone from Git

1. Go to: [http://cs.glaceemr.com](http://cs.glaceemr.com)
2. Navigate to: **Projects > Project List**
3. Search and select the required project
4. Copy the **SSH URL**

#### 📥 Import into Eclipse

1. Open **Eclipse**
2. `Right-click` in the Project Explorer → `Import`
3. Select: `Git > Projects from Git` → `Clone URI`
4. Paste the SSH URL
5. Select destination folder
6. Choose: `Import as General Project`
7. Click `Finish`

#### 🔧 Configure Project Files

* **webfarm.xml**

  ```xml
  <context-path>GlaceStage</context-path>
  ```

  Replace all IP addresses with your local/stage IP.

* **DBFarm.xml**

  ```xml
  <context-path>GlaceStage</context-path>
  ```

  * Configure database access using your local IP.

#### ⚙ Tomcat Configuration

1. Navigate to:

   ```
   tomcat9/conf/Catalina/localhost
   ```

2. Open `GlaceStage.xml`

3. Update project path:

   * Right-click project → `Properties` → `Location`
   * Copy full path and update it in the XML file

#### 🏗 Build Project with Ant

1. Right-click `build.xml`
2. Choose: `Run As > Ant Build`
3. Ensure successful build

#### 🚀 Start Tomcat Server

```bash
cd tomcat9/bin
sudo ./startup.sh
```

#### 🌐 Check Output

Open in browser:

```
http://localhost:8080/
```

✅ **Legacy project is running successfully**

> ⚠️ *Avoid editing Apache files while Tomcat is running.*

#### 🛑 Stop Tomcat Server

```bash
./shutdown.sh
```

---

### 🧱 **2. Backend Project Setup (`glaceemr`) – Maven**

#### ⚙ Convert to Maven

1. Right-click project → `Configure` → `Convert to Maven Project`

#### 🔧 Update Configuration Files

* `applicationContext.xml`
* `pom.xml`
* `security.xml`
* `application.properties`

> 🔧 Also set annotation processing directory:
>
> * Right-click → `Build Path` → `Configure Build Path`
> * Go to: `Java Compiler` → `Annotation Processing`
> * Set:
>
> ```
> target/generated-sources
> ```

#### 🔨 Build with Maven

1. Right-click project → `Run As` → `Maven Clean`
2. Then → `Run As` → `Maven Install`

#### 🚀 Deploy WAR File

1. Right-click project → `Run on Server`
2. Choose **Tomcat**, set path → Click `Finish`

Open in browser:

```
http://192.168.2.241/glaceemr_backend/
```

Check Output:

```text
Welcome To Glenwood Services !
IP : 192.168.2.241
```

✅ **Backend project deployed successfully**

---

### 🎨 **3. UI Project (`glaceemr_ui`) – GWT**

#### 📌 Entry Points

* `glaceemr.html`
* `glaceemr.java`

#### 🧪 Run GWT in Dev Mode (Jetty)

1. Allocate **6 GB RAM**
2. Bind local IP
3. Execute:

   * `Maven Clean`
   * `Maven Install`
   * Right-click → `Run As` → `GWT Dev Mode (Jetty)`

#### 🌐 Check Development Output

```
Compiling glaceemr...
```

#### 🌐 Open in Browser

Options to test:

* **Main URL**

  ```
  http://192.168.2.241/glaceemr.html
  ```

* **Jetty Dev Mode**

  ```
  http://192.168.2.241:8888/glaceemr.html
  ```

* **Full URL with Parameters**

  ```
  http://192.168.2.241/glaceemr.html?homeParam=true&userId=1&loginId=2&contextPath=glace&dbn=glace&sprToken=ZGVmb2RvY3RvcjpmNDJlZGZiNjNlM2I5ZDM1M2QxZTIwZDZhMDY3NDg0MGNjMjNlZDY5NDMxZDM5MzcxODc2YTE4YTE3OGY2MTJhN2VjMWRlMDQwYTQxYWNhZDdjYmMzY2M5ZGEwMDY5Y2Q4YmI4ZTljMjRiNTg2Y2FmODlhNWExNGQyNzA2ZWFkYg==&localSharedPath=/home/abisheik/Documents/shared&springurl=http://localhost:9080/glaceemr_backend&taskIntegration=true&tomcaturl=http://192.168.2.241:8080/GlaceStage&userName=Demodoctor&demoFlag=0
  ```

* **Default Localhost (Tomcat)**

  ```
  http://localhost:8080/
  ```

✅ **UI displayed successfully in browser**
