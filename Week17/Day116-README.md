
# Day 116 – September 11, 2025 (Thursday)

## Summary

### UI Updates

* Added a **CPT II code auto-populate button** in the Superbill UI to streamline code entry.
* Modified files:

  * `SuperbillSummaryDesktopView.ui.xml`
  * `SuperbillSummaryDesktopView.java`
  * `SuperbillSummaryView.java`
  * `SuperbillSummaryActivity.java`
  * `SuperBillClient.java`

### Backend Updates

* Implemented the **CPT II code auto-populate logic** for the Superbill feature.
* Modified files:

  * `ChargesController.java`
  * `ChargesServices.java`
  * `ChargesServicesImpl.java`

---

## Feature Details

This update introduces an enhancement to the Superbill functionality by adding a button that automatically populates CPT II codes for a patient encounter, improving efficiency and reducing manual entry errors.

### Key CPT II Codes Supported:

* G8756
* G8428

### Patient Example Link:

[Patient Chart (ID: 2697)](https://dev2.glaceemr.com:444/D2DesktopNew/jsp/chart/patientdetails/Patient_Chart.Action?isLog=1&patientAlert=1&patientId=2697#)

---

## Code Snippets & API

### UI: Calling Backend to Add CPT II Codes

```java
superBillClient.addCptIiCodes(patientId, encounterId, new CustomMethodCallback<EMRResponseBean>() {
    @Override
    public void onSuccess(Method method, EMRResponseBean response) {
        NotificationAlerts.hideLoadingPanel(NotificationConstants.SINGLE_NOTIFICATION, "", NotificationConstants.DEFAULT, view.getSaveLoadingPanel(), "superbill");

        if (response != null && response.isSuccess()) {
            view.notificationSuccess(response.getData().toString());

            setClickedRefresh(true);
            getCreatedServices(getActionServletURL().getaccountId(), getPatientId(), getEncounterDate(), getsDoctorId());
            NotificationAlerts.loadingPanel("superbill", "Refreshing services...", view.getSaveLoadingPanel());

        } else {
            view.notificationWarning(response != null ? response.getData().toString() : "Failed to add CPT II codes.");
        }
    }

    @Override
    public void onFailure(Method method, Throwable exception) {
        NotificationAlerts.hideLoadingPanel(NotificationConstants.SINGLE_NOTIFICATION, "", NotificationConstants.DEFAULT, view.getSaveLoadingPanel(), "superbill");
        view.notificationError("Error adding CPT II codes: " + exception.getMessage());
    }
});
```

---

### Backend Controller (JAX-RS Endpoint)

```java
@POST
@Path("/Charges/addCptIiCodes?patientId={patientId}&encounterId={encounterId}")
void addCptIiCodes(@PathParam("patientId") Integer patientId,
                   @PathParam("encounterId") Integer encounterId,
                   CustomMethodCallback<EMRResponseBean> callback);
```

---

### Spring MVC Controller Method

```java
@RequestMapping(value="/addCptIiCodes", method=RequestMethod.POST)
@ResponseBody
public EMRResponseBean addCptIiCodes(
        @RequestParam(value="patientId", required=true) Integer patientId,
        @RequestParam(value="encounterId", required=true) Integer encounterId) throws Exception {

    EMRResponseBean responseBean = new EMRResponseBean();

    try {
        Boolean success = chargesServices.addCptIiCodes(patientId, encounterId);

        if (success != null && success) {
            responseBean.setSuccess(true);
            responseBean.setData("CPT II codes added successfully.");
        } else {
            responseBean.setSuccess(false);
            responseBean.setData("Failed to add CPT II codes.");
        }

    } catch (Exception ex) {
        responseBean.setSuccess(false);
        responseBean.setData("Error adding CPT II codes: " + ex.getMessage());
        // optionally: log the error
    }

    return responseBean;
}
```

---
