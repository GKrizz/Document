## FILE : HtmlTransformer.java:295

---

# Day 157 - October 22, 2025 - Apache Xalan `accessExternalDTD` Issue

## Problem

When running your XML transformation code, you encounter the following error:

```java
java.lang.IllegalArgumentException: Not supported: http://javax.xml.XMLConstants/property/accessExternalDTD
```

This happens because **Apache Xalan's TransformerFactory doesn't support the `accessExternalDTD` property**, yet your code tries to set it:

```java
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
```

---

## Why does this happen?

* Your current TransformerFactory implementation is **Apache Xalan**.
* Apache Xalan **does not implement** the security property `accessExternalDTD`.
* Calling `setAttribute` with this property in Apache Xalan results in an `IllegalArgumentException`.

---

## How to fix?

Use the **JDK’s built-in TransformerFactory**, which supports the `accessExternalDTD` property, by forcing its usage:

```java
System.setProperty("javax.xml.transform.TransformerFactory",
                   "com.sun.org.apache.xalan.internal.xsltc.trax.TransformerFactoryImpl");

TransformerFactory factory = TransformerFactory.newInstance();
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_STYLESHEET, "");
// continue your processing
```
---

Would you like me to generate this as a markdown file or add anything else?
