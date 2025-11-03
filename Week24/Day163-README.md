# Project Update — Day 163 (October 28, 2025, Tuesday)

## MODULE: EmailDocumentServiceImpl.java

### Code Review & Refactoring Summary

| #  | Line(s)     | Issue / Observation                                       | Action / Note     | Status        |
| -- | ----------- | --------------------------------------------------------- | ----------------- | ------------- |
| 1  | 2618 → 2628 | Constructor without specified character encoding          | ✅ Fixed           | **Complete**  |
| 2  | 788         | Multiple exceptions found                                 | ⚠️ Investigate    | **Not Done**  |
| 3  | 2045 → 2048 | Directory creation not checked; missing return validation | Added `log.warn`  | **Complete**  |
| 4  | 1756        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 5  | 1769        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 6  | 1413        | —                                                         | ⚠️ Review pending | **Not Done**  |
| 7  | 645         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 8  | 646         | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 9  | 2001        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 10 | 497         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 11 | 2683        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 12 | 1841 → 1838 | `InputStreamReader` without charset                       | ✅ Added charset   | **Complete**  |
| 13 | 1774        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 14 | 2000        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 15 | 2509 → 2522 | `InputStreamReader(inputStream)` missing encoding         | ✅ Fixed           | **Complete**  |
| 16 | 2546        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 17 | 223         | GS-Tech Static                                            | ⚠️ Review pending | **Not Done**  |
| 18 | 787         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 19 | 2640 → 2639 | `InputStreamReader(inputStream)` missing encoding         | ✅ Fixed           | **Complete**  |
| 20 | 1999        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 21 | 376         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 22 | 2647        | `String.getBytes()` without charset                       | ✅ Already handled | **Completed** |
| 23 | 2684        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 24 | 370         | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 25 | 2648        | `String.getBytes()` without charset                       | ✅ Already handled | **Completed** |
| 26 | 1426        | Missing `.lower()` in LIKE comparison                     | ✅ Added           | **Complete**  |
| 27 | 901         | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 28 | 794         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 29 | 2229        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 30 | 1853        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 31 | 2627        | —                                                         | ✅ Already handled | **Completed** |
| 32 | 907         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 33 | 504         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 34 | 154         | Improper locale use (i18n issue)                          | ⚠️ Fix required   | **Not Done**  |
| 35 | 1764        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 36 | 1979        | Improper locale use (i18n issue)                          | ⚠️ Fix required   | **Not Done**  |
| 37 | 1092        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 38 | 1691        | Improper locale use (i18n issue)                          | ⚠️ Fix required   | **Not Done**  |
| 39 | 2626        | —                                                         | ✅ Already handled | **Completed** |
| 40 | 369         | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 41 | 1312        | —                                                         | ⚠️ Pending review | **Not Done**  |
| 42 | 2362        | —                                                         | ⚠️ Pending review | **Not Done**  |
| 43 | 1098        | Locale or TimeZone usage                                  | ⚠️ Review needed  | **Not Done**  |
| 44 | 215         | Improper locale use (i18n issue)                          | ⚠️ Fix required   | **Not Done**  |
| 45 | 1764        | Exceptions                                                | ⚠️ Investigate    | **Not Done**  |
| 46 | 151         | Improper locale use (i18n issue)                          | ⚠️ Fix required   | **Not Done**  |
| 47 | 1280        | —                                                         | ⚠️ Review pending | **Not Done**  |

---

### API References (Testing / Verification)

#### 1. **Inbox XML File View**

```bash
GET
http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/EmailDocument/getInboxMimeFileViewForXML
?fileName=DOC-211062022121936_315.xml
&mimeFilePath=/documents/unprocessed/DOC-211062022121936_315.xml
&accountId=glace
&userid=1
&versionPath=/var/version/GlaceR15/
&accessCode=-1
```

#### 2. **Inbox Body Content**

```bash
GET
http://192.168.2.241/glaceemr_backend/glace/api/desktop/user/EmailDocument/getInboxBodyContent
?fileName=%2Flog%2FCDAFiles%2Fcda_Outbox%2FOUT-01-31-2025-07-38-11-461-Encounter-151310.xml
&id=1755
```

---

### Related Methods

| Method                                                  | Description / Endpoint                                                          |
| ------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `public List<MailDetailsBean> searchByName(...)`        | `/searchByName`                                                                 |
| `public JSONArray getpatientDetailsforReferral(...)`    | `/getpatientDetailsforReferral`                                                 |
| `private String directPreview(...)`                     | `/getInboxMimeFileViewForXML` (E-Direct → `DOC-211062022121936_315.xml → View`) |
| `public JSONArray getInboxMimeFileViewForZip(...)`      | `/getInboxMimeFileViewForZip`                                                   |
| `public String getInboxMimeFileViewForZipDownload(...)` | `/getInboxMimeFileViewForZipDownload`                                           |

---
