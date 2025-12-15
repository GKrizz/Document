
# Day 205 – December 9, 2025 – Tuesday

**Account:** vreddy

## Issue: Incorrect Capitalization of `@@patientheshe` in SOAP / AWV HTML Output

---

### 1. Issue Summary

In the generated **SOAP / AWV Questionnaire HTML**, all dynamic pronouns using `@@patientheshe` were displayed in **lowercase**, even when appearing:

* At the **start of a sentence**
* Immediately **after a full stop (`.`)**

#### Example (Incorrect Output)

```html
<Font style="">she has difficulty with hearing.</Font>
<Font style="">she is not afraid of falling.</Font>
```

#### Expected Output

```html
<Font style="">She has difficulty with hearing.</Font>
<Font style="">She is not afraid of falling.</Font>
```

---

### 2. Root Cause

* `@@patientheshe` was globally replaced using:

```java
HtmlData.replaceAll("@@patientheshe", patientheshe);
```

* `patientheshe` was always **lowercase** (`"she"` / `"he"`).
* No logic existed to:

  * Detect **sentence boundaries**
  * Detect **start of HTML content**
  * Apply **conditional capitalization**

Result: all pronouns rendered in lowercase.

---

### 3. Requirements

1. Capitalize `@@patientheshe` when:

   * First visible text inside an HTML tag
   * After `.`, `?`, or `!` followed by a space

2. Keep lowercase when used in the middle of a sentence.

---

### 4. Fix Approach

* Replace single global replacement with **context-aware replacements** using regex.
* Strategy:

  1. Build two versions of the pronoun:

     * Lowercase → `she / he / the patient`
     * Uppercase → `She / He / The patient`

  2. Apply replacements in this order:

     1. Start of HTML text → capitalize
     2. After punctuation → capitalize
     3. Remaining occurrences → lowercase

---

### 5. Code Changes

#### Pronoun Setup

```java
String heSheLower = patientheshe; // "she" / "he" / "the patient"
String heSheUpper =
        heSheLower.substring(0, 1).toUpperCase() + heSheLower.substring(1);
```

#### Capitalization Logic

```java
// 1) Capitalize when first visible text inside HTML tag
HtmlData = HtmlData.replaceAll(
        ">(\\s*)@@patientheshe",
        ">$1" + heSheUpper
);

// 2) Capitalize after sentence-ending punctuation
HtmlData = HtmlData.replaceAll(
        "([\\.\\?\\!]\\s*)@@patientheshe",
        "$1" + heSheUpper
);

// 3) Remaining occurrences default to lowercase
HtmlData = HtmlData.replace("@@patientheshe", heSheLower);
```

---

### 6. Final Output (Verified)

#### Before Fix

```html
<Font>she has difficulty with hearing.</Font>
<Font>she is not afraid of falling.</Font>
<Font>she has not been bothered by dizziness.</Font>
```

#### After Fix

```html
<Font>She has difficulty with hearing.</Font>
<Font>She is not afraid of falling.</Font>
<Font>She has not been bothered by dizziness.</Font>
```

✔ Correct capitalization
✔ Grammar preserved
✔ No impact on existing templates

---

### 7. Scope & Safety

* ✅ Change isolated to `@@patientheshe`
* ✅ No HTML structure modifications
* ✅ Safe for SOAP notes, AWV questionnaires, print & PDF views
* ✅ Works for male, female, unknown gender

---

### 8. Status

* **Issue Status:** ✅ Resolved
* **Verified In:** AWV Questionnaire, SOAP Template Rendering
* **Risk Level:** Low

---
