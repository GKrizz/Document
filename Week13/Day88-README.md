
Here’s a **flowchart** for `getMeasureInfo()` showing exactly how the **7-day check** works:

```
               ┌─────────────────────────────┐
               │ Start getMeasureInfo(...)    │
               └───────────────┬─────────────┘
                               │
                               ▼
           ┌─────────────────────────────────────┐
           │ Build JSON file path in shared folder│
           └───────────────────┬─────────────────┘
                               │
                ┌──────────────▼──────────────┐
                │ File exists AND is a file?  │
                └──────────────┬──────────────┘
                               │ Yes
                               ▼
         ┌────────────────────────────────────────┐
         │ Adjust permissions (read/write/exec)   │
         └───────────────────┬────────────────────┘
                             │
                ┌────────────▼─────────────┐
                │ Calculate file age (diff)│
                └────────────┬─────────────┘
                             │
             ┌───────────────▼───────────────┐
             │ diff > 7 days threshold?      │
             └───────────────┬───────────────┘
                             │Yes
                             ▼
        ┌─────────────────────────────────────────┐
        │ Delete file                              │
        │ Fetch fresh data from gateway (put...)   │
        │ Recursively call getMeasureInfo(...)     │
        └─────────────────────────────────────────┘
                             │ No
                             ▼
        ┌─────────────────────────────────────────┐
        │ Load JSON into List<EMeasure> using      │
        │ Jackson mapper                           │
        │ Return first EMeasure                    │
        └─────────────────────────────────────────┘

        ┌─────────────────────────────────────────┐
        │ If file does NOT exist:                  │
        │   Fetch fresh data from gateway (put...) │
        │   Recursively call getMeasureInfo(...)   │
        └─────────────────────────────────────────┘
```

---

**Key Points in the Logic**

* **7-day rule:** `diff > (7L * 24 * 60 * 60 * 1000)` triggers a fresh fetch.
* **Cache file:** Stored in `.../tmp/mu/ECQM/{year}/{measureId}.json`.
* **Recursive calls:** Always re-runs `getMeasureInfo()` after fetching new data.
* **Freshness check** happens *only* if the file exists — missing files skip straight to fetching.

---
