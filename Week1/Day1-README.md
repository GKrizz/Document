

# Day 1 Tasks

## Task 1: Create a Login and Logout Page using HTML, CSS, and JS

* **Objective:** Design and implement a simple login page that includes HTML for structure, CSS for styling, and JavaScript for handling login/logout functionality.
* **Technologies Used:** HTML, CSS, JavaScript.

---

## Task 2: Learn JSP and Servlet

* **Objective:** Familiarize yourself with Java Server Pages (JSP) and Servlets by reading official documentation and tutorials.
* **Technologies Used:** JSP, Servlets.

---

# Setting Up Eclipse, Tomcat, and Java on Linux

## 1. Install Eclipse

1. Download Eclipse from the [official site](https://www.eclipse.org/downloads/).
2. Unzip the downloaded tarball and run the installer.
3. Optionally, create a launcher for easy access to Eclipse.

---

## 2. Install Tomcat

1. Download Tomcat from [Apache Tomcat](https://tomcat.apache.org/download-90.cgi).

2. Extract Tomcat to `/opt/tomcat`.

3. Set permissions:

   ```bash
   sudo chown -R $USER:$USER /opt/apache-tomcat-9.x.x
   ```

4. Start Tomcat:

   ```bash
   ./startup.sh
   ```

5. Access Tomcat via: `http://localhost:8080`.

---

## 3. Install Java (OpenJDK 8)

1. Install OpenJDK 8:

   ```bash
   sudo apt install openjdk-8-jdk
   ```

2. Set `JAVA_HOME` in `~/.bashrc`:

   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```

3. Apply the changes:

   ```bash
   source ~/.bashrc
   ```

4. Verify Java installation:

   ```bash
   java -version
   ```

---

## 4. Manage Shared Folders

1. Copy the shared folder to `/home/software/Documents/shared`.
2. Set permissions:

   ```bash
   sudo chmod 777 -Rf /home/software/Documents/shared
   ```

---

## 5. Mount Folder for External Access

1. Mount external drive:

   ```bash
   sudo mount /dev/sdb1 /mnt
   ```

2. Copy files to `/mnt/`.

---

## 6. Accessing Root User (sudo)

1. Switch to root user:

   ```bash
   sudo su
   ```

2. Grant permissions:

   ```bash
   chmod 777 -Rf /path/to/directory
   ```

---

## 7. Conclusion

You have successfully set up Eclipse, Tomcat, and Java, along with managing shared folders and mounting external drives.

---

# GWT Glace Development Environment Setup

**Date:** 2025-05-19
**Author:** \[Your Name]
**Version:** 1.0

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [System Requirements](#2-system-requirements)
3. [Software Installation](#3-software-installation)

   * 3.1 [Eclipse IDE](#31-eclipse-ide)
   * 3.2 [Apache2](#32-apache2)
4. [Eclipse Plugin Installation](#4-eclipse-plugin-installation)

   * 4.1 [GWT Plugin](#41-gwt-plugin)
   * 4.2 [Git Plugin (EGit)](#42-git-plugin-egit)
5. [Apache2 Configuration for Proxy](#5-apache2-configuration-for-proxy)
6. [Account Setup](#6-account-setup)

   * 6.1 [GitHub Account](#61-github-account)
   * 6.2 [cs.glaceemr.com Account Linking](#62-csglaceemrcom-account-linking)
7. [Eclipse Settings](#7-eclipse-settings)

   * 7.1 [SSH Key Generation](#71-ssh-key-generation)
   * 7.2 [Git Configuration](#72-git-configuration)
8. [Project Cloning from cs.glaceemr.com](#8-project-cloning-from-csglaceemrcom)
9. [Project Setup in Eclipse](#9-project-setup-in-eclipse)

   * 9.1 [Showing Git Views](#91-showing-git-views)
   * 9.2 [Configuring for Gerrit](#92-configuring-for-gerrit)
   * 9.3 [Making Project a Maven Project](#93-making-project-a-maven-project)
10. [Next Steps: Starting Your Work](#10-next-steps-starting-your-work)

---

## 1. Introduction

This document outlines the process of setting up a development environment to work with the GWT Glace project. Follow these steps to install the required software, configure Eclipse, and set up your workspace.

---

## 2. System Requirements

Ensure your system meets the following requirements:

* **Operating System:** Linux (Ubuntu 14.06 or later recommended)
* **RAM:** 4GB minimum
* **Processor:** Intel Core or equivalent
* **IDE:** Eclipse 4.4.2 or higher
* **Browser:** Firefox 16.0.1 or higher
* **Web Server:** Apache2 (latest version)
* **Online Accounts:**

  * GitHub ([https://github.com/](https://github.com/))
  * [cs.glaceemr.com](http://cs.glaceemr.com/)

---

## 3. Software Installation

### 3.1. Eclipse IDE

1. Download the Eclipse package from [Eclipse Downloads](https://www.eclipse.org/downloads/packages/).
2. Extract the downloaded `.tar.gz` file to `/opt/eclipse`.
3. Create a desktop shortcut or run Eclipse directly from the extracted folder.

### 3.2. Apache2

1. Open your terminal and update package lists:

   ```bash
   sudo apt update
   ```

2. Install Apache2:

   ```bash
   sudo apt install apache2
   ```

3. Verify the installation by visiting `http://localhost` in your browser. You should see the Apache2 default page.

---

## 4. Eclipse Plugin Installation

### 4.1. GWT Plugin

1. In Eclipse, go to **Help** > **Install New Software...**
2. Enter the following URL: `https://dl.google.com/eclipse/plugin/4.2`
3. Select **Google Plugin for Eclipse (Required)** and **SDKs**.
4. Follow the prompts to install and restart Eclipse.

### 4.2. Git Plugin (EGit)

1. In Eclipse, go to **Help** > **Eclipse Marketplace...**
2. Search for **EGit** and install **EGit - Git Team Provider**.
3. Restart Eclipse after installation.

---

## 5. Apache2 Configuration for Proxy

1. Navigate to Apache2 configuration directory:

   ```bash
   cd /etc/apache2/
   ```

2. Edit the `httpd.conf` file:

   ```bash
   sudo nano httpd.conf
   ```

3. Add the following proxy configurations:

   ```bash
   ProxyRequests on
   ProxyVia on
   ServerName localhost
   ProxyPass /dev http://dev1.glaceemr.com/dev
   ProxyPassreverse /dev http://dev1.glaceemr.com/dev
   ProxyPass / http://localhost:8888/
   ProxyPassreverse / http://localhost:8888/
   ```

4. Save and close the file. Then edit `apache2.conf`:

   ```bash
   sudo nano apache2.conf
   ```

5. Add the line:

   ```bash
   Include /etc/apache2/httpd.conf
   ```

6. Enable necessary modules:

   ```bash
   sudo a2enmod proxy proxy_http proxy_ajp rewrite deflate headers proxy_balancer proxy_connect proxy_html
   ```

7. Restart Apache2:

   ```bash
   sudo systemctl restart apache2
   ```

---

## 6. Account Setup

### 6.1. GitHub Account

1. Create an account on [GitHub](https://github.com/) using your Glenwood email.
2. Sign out of GitHub after account creation.

### 6.2. cs.glaceemr.com Account Linking

1. Visit [cs.glaceemr.com](http://cs.glaceemr.com/) and link your GitHub account.
2. Verify your email address as instructed in the verification email.

---

## 7. Eclipse Settings

### 7.1. SSH Key Generation

1. In Eclipse, go to **Window** > **Preferences**.
2. Search for **SSH2** and select **Key Management**.
3. Generate a new RSA key and save it securely.

### 7.2. Git Configuration

1. Go to **Window** > **Preferences** and search for **Git**.
2. Add your GitHub username and email under **user.name** and **user.email**.

---

## 8. Project Cloning from cs.glaceemr.com

1. Log in to [cs.glaceemr.com](http://cs.glaceemr.com/), add your SSH key, and copy the SSH URL of the project you wish to clone.
2. In Eclipse, go to **File** > **Import...** > **Projects from Git** and follow the steps to clone the repository


.

---

## 9. Project Setup in Eclipse

### 9.1. Showing Git Views

1. Go to **Window** > **Show View** > **Others...**.
2. Search for **Git** and select **Git Staging** and **Git Repositories**.

### 9.2. Configuring for Gerrit

1. Right-click your repository in **Git Repositories** and select **Gerrit Configuration**.

### 9.3. Making Project a Maven Project

1. Right-click your project and choose **Configure** > **Convert to Maven Project**.

---

## 10. Next Steps: Starting Your Work

Your GWT Glace development environment is ready! Start exploring the project, making changes, and committing them using Eclipse's Git integration.

---

# Database Setup 

---

## Prerequisites

* SSH access credentials for the remote server.
* PostgreSQL client installed on the remote server.
* Necessary permissions to access the target database.

---

## Steps

### 1. SSH into the Server

Connect to the remote server using SSH on the specified port.

```bash
ssh glace@192.168.2.3 -p24
```

* When prompted, enter the password: `glacenxt`

---

### 2. List All Available Databases

Once logged in, list all PostgreSQL databases to verify connection and check available databases.

```bash
psql -l
```

---

### 3. Connect to the `ciama_local` Database

Connect to the target database named `ciama_local`.

```bash
psql ciama_local
```

---

### 4. List All Tables in the Database

Inside the `ciama_local` database, list all tables to explore the schema.

```sql
\dt
```

---

### 5. Check Table Structure

View the detailed structure (columns, types, constraints) of the table `patient_registration`.

```sql
\d patient_registration
```

---

### 6. Enable Expanded Output Mode

Toggle expanded output mode for better readability of query results (especially useful for wide tables).

```sql
\x
```

---

### 7. Retrieve Sample Data

Fetch the first row of data from the `patient_registration` table as a sample.

```sql
SELECT * FROM patient_registration LIMIT 1;
```

---

## Notes

* Use `\q` to exit the `psql` interface.
* Replace `patient_registration` with any other table name to explore different tables.
* Always ensure secure handling of credentials and permissions.

---

Let me know if you want this formatted as Markdown or in any other style!




