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

## Comparing rejected cases

A rejected result has `status: "rejected"`, an `error`, and an `accepted_prefix`. The prefix is the accepted-case summary immediately before the rejected event; that event changes neither the accepted event count nor the model records.

The error identifies a generic `code`, the rejected envelope's ID under `event`, and the invalid field path under `field`. The check-target cases use `invalid_check_target` for a missing referenced widget or an observation belonging to another run. Implementations may normalize their own error types to these values; error-message wording is unrestricted.

Reader tests stop at the first invalid event. Producer tests submit that record through the API, confirm rejection, and verify the prefix remains intact. Reopening the successfully recorded prefix produces the same accepted result.

## Cases

- [Initial observation](cases/initial-observation/README.md): one captured widget, candidate, and screenshot.
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
