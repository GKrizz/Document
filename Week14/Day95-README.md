# `createURIResolver()` Method

## Overview

The `createURIResolver()` method returns a custom `URIResolver` implementation used to resolve XSLT resource paths dynamically during XML transformations.

This resolver adjusts the resource URIs (`href`) based on tenant-specific paths retrieved via a remote SSO service, ensuring that XSL files and related resources are correctly located and loaded in a multi-tenant environment.

---

## Functionality

* Uses a remote HTTP GET request to retrieve tenant-specific versioned paths from an SSO service.
* Dynamically rewrites `href` values for XSL imports/includes based on tenant version paths.
* Supports relative path rewriting for cases where `href` contains `"../../"` or `"../"`.
* Falls back to resolving paths relative to the current XSL file directory (`currentXslPath`) when no tenant-specific path rewriting applies.

---

## Detailed Behavior

1. **Fetch tenant-specific path**

   * Calls `http://sso.glaceemr.com/TestSSOAccess?accountId=<tenantId>` to retrieve JSON data.
   * Extracts the `"version_path"` field, used as a base path for resource loading.

2. **Rewrite `href` paths**

   * If `href` contains `"../../"`, replaces this prefix with `<version_path>/jsp/chart/`.
   * If `href` contains `"../"`, replaces this prefix similarly.
   * Otherwise, resolves the path relative to the directory of the current XSL file.

3. **Return a `Source` object**

   * The resolved URI will be used by the XSLT processor to load the referenced resource.

---

## Code Snippet

```java
protected URIResolver createURIResolver() {
    final String currentXslPath = this.xslpath;

    return new URIResolver() {
        public Source resolve(String href, String base) throws TransformerException {
            String hrefVal = "";
            try {
                AjaxConnect conn = new AjaxConnect();
                String sso_url = conn.sendGet("http://sso.glaceemr.com/TestSSOAccess?accountId=" + TennantContextHolder.getTennantId());
                JSONObject data1 = new JSONObject(sso_url);
                String acc_crm_id = data1.getString("version_path");

                if (href.contains("../../"))
                    hrefVal = href.replaceAll("../../", acc_crm_id + "/jsp/chart/");
                else if (href.contains("../"))
                    hrefVal = href.replace("../", acc_crm_id + "/jsp/chart/");
                else {
                    String baseDir = "";
                    if (currentXslPath != null && currentXslPath.length() > 0) {
                        int index = currentXslPath.lastIndexOf('/');
                        if (index >= 0) {
                            baseDir = currentXslPath.substring(0, index + 1);
                        }
                    }
                    hrefVal = baseDir + href;
                }

                // Return the resolved Source object here (missing in snippet)
                // For example:
                // return new StreamSource(new FileInputStream(new File(hrefVal)));

            } catch (Exception e) {
                throw new TransformerException("Error resolving URI: " + href, e);
            }
            // Must return a Source, placeholder:
            return null;
        }
    };
}
```

---
