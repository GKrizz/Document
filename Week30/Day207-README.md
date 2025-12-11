**ready-to-copy MASTER PROMPT**

✅ **Frontend:** React + Material UI
✅ **Backend:** Spring Boot (Java)
✅ **Database:** MySQL or SQLite
✅ **Platform:** LeetCode / HackerRank–like
✅ **Features:** Login, Signup, Admin Panel, Code Execution, Problems, Submissions

Paste this prompt directly into **Cursor / Claude / Antigravity** to generate the entire project.

---

# 🚀 **FULL PROJECT GENERATION PROMPT**

### *(React + Material UI + Spring Boot + Java + SQL/SQLite)*

Copy everything below:

---

## **📌 PROJECT TITLE:**

Build a full-stack coding challenge platform similar to **LeetCode / HackerRank / SkillRack** using **React + Material UI + Spring Boot + Java + SQL**.

---

# **📦 REQUIREMENTS — FRONTEND (React + Material UI)**

### **Frameworks & Libraries**

* React (Vite or CRA)
* Material UI (MUI v5)
* React Router DOM
* Axios
* Monaco Editor (for code editor)
* JWT handling (context or Redux)

### **Frontend Pages**

1. **Auth**

   * Login Page
   * Signup Page

2. **User UI**

   * Home Page
   * Problem List Page (filters: difficulty, tags)
   * Problem Details Page
   * Code Editor Page with:

     * Monaco Editor
     * Language selector (Java, C++, Python, JS)
     * Run button
     * Submit button
     * Output window

3. **Profile**

   * User info
   * List of solved problems
   * Submission history

4. **Admin Dashboard**

   * Add Problem
   * Edit Problem
   * Manage Problems
   * View Users
   * View Submissions

### **Frontend Modules to Generate**

* Auth context or Redux store
* ProtectedRoute components
* Axios interceptors for Authorization header
* Material UI theme + dark mode

---

# **🛠️ BACKEND REQUIREMENTS — SPRING BOOT + JAVA**

### **Spring Boot Modules**

* Spring Web
* Spring Security (JWT)
* Spring Data JPA
* Database driver: MySQL or SQLite
* Lombok
* Validation

---

## **Backend Entities**

### **User**

```
id
username
email
password (hashed)
role (USER/ADMIN)
createdAt
```

### **Problem**

```
id
title
description
difficulty
constraints
samples (JSON)
hiddenTestcases (JSON)
tags
createdBy
```

### **Submission**

```
id
userId
problemId
language
code
status
runtime
output
timestamp
```

---

## **Backend Features**

### **1. Authentication (JWT)**

* Register
* Login
* Logout
* Role-based access (`ADMIN`, `USER`)
* Password hashing using BCrypt

### **2. Problem Management API**

Admin APIs:

* Create problem
* Edit problem
* Delete problem
* Get all problems
  User APIs:
* Get problem by ID

### **3. Code Execution API**

Create an endpoint:

```
POST /api/run
{
  "language": "java/cpp/python/js",
  "code": "...",
  "input": "..."
}
```

Backend must:

* Use a Docker Sandbox OR
* Use Java ProcessBuilder for simple execution
* Enforce:

  * timeouts
  * memory limits
  * safe restricted execution

Return:

```
stdout
stderr
exitCode
status
time
```

### **4. Submission Processing**

* Run against hidden test cases
* Store submission
* Mark:

  * ACCEPTED
  * WRONG_ANSWER
  * TLE
  * RUNTIME_ERROR
  * COMPILE_ERROR

### **5. Leaderboard (Optional)**

---

# **🗄 Database Requirements**

Support either:

### **Option 1 — MySQL**

* Use Spring Boot MySQL driver
* application.properties includes:

```
spring.datasource.url=jdbc:mysql://localhost:3306/coding_platform
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
```

### **Option 2 — SQLite**

* Use `xerial/sqlite-jdbc`
* application.properties:

```
spring.datasource.url=jdbc:sqlite:database.db
spring.jpa.hibernate.ddl-auto=update
```

---

# **📁 BACKEND PACKAGE STRUCTURE**

```
/src/main/java/com/app
   /config
   /controller
   /service
   /repository
   /model
   /dto
   /security
```

---

# **📁 FRONTEND FOLDER STRUCTURE**

```
/src
  /components
  /pages
  /layouts
  /api
  /context
  /hooks
  /theme
```

---

# **🎯 DELIVERABLES**

Generate **fully working code** for:

### **Frontend**

* All pages + components
* Working code editor page
* API integrations
* MUI themed UI
* Admin dashboard
* Routing + auth protection

### **Backend**

* Full Spring Boot project
* All models, controllers, services, repositories
* Authentication + JWT security
* Problem management APIs
* Submission APIs
* Code execution engine
* SQL database schema

### **Extras**

* A script to create the first admin user
* Dockerfile for backend
* Instructions to run both frontend & backend

---

# **👉 FINAL INSTRUCTION**

Now **generate the complete full-stack application** (frontend + backend + code execution engine + SQL schema + setup instructions) using the above specifications.

---
