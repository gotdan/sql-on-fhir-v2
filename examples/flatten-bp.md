# US Core BP Flattening Definition Example

## Definition
```json
{
	"name": "us_core_blood_pressure",
	"resource": "Observation",
	"vars": [{
		"name": "sbp_component",
		"expr": "component.where(code.coding.exists(system='http://loinc.org' and code='8480-6')).first()"
	},{
		"name": "dbp_component",
        "expr": "component.where(code.coding.exists(system='http://loinc.org' and code='8462-4')).first()"
	}],
	"filters": [{
		"expr": "code.coding.exists(system='http://loinc.org' and code='85354-9')"
	},{
		"expr": "%sbp_component.dataAbsentReason.empty()"
	},{
		"expr": "%dbp_component.dataAbsentReason.empty()"
	}],
    "columns": [{
		"name": "id", "expr": "id"
	},{
		"name": "patient_id", "expr": "subject.getId()"
	},{
		"name": "effective_date_time", "expr": "effective.ofType(dateTime)"
	},{
		"name": "sbp_quantity_system",  "expr": "%sbp_component.value.ofType(Quantity).system"
	},{
		"name": "sbp_quantity_code",  "expr": "%sbp_component.value.ofType(Quantity).code"
	},{
		"name": "sbp_quantity_display",  "expr": "%sbp_component.value.ofType(Quantity).unit"
	},{
		"name": "sbp_quantity_value",  "expr": "%sbp_component.value.ofType(Quantity).value"
	},{
		"name": "dbp_quantity_system",  "expr": "%dbp_component.value.ofType(Quantity).system"
	},{
		"name": "dbp_quantity_code",  "expr": "%dbp_component.value.ofType(Quantity).code"
	},{
		"name": "dbp_quantity_display",  "expr": "%dbp_component.value.ofType(Quantity).unit"
	},{
		"name": "dbp_quantity_value",  "expr": "%dbp_component.value.ofType(Quantity).value"
	}]
}
```

## Expected Behavior
- Generate one row in the view per Observation in the source data
- Only include Observations with a LOINC code of `85354-9` and systolic and diastolic components
- If there are multiple diastolic and/or systolic components (not valid for US Core profile), only take the first of each 
- Don't include Observations with a populated data absent reason in the systolic or diastolic component
- Extract the id portion of the url from any populated subject reference as the `patient_id` column value
- Set the `patient_id` column value to null if the subject reference is not populated (not valid for US Core profile)
- Populate the columns where data is present and set columns to null where it is not
- Name the columns to correspond with the column names listed
- Name the view or table `us_core_blood_pressure`