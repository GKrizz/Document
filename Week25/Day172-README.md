# 📅 **Day 171 – November 5, 2025 (Wednesday)**

## 📝 **Summary**

Reviewed and addressed **Amazon CodeGuru** findings across multiple service implementations, focusing on **string encoding**, **internationalization**, and **safe calendar usage**.
Most `ObjectFactory.java` issues were verified as **false positives** for internationalization and required **no code changes**.
Encoding consistency updates continued across the **PaymentTransactionExecutionServiceImpl** and **PortalPaymentsServiceImpl** classes, ensuring explicit **UTF-8 usage**.

---

## 📂 **Files Reviewed / Updated**

| File                                            | # Findings        | Key Actions                                                                                                                                                                      |
| ----------------------------------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ObjectFactory.java**                          | 58                | Verified all warnings on “improper use of locals prevent internationalization.” All reviewed → **No code change required**.                                                      |
| **ImmunizationServiceImpl.java**                | 16                | Reviewed repetitive patterns and `Calendar.getInstance` usage. All instances confirmed safe → **No changes required**.                                                           |
| **PaymentTransactionExecutionServiceImpl.java** | 6                 | Updated `InputStreamReader` and `String.getBytes()` calls to explicitly use **UTF-8**; verified `Calendar.getInstance` usage as safe. ✅ *Completed*                              |
| **PortalPaymentsServiceImpl.java**              | 14 + code segment | Replaced all `String.getBytes()` calls with **UTF-8** encoding and corrected one `==` string comparison. Added Base64 → Hex encoding with explicit charset safety. ✅ *Completed* |

---

## ⚙️ **Detailed Review Notes**

### 🧩 **ObjectFactory.java**

* **58 findings (lines 27 – 69)**
  All warnings reported “Improper use of locals prevent internationalization.”
  ✔ Each instance reviewed and confirmed *safe for current context* (local literals, not user-visible strings).
  **→ No modifications required.**

---

### 💉 **ImmunizationServiceImpl.java**

| Category                 | Description                                 | Status                                            |
| ------------------------ | ------------------------------------------- | ------------------------------------------------- |
| `Calendar.getInstance`   | Found in multiple methods (lines 110–1802)  | ✅ Safe; no timezone-sensitive logic affected      |
| Duplicate code fragments | Sections 279–368, 323–330, 187–199, 279–286 | 🔍 Review noted but not critical to current scope |
| Overall                  | Code functionally consistent and safe       | ✅ No changes required                             |

---

### 💳 **PaymentTransactionExecutionServiceImpl.java**

| Line | Issue                  | Resolution           |
| ---- | ---------------------- | -------------------- |
| 495  | `Calendar.getInstance` | ✅ No change required |
| 452  | `InputStreamReader`    | ✅ Added UTF-8        |
| 477  | `InputStreamReader`    | ✅ Added UTF-8        |
| 445  | `String.getBytes()`    | ✅ Added UTF-8        |
| 477  | `String.getBytes()`    | ✅ Added UTF-8        |
| 452  | `InputStreamReader`    | ✅ Added UTF-8        |

**Result:** All encoding operations are now **UTF-8 compliant**.

---

### 💰 **PortalPaymentsServiceImpl.java**

| Line                     | Issue                   | Action                                                        |
| ------------------------ | ----------------------- | ------------------------------------------------------------- |
| 1648 – 3066              | `String.getBytes()`     | ✅ Replaced with UTF-8                                         |
| 3694                     | `==` string comparison  | ✅ Replaced with `.equals()`                                   |
| 2409  + additional block | Encoding of credentials | ✅ Added explicit UTF-8 in all Base64 encode/decode operations |

**Implemented Encoding Logic Example:**

```java
String passwordEncode = new String(Base64.encodeBase64(pName.getBytes(StandardCharsets.UTF_8)), StandardCharsets.UTF_8);
String password_hex = PaymentTransactionExecutionService.base64ToHex(passwordEncode);

String userNameEncode = new String(Base64.encodeBase64(uName.getBytes(StandardCharsets.UTF_8)), StandardCharsets.UTF_8);
String userName_hex = PaymentTransactionExecutionService.base64ToHex(userNameEncode);
```

✔ All string conversions now explicitly specify **UTF-8**, ensuring consistent cross-system behavior.

---
