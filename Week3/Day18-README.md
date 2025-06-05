# 📅 Day 18 — Jun 5, 2025 (Thursday)

## 🩺 Depression Screening (G8510) CPT Code Support

### ✅ Feature Summary

This update enables automatic **insertion of the `G8510` CPT code** for Depression Screening **if the result is "negative"**, based on **HEDIS measure 134**. The result is read from the `geteditData` map passed to the `saveServicesBasedOnHedisMeasuresConfig()` method.

---

## 🔧 Where This Is Implemented

### ➤ `ChargesServiceImpl.java`

#### Method:

```java
public void saveServicesBasedOnHedisMeasuresConfig(...)
```

---

## 💡 What Was Added

### 1. **Measure Check**

This line checks if the Depression Screening (measure ID 134) is included:

```java
boolean includeDepressionScreening = measureIds != null && measureIds.contains("134");
```

---

### 2. **CPT Code Setup**

```java
String depressionScreeningCptCode = "G8510";
List<String> depressionScreenCodesList = Arrays.asList("G8431", "G8510");
```

---

### 3. **Service Classification**

During loop over existing services:

```java
else if (depressionScreenCodesList.contains(cpt)) {
    existingServiceMap.put("depressionScreeningData", service);
}
```

---

### 4. **Helper Method to Get Screening Result**

This checks if depression screening result is “negative”, “neg”, or directly “g8510”.

```java
private boolean getDepressionScreeningResultFromEditData(Map<String, Object> geteditData) {
    if (geteditData == null) return false;

    Object resultObj = geteditData.get("depressionScreeningResult");
    if (resultObj != null) {
        String result = resultObj.toString().trim().toLowerCase();
        return result.equals("negative") || result.equals("neg") || result.equals("g8510");
    }

    return false;
}
```

---

### 5. **Main Logic to Add CPT G8510**

```java
if (includeDepressionScreening) {
    // Debug Logs
    System.out.println(">> Checking Depression Screening Result...");
    System.out.println(">> geteditData = " + geteditData);

    boolean depressionScreenedNegative = getDepressionScreeningResultFromEditData(geteditData);
    System.out.println(">> Depression Result = " + depressionScreenedNegative);

    boolean alreadyExists = false;
    if (existingServices != null) {
        alreadyExists = existingServices.stream()
            .anyMatch(service -> depressionScreeningCptCode.equals(service.getCptCode()));
    }
    System.out.println(">> Already Exists = " + alreadyExists);

    if (depressionScreenedNegative && !alreadyExists) {
        Cpt depressionCpt = getCptCodesDetails(depressionScreeningCptCode, -1, -1);
        if (depressionCpt != null && Boolean.TRUE.equals(depressionCpt.getCptIsactive())) {
            System.out.println(">> Adding new CPT Code: " + depressionScreeningCptCode);
            newCptCodes.add(depressionScreeningCptCode);
        } else {
            System.out.println(">> CPT G8510 not active or not found.");
        }
    } else {
        System.out.println(">> No CPT added: Either not negative or already exists.");
    }
}
```

---

## 🧪 How to Test It

### 1. Trigger `saveServicesBasedOnHedisMeasuresConfig()` with:

* `measureIds` including `"134"`
* `geteditData` map having key `"depressionScreeningResult"` with value `"negative"` or `"neg"`

### 2. Watch logs for:

```shell
>> Checking Depression Screening Result...
>> geteditData = {depressionScreeningResult=negative}
>> Depression Result = true
>> Already Exists = false
>> Adding new CPT Code: G8510
```

---


