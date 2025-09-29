# 🔍 Amazon CodeGuru Findings

**Source:** `aws Findings_Final.xls`  
**Total Issues Identified:** `577`

### 🚨 Issues by Category

| Issue Type / CWE                                           | Count | Description |
|------------------------------------------------------------|-------|-------------|
| **Catching Generic Exceptions**                            | 8     | Use of broad exception types (e.g., `Exception`, `Throwable`) should be avoided for better error control. |
| **CWE-117, CWE-93 – Log Injection**                        | 2     | User input logged without sanitization, potentially allowing injection into logs. |
| **CWE-20 – Improper Input Validation**                     | 4     | Input not properly validated, risking unsafe operations. |
| **CWE-252, CWE-754 – Missing Return Value Checks**         | 3     | Return values from methods are ignored, possibly missing error handling. |
| **CWE-327, 328, 326, 208, 1240 – Weak Cryptography**       | 9     | Use of outdated or insecure cryptographic algorithms (e.g., MD5, SHA-1). |
| **CWE-352 – Cross-Site Request Forgery (CSRF)**            | 8     | CSRF protections missing on state-changing endpoints. |
| **CWE-390 – Catching Exceptions Without Action**           | 112   | Caught exceptions are not logged or properly handled. |
| **CWE-400, CWE-664 – Resource Exhaustion / Unsafe Dependencies** | 100   | Potential for denial-of-service or race conditions due to improper resource handling. |
| **CWE-611 – XML External Entity (XXE) Injection**          | 73    | XML parsers are not securely configured, allowing XXE attacks. |
| **CWE-611 – Insecure DocumentBuilder Configuration**       | 45    | `DocumentBuilderFactory` configured insecurely, enabling XXE or similar risks. |
| **CWE-755 – Exposure of Stack Traces**                     | 71    | Stack traces exposed to end-users, leaking implementation details. |
| **CWE-798 – Hardcoded Credentials**                        | 36    | Credentials or secrets found hardcoded in source code. |
| **Use of `SimpleDateFormat` in Multi-threaded Contexts**   | 96    | `SimpleDateFormat` is not thread-safe and may cause unpredictable results in concurrent applications. |
| **Miscellaneous Logic / Pattern Issues**                   | 10    | General best-practice violations or minor security/code quality issues. |

---

## 📄 Manual Code Reviews

**Source:** `Code reviews.docx`  
**Total Issues Identified:** `2,018`

---


