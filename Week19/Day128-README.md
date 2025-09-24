# Superbill - Show "Auto Populate" Button Based on HEDIS Configuration

📅 **Day 128**  
🗓️ **Date:** September 23, 2025  
📍 **Context:** Enable visibility of the **Auto Populate** button in Superbill based on HEDIS configuration availability.


---

## 🧩 Affected Files

### ✅ UI Layer

#### `SuperbillSummaryDesktopView.java`
```java
@Override
public void setAutoPopulateButtonVisible(boolean visible) {
    addCptButtonElements.setVisible(visible);
}
````

#### `SuperbillSummaryView.java`

```java
void setAutoPopulateButtonVisible(boolean visible);
```

#### `SuperbillSummaryActivity.java`

```java
@Override
public void start(AcceptsOneWidget panel) {
    panel.setWidget(this.view.asWidget());
    bindEvent();
    initActivity();
    bindEventHandler();

    Integer encounterId = null;
    try {
        String encounterIdStr = actionServletURL.getEncounterId();
        if (encounterIdStr != null && !encounterIdStr.isEmpty()) {
            encounterId = Integer.valueOf(encounterIdStr.trim());
        }
    } catch (NumberFormatException e) {
        view.notificationError("Invalid encounter ID. Please check and try again.");
    }

    if (encounterId != null) {
        checkHedisConfiguration(encounterId);
    } else {
        view.setAutoPopulateButtonVisible(false); 
    }
}

private void checkHedisConfiguration(Integer encounterId) {
    superBillClient.isHedisConfigured(encounterId, new CustomMethodCallback<Boolean>() {
        @Override
        public void onRequestSuccess(Boolean result) {
            view.setAutoPopulateButtonVisible(Boolean.TRUE.equals(result));
        }

        @Override
        public void onRequestFailure(Exception e) {
            view.setAutoPopulateButtonVisible(false);
        }
    });
}
```

#### `SuperBillClient.java`

```java
@GET
@Path("/Charges/isHedisConfigured?encounterId={encounterId}")
void isHedisConfigured(@PathParam("encounterId") Integer encounterId, CustomMethodCallback<Boolean> callback);
```

---

### ✅ Backend Layer

#### `ChargesController.java`

```java
@RequestMapping(value="/isHedisConfigured", method=RequestMethod.GET)
@ResponseBody
public EMRResponseBean isHedisConfigured(@RequestParam(value="encounterId", required=true) Integer encounterId) {
    EMRResponseBean responseBean = new EMRResponseBean();
    try {
        Boolean isConfigured = chargesServices.isHedisConfigured(encounterId);
        responseBean.setSuccess(Boolean.TRUE.equals(isConfigured));
        responseBean.setData(isConfigured);
    } catch (Exception e) {
        responseBean.setSuccess(false);
        responseBean.setData(false);
    }
    return responseBean;
}
```

#### `ChargesServices.java`

```java
boolean isHedisConfigured(Integer encounterId) throws Exception;
```

#### `ChargesServicesImpl.java`

```java
@Override
public boolean isHedisConfigured(Integer encounterId) {
    String measureIds = measureCalculationService.checkHedisConfiguration(encounterId);
    return measureIds != null && !measureIds.isEmpty();
}
```

---

## 🔄 Database HEDIS Configuration (Test SQL)

```sql
-- Disable HEDIS configuration for 2025
UPDATE hedis_configuration
SET hedis_configuration_isactive = 'f'
WHERE hedis_configuration_reporting_year = 2025;

-- Verify status
SELECT hedis_configuration_reporting_year, hedis_configuration_isactive FROM hedis_configuration;

-- Enable HEDIS configuration for 2025
UPDATE hedis_configuration
SET hedis_configuration_isactive = 't'
WHERE hedis_configuration_reporting_year = 2025;
```

---
## Final UI:
<img width="1275" height="614" alt="Screenshot" src="https://github.com/user-attachments/assets/833be311-9ba4-455b-b111-7ed711f48b01" />
