

# OpenFaxPickerAction.java

### ✔ Java

```java
if (parent_location == 9) {
    System.out.println("Processing web fax for parent_location = " + parent_location);
    Hashtable toList = new Hashtable();

    if (!((empFirstName.equalsIgnoreCase(refDoctorFName)) && (refDoctorLName.equalsIgnoreCase(empLname)))) {
        System.out.println("Full name = " + fullName);
        System.out.println("Ref number = " + refnumber);

        // Ensure delimiter is correct (change $$ to , if needed)
        String[] faxNumbers = refnumber.split("\\$\\$");
        for (String fax : faxNumbers) {
            fax = fax.trim();
            if (!fax.isEmpty()) {
                toList.put(fax, fullName);
            }
        }

        request.setAttribute("refDocName", fullName);
        request.setAttribute("refDocNumber", refnumber);
    } else {
        request.setAttribute("refDocName", "-1");
        request.setAttribute("refDocNumber", "-1");
    }

    System.out.println("Final toList = " + toList);
    request.setAttribute("toList", toList);

    String[] urls = PrintURL.split("@@@@@@");
    request.setAttribute("fromPrintFax", "1");
    request.setAttribute("attachmentPdf", urls[0] + ".pdf");

    request.setAttribute("parent_location", "2");
    PrintURL = urls[1];
}
```

---
# OpenFaxPicker.jsp

### ✔ JSP

```
<table id="faxnodetailsto" width="100%" height="100%" cellpadding="0" cellspacing="1">
    <% 
    Hashtable toList = (Hashtable) request.getAttribute("toList");
    if (toList == null || toList.isEmpty()) { %>
        <tr>
            <td align="center" width="10%" class="tracksheetColumn" valign="top">Fax No.</td>
            <td width="20%" align="left" valign="top">
                <input name="Totxtfax_No" maxlength="255" size="20" type="text" value="" id="faxInput0" autocomplete="off" oninput="fetchAndDisplayFaxNumbers(this.id,this.value)"/>
                <font color="red" size="4">*</font>
            </td>
            <td align="center" width="10%" class="tracksheetColumn" valign="top">Recipient Name.</td>
            <td width="60%" align="left" valign="top">
                <input name="TotxtReceipient_Name" maxlength="255" size="60" type="text" value="" autocomplete="off" id="nameInput0"/>
                <font color="red" size="4">*</font>
            </td>
        </tr>
    <% } else {
        Enumeration e = toList.keys();
        int index = 0;
        while (e.hasMoreElements()) {
            String faxNumber = (String) e.nextElement();
            String name = (String) toList.get(faxNumber);
    %>
        <tr>
            <td align="center" width="10%" class="tracksheetColumn" valign="top">Fax No.</td>
            <td width="20%" align="left" valign="top">
                <input name="Totxtfax_No" maxlength="255" size="20" type="text" value="<%=faxNumber%>" id="faxInput<%=index%>" autocomplete="off" oninput="fetchAndDisplayFaxNumbers(this.id,this.value)"/>
                <font color="red" size="4">*</font>
            </td>
            <td align="center" width="10%" class="tracksheetColumn" valign="top">Recipient Name.</td>
            <td width="60%" align="left" valign="top">
                <input name="TotxtReceipient_Name" maxlength="255" size="60" type="text" value="<%=name%>" autocomplete="off" id="nameInput<%=index%>"/>
                <font color="red" size="4">*</font>
                <img src="<%=request.getContextPath()%>/images/close.gif" title="Remove this number" style="cursor:pointer" onclick="deleteRow('faxnodetailsto',this.parentNode.parentNode.rowIndex);"/>
            </td>
        </tr>
    <% 
            index++;
        }
    } %>
</table>

```
