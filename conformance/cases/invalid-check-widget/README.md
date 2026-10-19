# Check Result with an Unknown Widget

The check result refers to a widget that is absent from the identified observation. Processing rejects that record with invalid_check_target, identifies target.widget, and preserves the two accepted prefix events with zero recorded check results. Naming an absent property is valid; naming an uncaptured widget as a concrete widget reference is not.

Read the events in order and compare the result with `expected.json`.
