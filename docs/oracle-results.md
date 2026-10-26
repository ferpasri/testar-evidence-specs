# Oracle results

A test oracle determines whether a condition holds. Running a test oracle once against a particular target is an oracle evaluation. Its recorded outcome and supporting evidence form an oracle result.

## Result fields

The `oracle_result_recorded` event has the following data fields:

| Field | Meaning |
| --- | --- |
| `test_run` | Required identity of the containing test run. |
| `test_sequence` | Required for sequence-level targets; omitted for a whole-test-run target. It belongs to the named test run. |
| `name` | Required readable description of the condition evaluated. |
| `oracle` | Required object: nonblank `id`, optional nonblank `version`, and optional `parameters` property map. It identifies and configures the check; it need not contain executable logic or a separate condition object. |
| `oracle_target` | Required target described below. |
| `oracle_status` | Required reported outcome: `passed`, `failed`, `pending`, `unknown`, or `error`. |
| `expected_value` | Optional JSON expectation, including an explicitly supplied null. |
| `observed_value` | Optional value, established absence, or unknown information obtained by the oracle. |
| `oracle_severity` | Optional source-defined importance: required nonblank `label` and optional finite numeric `value`. |
| `oracle_details` | Required property map for explanations and supporting information; can be empty. |

Passed means the condition was satisfied; failed means it was not. Pending means no definite result when reported; unknown means the oracle could not determine one; error means an evaluation problem. Each report is retained separately, including pending results after a history ends.

Severity is independent of status. A missing alternative-text condition can be failed with severity warning. Preserve the source label and numeric value exactly. There is no fixed list, numeric range, default severity, or inferred cross-tool ranking. Omitted severity stays omitted; cross-tool normalization uses explicit analysis rules.

## Oracle targets

The `target_type` field identifies what the oracle evaluated. The table lists the additional fields needed to identify that target.

| Target type | Additional fields | Meaning |
| --- | --- | --- |
| `test_run` | None | The result's whole test run; omit `test_sequence`. |
| `test_sequence` | None | The result's whole test sequence. |
| `observation` | `observation` | One recorded moment. |
| `state_property` | `observation`, `property` | One property of that observation's recorded state. |
| `widget` | `observation`, `widget` | One captured widget. |
| `widget_property` | `observation`, `widget`, `property` | One property of that widget. |
| `action_execution` | `action_execution` | One selected action occurrence in the result's sequence. |
| `observation_range` | `first_observation`, `last_observation` | An inclusive range within the result's sequence; the last index is at least the first. |

Referenced records already exist and belong to the result's test sequence or run as appropriate. Widgets belong to the target observation. Property names are literal top-level keys: `ui.alt` is one key, not a nested path. A target may name a missing property because presence itself can be evaluated. A whole-run, whole-sequence, or whole-widget result does not establish that all individual properties were checked.

## Observed values

The `value_status` field distinguishes a reported value, established absence, and unknown information. It does not describe the JSON data type of the value itself.

| Representation | Meaning |
| --- | --- |
| `{"value_status":"value","value":null}` | A reported JSON value; this example is explicit null. |
| `{"value_status":"absent"}` | The oracle established that the property was absent. |
| `{"value_status":"unknown"}` | The oracle could not establish its value or presence. |

Omitting `observed_value` means none was supplied. Missing captured data alone does not establish application-level absence. Oracle readings and state capture can happen at different moments; retain both without replacing one with the other.

See the [property-oracle example](examples.md#property-oracle-result) for a complete result payload.

## Conditions over time

An oracle can evaluate one moment, an action execution, a range of observations, a whole sequence, or a whole run. An open-ended condition such as a warning never appearing can remain pending when testing stops. A condition explicitly bounded to recorded observations can pass if all required readings satisfy it. Neither establishes what happened between captures.

Optional `oracle_details["evidence.assessment"]` describes temporal context:

| Field | Meaning |
| --- | --- |
| `phase` | `during_sequence`, `end_of_sequence`, `during_run`, or `end_of_run`. |
| `semantics` | `open_ended` or `recorded_interval`, required when the context is supplied. |
| `termination_reason` | Optional for an end phase: the exact reason from the corresponding sequence or run finish event. |

Both phase and semantics are required when assessment context is supplied. Sequence phases require the result's test-sequence reference. A during phase refers to an active history; an end phase refers to an existing finish event. The oracle identifies the timing condition and supplies its outcome; this context does not execute or infer it. Ending because of tool failure does not itself prove that an application condition failed.

Every report contributes to result counts. To count distinct evaluated properties, use their observation, widget when applicable, and literal property key. Further reports for the same oracle and target remain separate. Evaluation coverage describes recorded results, not an assertion that unreported checks never occurred.
