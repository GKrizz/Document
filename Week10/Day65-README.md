# **SFTP Session Log Summary**

#### **Remote Directory:** `/Temp/Gobal`

#### **Local Directory:** `/home/software/Documents/maureen_CDA_files`

---

1. **Change to Remote Directory**

```bash
sftp> cd /Temp/Gobal
```

* Navigated to the target remote directory containing CDA files.

2. **List Remote Files**

```bash
sftp> ls -lth
```

* Displays remote directory contents with file details, sorted by modification time.

  ```
  -rw-r----- ? 1026 100 564K Jul 22 15:33 OUT-07-22-2025-04-55-46-293-432.cda
  ```

3. **Check Remote Working Directory**

```bash
sftp> pwd
```

* Output:

  ```
  Remote working directory: /Temp/Gobal
  ```

4. **Check Local Shell Directory**

```bash
sftp> !pwd
```

* Initial shell directory:

  ```
  /home/software/Downloads/tomcat9/bin
  ```

5. **Change Local Directory for Downloads**

```bash
sftp> lcd '/home/software/Documents/maureen_CDA_files'
```

6. **Verify Local Directory Change**

```bash
sftp> !pwd
```

* Confirmed:

  ```
  /home/software/Documents/maureen_CDA_files
  ```

---

### **File Transfers**

#### 1. Download First CDA File

```bash
sftp> get OUT-07-22-2025-04-55-46-293-432.cda
```

* Status:

  ```
  100% 564KB 72.3KB/s   00:07
  ```

#### 2. List Remote Files Again

```bash
sftp> ls -lrth
```

* Another file appears:

  ```
  -rw-r----- ? 1026 100 1.4M Jul 22 15:43 OUT-07-22-2025-06-10-21-699-432.cda
  ```

#### 3. Download Second CDA File

```bash
sftp> get OUT-07-22-2025-06-10-21-699-432.cda
```

* Status:

  ```
  100% 1460KB 182.4KB/s   00:08
  ```

---

### **Session Termination**

* After the second download, the session ends unexpectedly:

  ```
  client_loop: send disconnect: Broken pipe
  Connection closed.
  ```

**Note:** The broken pipe likely indicates a timeout or network issue after transfer completion. Since both files transferred successfully, no retransmission is necessary unless further errors are found in the downloaded files.

---
