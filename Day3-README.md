## ✅ Day 3

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

  * Set:

    ```xml
    <context-path>GlaceStage</context-path>
    ```
  * Replace all IP addresses with your stage/local IP

* **`DBFarm.xml`**

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

#### 🌐 Open in Browser

```url
http://localhost:8080/
```

---

### 🧱 **2. Setup GlaceEMR (Maven Project)**

#### ⚙ Convert and Configure

1. Right-click the project → `Configure` → `Convert to Maven Project`

2. Update the following files:

   * `applicationContext.xml`
   * `pom.xml`
   * `security.xml`
   * `application.properties`

#### 🔨 Build with Maven

1. Right-click the project
2. Run:

   * `Maven Clean`
   * `Maven Install`

