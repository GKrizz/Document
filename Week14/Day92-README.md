CMS125v13 

CMS125v13 Measure Test
Measure: Breast Cancer Screening
Measurement Period: 01/01/2025 - 12/31/2025
HQMF ID: 2C928083-8907-CE68-0189-2BC3939006EC
CMS ID: CMS125v13 (eCQM Specification)





https://static.glaceemr.com/ECQM/2025/CMS125v13.html
https://datagateway.glaceemr.com/DataGatewayMediSpan/eCQMServices/getECQMInfoById?ids=112&reportingYear=2025


Here’s why the current rule is **incorrect**:

1. **`encounter1: Encounter()`**

   * You’re looping through encounters instead of diagnoses.
   * CMS says: `["Diagnosis": "Advanced Illness"] AdvancedIllnessDiagnosis`. So you need `Diagnosis()` objects.

2. **Diagnosis code check** is correct (`2.16.840.1.113883.3.464.1003.110.12.1082`) — that’s the Advanced Illness ValueSet.

3. **Time window**:

   * You correctly need **2 years before the end of the measurement period**, but using `encounter1.getStartDate()` is wrong. Use the diagnosis start/end date.

---

✅ **Corrected Rule v13-12:**

```java
/**
Denominator Exclusions = 
    AND: Age >= 66 year(s) at: "End Measurement Period" 
    AND: ["Diagnosis": "Advanced Illness"] AdvancedIllnessDiagnosis
        starts 2 years or less before end of "Measurement Period"
**/
rule "Measure 112 - Denominator Exclusionv13 - 12"
    agenda-group "ecqm_denominator_exclusion"
when
    request:Request($patient:patient)
    response:Response($measureStatus:measureStatus.get("112"))
    Prerequisite($measureList:measureList)
    measure:EMeasure(measure.getId()==112 && measure.getCmsId().equals("CMS125v13")) from $measureList

    // Check denominator state
    eval($measureStatus.getDenominator()==1 && $measureStatus.getDenominatorExclusion()==0 && $measureStatus.getRuleState()!=2)
    
    // Check age >= 66 at end of measurement period
    eval(IntervalUtilities.getIntervalByYears($patient.getDob(), request.getMeasurementPeriodEnd()) >= 66)
    
    // Look for Advanced Illness diagnosis in last 2 years
    advIllness: Diagnosis() from $patient.getDxList()
    eval(advIllness.getCode() != null)
    eval(QDMUtilities.checkCodeAvailablity(measure, QDMCategory.ATTRIBUTE, 
            "2.16.840.1.113883.3.464.1003.110.12.1082",  // Advanced Illness Value Set
            advIllness.getCode(), advIllness.getCodeSystemOID()))
    eval(IntervalUtilities.isOverlapping(
            advIllness.getStartDate() != null ? advIllness.getStartDate() : advIllness.getRecordedDate(),
            advIllness.getEndDate() != null ? advIllness.getEndDate() : advIllness.getRecordedDate(),
            DateUtils.addMonths(request.getMeasurementPeriodEnd(), -24), 
            request.getMeasurementPeriodEnd()
    ))
then
    $measureStatus.setRuleState(2);
    $measureStatus.getRuleStateObject().put("denexclu", advIllness);
    modify(response) { getMeasureStatus().put("112", $measureStatus) }
end
```

---

Yes ✅ — this looks correct now.

Here’s why:

1. **Age check**:

```java
eval(IntervalUtilities.getIntervalByYears($patient.getDob(),request.getMeasurementPeriodEnd())>=66)
```

✔ Ensures patient is ≥66 at the end of the measurement period.

2. **Advanced Illness diagnosis**:

```java
advIllness: Diagnosis() from $patient.getDxList()
eval(advIllness.getCode() != null)
eval(QDMUtilities.checkCodeAvailablity(measure, QDMCategory.ATTRIBUTE,
    "2.16.840.1.113883.3.464.1003.110.12.1082",
    advIllness.getCode(), advIllness.getCodeSystemOID()))
```

✔ Correctly checks **Advanced Illness ValueSet** (OID `2.16.840.1.113883.3.464.1003.110.12.1082`).

3. **Time window: last 2 years**:

```java
eval(IntervalUtilities.isOverlapping(
    advIllness.getStartDate() != null ? advIllness.getStartDate() : advIllness.getRecordedDate(),
    advIllness.getEndDate() != null ? advIllness.getEndDate() : advIllness.getRecordedDate(),
    DateUtils.addMonths(request.getMeasurementPeriodEnd(), -24),
    request.getMeasurementPeriodEnd()
))
```

✔ Correctly limits diagnoses to **within 2 years before the end of measurement period**.

4. **Rule action**:

```java
$measureStatus.setRuleState(2);
$measureStatus.getRuleStateObject().put("denexclu", advIllness);
modify(response) {getMeasureStatus().put("112",$measureStatus)}
```

✔ Properly marks the denominator exclusion and stores the diagnosis.

---

💡 Only note: In your comments you wrote `starts 2 years or less before...` — the `isOverlapping` function allows for diagnoses that **start before but still overlap the 2-year window**, which is generally consistent with CMS specifications.
