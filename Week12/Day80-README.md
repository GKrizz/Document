# XML to HTML Transformation (Legacy Flow)

This module handles the **transformation of XML into HTML** using **XSLT**.
It relies on Java’s `TransformerFactory` along with a custom `URIResolver` to load and apply stylesheets.

---

## 🔄 Flow Overview

1. **Inputs**

   * `xmlIn` → The XML data to be transformed (usually a `StreamSource` or `DOMSource`).
   * `xslIn` → The XSLT stylesheet that defines how XML should be rendered as HTML.

2. **Transformer Creation**

   ```java
   TransformerFactory tFactory = TransformerFactory.newInstance();
   Transformer xformer = tFactory.newTransformer(xslIn);
   ```

   * If `xslIn` is missing, invalid, or unreadable → transformer creation fails.
   * Any `<xsl:include>` or `<xsl:import>` references are resolved via the custom `URIResolver`.

3. **Transformation**

   ```java
   StringWriter htmlOut = new StringWriter();
   xformer.transform(xmlIn, new StreamResult(htmlOut));
   ```

   * Produces an **HTML string** from the XML input.

---

## 🛠️ Debugging Guide

If transformation fails, check the following:

### 1. Validate `xslIn`

* Ensure the XSL file exists and is readable.

  ```java
  File f = new File("/path/to/NarrativeTemplate.xsl");
  System.out.println("Exists? " + f.exists() + ", CanRead? " + f.canRead());
  ```

### 2. Watch for `systemId: null`

* If you see logs like:

  ```
  [DEBUG] xslIn systemId: null
  ```

  → The stylesheet path is not being set correctly.

### 3. Catch Transformer Errors Clearly

Update exception handling:

```java
} catch (TransformerConfigurationException e) {
    System.err.println("[ERROR] Failed to create Transformer – Invalid or empty XSL input");
    e.printStackTrace();
}
```

### 4. Check `URIResolver`

* The resolver must not return `null`.
* Add debugging:

  ```java
  Source src = getSource(hrefVal);
  if (src == null) {
      System.err.println("[ERROR] getSource returned null for href: " + hrefVal);
  }
  ```

---

## ⚠️ Common Errors & Fixes

| Error Message                                      | Likely Cause                                 | Fix                                                      |
| -------------------------------------------------- | -------------------------------------------- | -------------------------------------------------------- |
| `[ERROR] Transformer is null – cannot transform`   | `xslIn` invalid / unreadable                 | Verify stylesheet file exists & is correctly initialized |
| `systemId: null`                                   | Missing stylesheet path                      | Ensure `xslIn.setSystemId()` or use absolute path        |
| `URIResolver returned null`                        | Included/imported stylesheet not found       | Fix `hrefVal` resolution logic                           |
| `NullPointerException (Location of error unknown)` | `StreamSource` or stylesheet missing content | Validate both XML and XSL inputs                         |

---

## ✅ Checklist Before Running

* [ ] Confirm `xmlIn` is a valid XML source
* [ ] Confirm `xslIn` points to a valid, readable `.xsl` file
* [ ] Ensure `TransformerFactory.newInstance()` is working in your JDK
* [ ] Check logs for `systemId` and `URIResolver` outputs

---

## 📌 Example Usage

```java
StreamSource xmlIn = new StreamSource(new File("input.xml"));
StreamSource xslIn = new StreamSource(new File("NarrativeTemplate.xsl"));

TransformerFactory tFactory = TransformerFactory.newInstance();
Transformer xformer = tFactory.newTransformer(xslIn);

StringWriter htmlOut = new StringWriter();
xformer.transform(xmlIn, new StreamResult(htmlOut));

System.out.println("HTML Output: " + htmlOut.toString());
```

---
