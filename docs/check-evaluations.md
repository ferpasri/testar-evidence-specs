# Evaluations and check results

Captured properties describe what a tool observed. A test oracle evaluates a rule on a particular target. The resulting outcome and supporting evidence are stored as a check result.

For example, a run can execute ten actions and record two property evaluations performed by test oracles. Another run can apply the same test oracle to several image widgets, recording a result for each widget. The saved evaluations make that difference visible.

## Terminology

| Term | Meaning |
| --- | --- |
| Test oracle | The mechanism that determines whether a condition is satisfied. |
| Rule | The condition being evaluated, such as whether an image has an `alt` property. |
| Evaluation | One application of a test oracle to a particular target according to a rule. |
| Check result | The recorded outcome and supporting evidence from an evaluation. |

For example, a test oracle evaluates the alternative-text rule on three image widgets. It performs three evaluations and produces three check results. In this document, `check` is used as an informal verb; `evaluation` names the operation and `check result` names its recorded evidence.

## Check result fields

A `checked` event stores a check result in its `data` field. The event envelope's ID identifies that result. The adapter supplies results for evaluations actually performed by the test oracle.

| Field | Meaning |
| --- | --- |
| `run` | Required ID of the run containing the evaluation. |
| `name` | Required, readable description of the condition checked by the test oracle. |
| `rule` | Required identity of the condition checked by the test oracle: a nonblank `id`, an optional nonblank `version`, and optional `parameters` containing named JSON values. |
| `target` | Required description of what was evaluated, using one of the target forms below. |
| `status` | Required result: `passed`, `failed`, `pending`, `unknown`, or `error`. |
| `expected` | Optional JSON value describing the expectation, interpreted according to the rule. |
| `actual` | Optional reading reported by the test oracle, using one of the reading forms below. |
| `details` | Named JSON values containing explanations or supporting information; defaults to an empty object. |

The `rule` ID identifies the condition checked by the test oracle. Its version distinguishes revisions when available, and its parameters preserve any supplied configuration. The test oracle performs the evaluation, the adapter supplies its result, and the state-model implementation validates and preserves that evidence. The check result's `name` explains the condition to a reader.

A `passed` result means the condition checked by the test oracle was satisfied. A `failed` result means that condition was not satisfied. An `error` result means the test oracle encountered a problem while performing the evaluation. `unknown` means the oracle could not determine an outcome, and `pending` means the evaluation had no definite result when it was recorded. Finishing a run preserves the reported statuses. A pending evaluation can therefore remain pending in a finished run. Each check result has its own event ID. Further reported results use their own event IDs.

## Evaluation targets

The `kind` field selects a target form:

| Kind | Additional fields | What it identifies |
| --- | --- | --- |
| `run` | None | The enclosing run. |
| `observation` | `observation` | One captured visit. |
| `state_property` | `observation`, `property` | A property of the visit's concrete state. |
| `widget` | `observation`, `widget` | A captured widget at that visit. |
| `widget_property` | `observation`, `widget`, `property` | A property of that captured widget. |
| `execution` | `execution` | One recorded action occurrence. |
| `sequence` | `from_observation`, `to_observation` | A range of visits in the same run, including its endpoints. |

Each target contains its kind and the fields listed for that form. Every reference identifies an existing record in the enclosing run. A widget belongs to the target observation's snapshot. A sequence's ending observation has a sequence number at least as large as its starting observation. Check results can be recorded after the run finishes.

A property is a nonblank key in the relevant properties map. For example, `ui.alt` is one complete property name. Its presence in the map is something a test oracle can check, so the target may name an absent property.

The target says what the test oracle evaluated. A run or observation target names the whole run or visit; a property target names one property at one visit. An evaluation concerning the absence of a widget targets the observation and describes the searched-for condition in its rule parameters.

## Reported readings

The `actual` field distinguishes these situations:

| Form | Meaning |
| --- | --- |
| `{"kind":"value","value":...}` | The test oracle obtained a JSON value, including an explicit null. |
| `{"kind":"absent"}` | The test oracle established that the property was absent. |
| `{"kind":"unknown"}` | The test oracle could not establish its value or presence. |

