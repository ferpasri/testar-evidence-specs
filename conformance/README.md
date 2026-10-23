# Conformance Data

Cases contain generic evidence and expected results. Every language implementation uses the same cases, independently of the testing tool that supplied observations.

## Case layout

```text
cases/<behavior>/
    README.md
    manifest.json
    evidence.jsonl
    expected.json
    artifacts/          # When referenced by evidence
```

The manifest, events, and referenced artifacts are inputs. `expected.json` describes the records or error expected after processing them. It is test metadata; model producers do not generate that file.

The input format is described in [Model semantics](../docs/model-semantics.md#local-data-and-replay). [Evaluations and check results](../docs/check-evaluations.md) defines the terminology, record fields, and target forms. Each observation and each performed action keeps its own record.

## Input trace, reconstructed model, and expected result

`evidence.jsonl` is the input trace: one JSON event per line. It represents evidence that an adapter could record during a test execution. The shared cases supply controlled examples so every implementation receives the same input.

For example, a trace can describe this sequence. This is a readable illustration; the file itself contains JSON events:

```text
Run started
Observation 1 captured, including widgets and properties
Click action defined and selected in observation 1
Action submission accepted
Observation 2 captured
Action result connects the click to observation 2
Test-oracle evaluation targets a property in observation 2
Run finished
```

The three parts have different roles:

| Part | Meaning |
| --- | --- |
| Input trace | `manifest.json`, `evidence.jsonl`, and referenced artifacts supplied by the case. |
| Reconstructed model | The library's in-memory interpretation of those events: observations, widgets, properties, actions, execution links, and evaluations. Each library can use its own internal classes. |
| Expected result | `expected.json`: the summary that tests should obtain from the model, or the expected error and previously accepted records for an invalid trace. |

```text
Input trace -> library reads events -> reconstructed model
                                             |
                                      test builds a summary
                                             |
                                      compare with expected.json
```

The reconstructed model is not an abstract or merged model. It preserves the concrete records and their original links. `expected.json` is a test expectation, not another execution trace or a normal library output file. Tests also compare full accepted event contents and artifact bytes, because the summary alone does not contain every captured value.

Reader tests open the supplied trace. Producer tests decode the supplied events, submit them through the implementation's recording API, and reopen the generated files. Both check against the same expectations. Using the same cases in different languages checks agreement on those examples; directly reading one implementation's output with another implementation is a separate interoperability test.

## Comparing accepted cases

A reader processes events in order, builds the summary described below, and compares it with `expected.json`. A producer uses its API to record equivalent evidence, reopens it, and performs the same comparison. Both preserve the input properties, evaluation fields, and artifact bytes. JSON object-key order and whitespace are insignificant. Numeric values retain their exact JSON meaning.

Accepted results contain:

- `status: "accepted"`.
- `counts`: accepted events, runs, observations, actions, executions, check results, artifact references, and concrete snapshots.
- `runs`: IDs and running/finished status.
- `observations`: IDs, run, sequence, snapshot ID, capture and candidate scopes, ordered widget IDs, candidates, and artifact IDs.
- `actions`: IDs and kinds.
- `executions`: the selected execution under `execution`, its lifecycle `status`, result observation ID or null under `result`, and submission error or null under `error`.
- `checks`: complete check result records from `checked` event payloads, including rule, target, status, and supplied readings/expectations.
- `concrete_states`: snapshot IDs.

The summary checks record IDs, links, execution statuses, and complete check results. In addition, compare the complete accepted run, action, observation, and execution payloads with their inputs, including state and widget properties, parent links, capture descriptions, aliases, omissions, and parameters. Verify each run's recorded ending reason and each referenced artifact's bytes. These comparisons apply after reopening or replaying the saved data as well as after recording it.

Runs, observations, actions, and snapshot IDs are sorted by string identifier. Executions follow selection order; check results follow event order. Widget, candidate, and artifact lists preserve their recorded order. The `checks` count includes every recorded check result, regardless of status or repeated rules and targets. A run's finished status retains its recorded ending reason in the original evidence.

For the run metadata cases, compare the complete `run_started` payloads as well as the summary. Preserve supplied descriptions exactly, including Unicode and spacing, and preserve omitted keys as omitted. [Run metadata](../docs/run-metadata.md) defines the shared property names.

## Comparing rejected cases

A rejected result has `status: "rejected"`, an `error`, and an `accepted_prefix`. The prefix is the accepted-case summary immediately before the rejected event; that event changes neither the accepted event count nor the model records.

The error identifies a generic `code`, the rejected event's ID under `event`, and the invalid field path under `field`. The check-target cases use `invalid_check_target` for a missing referenced widget or an observation belonging to another run. Implementations may normalize their own error types to these values; error-message wording is unrestricted.

Reader tests stop at the first invalid event. Producer tests submit that record through the API, confirm rejection, and verify the prefix remains intact. Reopening the successfully recorded prefix produces the same accepted result.

## Cases

- [Initial observation](cases/initial-observation/README.md): one captured widget, candidate, and screenshot.
- [Supplied run metadata](cases/run-metadata-supplied/README.md): exact descriptions and adapter details across setup and exploration.
- [Omitted run metadata](cases/run-metadata-omitted/README.md): empty and adapter-only properties without invented defaults.
- [Flexible origin labels](cases/run-origin-labels/README.md): arbitrary source names and distinct runs sharing a label.
- [Targeted property evaluations](cases/targeted-property-checks/README.md): ten completed actions and two property evaluations.
- [Widget property sweep](cases/widget-property-sweep/README.md): one rule evaluated separately for three image widgets.
- [Property reading kinds](cases/property-reading-kinds/README.md): established absence, explicit null, and unknown information.
- [Broader evaluation targets](cases/broader-check-targets/README.md): run, observation, state property, widget, execution, and sequence targets, including check results arriving after the run finishes.
- [Capture and properties](cases/capture-properties/README.md): complete, filtered, and unavailable trees; nested values, aliases, omissions, and precise evaluations.
- [Discovered action selection](cases/discovered-action-selection/README.md): several candidate bindings, one completed selection, and retained unselected actions.
- [Scripted action trace](cases/scripted-action-trace/README.md): form steps, repeated visits, execution parameters, a rejected submission, and an interrupted execution.
- [Temporal evaluations](cases/temporal-evaluations/README.md): ongoing conditions, recorded-interval assessments, and results at different run endings.
- [Check result with an unknown widget](cases/invalid-check-widget/README.md): a target widget outside the captured observation.
- [Check result with an observation from another run](cases/invalid-check-observation-run/README.md): a target observation belonging to another run.
