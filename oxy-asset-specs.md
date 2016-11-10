# Using Asset Specifications in Form Definitions

This describes the features available to dynamically query asset and specification information to build forms that reflect values from the parent work order or asset.

## Available Attributes

The following parameterized attributes can be used to query information related to the current work order or asset:

* **${WORKORDER_LOOKUP('key')}** - returns a value from the current work order (ie: `${WORKORDER_LOOKUP('Priority')`} returns the priority)
* **${LOCATION_LOOKUP('key')}** - returns a value from the location for the current work order
* **${ASSET_LOOKUP('key')}** - returns a value from the primary asset for the current work order
* **${ASSET_SPEC_LOOKUP('attribute')}** - returns the asset specification value for an attribute (`${ASSET_SPEC_LOOKUP('RUNHOURS')}`)
* 

## Point Definitions

The group values for points will replace attribute values using the above attribute settings (or any other valid attributes). This can be used to insert values from the Maximo configuration into the headers displayed by the form.

![Inspection Example](https://cloud.githubusercontent.com/assets/421270/11827089/d79ce0fc-a346-11e5-8c5b-0ca40276ab17.png)

In addition, each point now has a separate *Applicability* field that can be used to control whether or not the associated readings are displayed. This can also be accomplished by editing the individual reading applicabilities but is simpler.

For instance this can be useful for cases where a section is only available if a particular specification is defined for the asset:

![Manage Point](https://cloud.githubusercontent.com/assets/421270/11827105/f7d3253e-a346-11e5-8fb2-dd81f59197c0.png)

## Reading Settings and Validation

The reading values can also reference the attribute statements to lookup dynamic values for the form.

These are primarily useful in validation statements - the asset specification values can be used as the bounds for triggering alarms:

![Validation Example](https://cloud.githubusercontent.com/assets/421270/11827096/e692da6c-a346-11e5-8c28-ba2acf65d559.png)

## Summary

The follow changes need to be made to forms to parameterize the settings based on the associated asset:

* Create a new draft for the form and change the description to be generic instead of referring to a specific asset
* Update the set points in the point group definitions to use specification lookups instead of hardcoded values
* Add applicability statements to points that are not available across all assets
* Change any explicit references to the asset in the readings to use an attribute lookup
  * ie change *Test Failed! Did or will you repair PIT-1005?* to *Test Failed! Did or will you repair ${ASSET_LOOKUP('Equip ID')}?*
* Modify the validation expressions to use specification lookups instead of hardcoded values