A property missing from captured data may have been omitted by the capture process. The adapter reports `absent` when the test oracle established absence, and `unknown` when the oracle could not establish the property's value or presence. Omitting `actual` means the producer supplied no reading for that evaluation. An explicitly supplied `expected: null` remains a recorded expectation.

Reported readings describe the evaluation. The snapshot preserves its captured properties separately. Differences between those two remain available as evidence.

## Conditions evaluated over time

An evaluation can concern one captured moment, an action and its result, or several observations in a run. Checking whether an image has an alternative-text property concerns that image at one observation. Checking whether a submitted request eventually produces a response concerns events over time. The rule describes the condition and its timing; the target identifies the recorded scope.

An open-ended condition such as "a warning never appears" remains `pending` while no violation has been observed. Ending the run does not prove that the condition will always hold. An observed violation can establish a `failed` result.

A rule can instead explicitly ask whether a condition held at every recorded observation within a specified interval. That finite question can produce `passed` when all required readings satisfy it. Its rule ID, version, parameters, and sequence target make that meaning clear. A successful assessment of recorded observations describes those observations; it does not establish what happened between captures.

### Assessment context

For temporal results, the adapter can supply this optional object in `details["evidence.assessment"]`:

| Field | Meaning |
| --- | --- |
| `phase` | `during_run` for a report made while the run is active, or `end_of_run` for a report made after it finishes. |
| `semantics` | `open_ended` for an unbounded condition, or `recorded_interval` for a condition explicitly bounded to recorded observations. |
| `termination_reason` | Optional when `phase` is `end_of_run`: the exact `reason` string from that run's `run_finished` event. |

When this object is supplied, `phase` and `semantics` are required. The rule defines the temporal meaning and any timing parameters. This context describes the reported assessment; it does not select or execute an evaluation algorithm.

For example, an open-ended condition can remain pending after normal completion:

```json
{
    "evidence.assessment": {
        "phase": "end_of_run",
        "semantics": "open_ended",
        "termination_reason": "normal completion"
    }
}
```

Normal completion, a deadline, an interruption, and a tool failure can end observation for different reasons. The adapter preserves the supplied reason and the outcome actually reported by the test oracle. A run ending because of a tool failure does not itself establish that an application condition failed. An `error` check result means the evaluator encountered a problem.

### Reports at the end of a run

A test oracle can report results while execution is running and after it finishes. The adapter appends each report as a separate `checked` event with its own event ID. Earlier results remain unchanged, including any `pending` results.

The rule identity, its parameters, and the target identify the condition and recorded scope being assessed. A sequence target names its first and last observations; a run target names the enclosing run through the result's `run` field. A result recorded after `run_finished` can refer to existing records in that run. The target keeps naming those records even when later observations exist. Its position in the event file records the order in which reports were supplied.

Every recorded result contributes to the evaluation count, including reports for a repeated rule and target. To count different evaluated properties, use their observation, widget when applicable, and property key. A run-level temporal result does not establish that individual widget properties were evaluated.

## Reading evaluation coverage

A property result identifies the observation, the widget when applicable, and the property name. Evaluating that property on a later visit concerns another captured moment. Test oracles can check several conditions on one target, so evaluation counts and distinct evaluated-property counts answer different questions.

When a test oracle evaluates a property on several widgets, the adapter emits one `checked` event per widget/property evaluation. The records share the rule ID and retain their individual targets, readings, and outcomes.

A property without a matching check result has no recorded evaluation. A result for an evaluation over a broad scope does not establish that every captured property was evaluated. Capture filters and the test oracle's scope provide context for interpreting any coverage count.

## Example

A test oracle checks whether a captured image widget has an `alt` property, recorded here as `ui.alt`. The test oracle establishes that the property is absent and reports `failed` because the required condition is not satisfied.

The following check result records that evaluation's outcome and evidence. This test oracle checks whether the property exists; another test oracle can check whether the alternative image description is meaningful:

```json
{
    "run": "example:run:1",
    "name": "Alternative-text property is present",
    "rule": {"id": "example:alt-present", "version": "1"},
    "target": {
        "kind": "widget_property",
        "observation": "example:observation:1",
        "widget": "image-help",
        "property": "ui.alt"
    },
    "status": "failed",
    "actual": {"kind": "absent"},
    "details": {}
}
```
