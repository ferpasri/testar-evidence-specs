# Property Reading Kinds

A test oracle checks whether each image widget has an alternative-text property. It reports failed when the property is established to be absent, passed when the property exists with a null value, and unknown when its presence cannot be determined. This oracle checks property presence; a separate oracle can evaluate the description's content. A fourth evaluation compares the captured null to an explicitly supplied expected null. Four evaluations therefore address three property targets. Missing data in a filtered snapshot alone establishes neither absence nor a verdict.

Read the events in order and compare the result with `expected.json`.
