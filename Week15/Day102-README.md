# Downloading and Uploading Files via SFTP

## Overview

This document explains how to connect to the remote SFTP server, navigate directories, and upload/download files.

---

## Steps

### 1. Connect to the SFTP server

```bash
sftp -oPort=8444 glenwood@ftp.glaceemr.com
```

**Password:** `Gl3nW00d@$3curE$FTP!`

### 2. Navigate to the remote directory

```bash
cd /Temp/Gobala_Krishnan
```

### 3. Check the remote working directory

```bash
pwd
```

### 4. List files in the current remote directory sorted by modification time (recent first)

```bash
ls -lt
```

### 5. Change local directory (optional)

Check local directory where files will be downloaded:

```bash
lpwd
```

Change local directory if needed:

```bash
lcd /path/to/local/directory
```

### 6. Download files from remote to local

```bash
get filename.txt
```

Example:

```bash
get ec_only_cms_20240502.xlsx
get csv_files.zip
```

### 7. Upload files from local to remote

```bash
put localfile.txt
```

---

## Example session

```bash
sftp -oPort=8444 glenwood@ftp.glaceemr.com
Password: Gl3nW00d@$3curE$FTP!

sftp> cd /Temp/Gobala_Krishnan
sftp> ls -lt
-rwxrwxrwx 1 glenwood users 8410662 Aug 28 02:17 ec_only_cms_20240502.xlsx
-rw-rw-r-- 1 glenwood users 2017433 Aug 28 02:16 csv_files.zip

sftp> lpwd
Local working directory: /home/software/Downloads/Documentation/1.MIPS

sftp> lcd /home/software/Downloads/Documentation/1.MIPS

sftp> get ec_only_cms_20240502.xlsx
sftp> get csv_files.zip

# Upload a file
sftp> put localfile.txt
```

---

Let me know if you want this saved as a file or formatted differently!
