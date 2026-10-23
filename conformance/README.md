# Conformance data

All implementations use the same generic input traces and expected results. Cases are grouped by behavior, independently of source tools or implementation languages.

## Input trace, reconstructed evidence, and expected result

```text
cases/<behavior>/
    README.md
    manifest.json
    evidence.jsonl
    expected.json
    artifacts/          # When referenced
```

| Part | Role |
| --- | --- |
| Input trace | The manifest, Evidence Events in JSONL, and referenced artifacts supplied to the implementation. |
| Reconstructed evidence | The implementation's in-memory records and relationships after processing accepted events. This preserves recorded facts and does not create an abstract model. |
| Expected result | `expected.json`: the normalized summary or expected error and accepted prefix used by tests. Producers do not write this test file as normal evidence output. |

For example, the input can start a test run and sequence, record an observation, select an available action, report its execution result, record a second observation and transition, report an oracle result, and finish both histories. A reader reconstructs those records; the test builds a summary and compares it with the expected file.

Reader tests consume the supplied trace. Producer tests decode the same events, report them through the library's public recording API, reopen the saved evidence, and compare against the same expectation. Also compare complete accepted payloads and artifact bytes: summaries do not contain every state property or metadata value.

## Accepted summary

Each accepted result contains `status: accepted` and these fields:

| Field | Content and order |
| --- | --- |
| `counts` | `evidence_events`, `test_runs`, `test_sequences`, `observations`, `action_descriptions`, `action_executions`, `recorded_transitions`, `oracle_results`, `artifact_references`, and `recorded_states`. Count unique accepted events, not identical retries. |
| `test_runs` | ID and running/finished status, sorted by ID. |
| `test_sequences` | ID, parent test run, sequence index, and running/finished status, sorted by ID. |
| `observations` | ID, test sequence, observation index, recorded-state ID, capture scope, available-action scope, widget IDs, available actions, and artifact IDs, sorted by observation ID. Inner lists preserve their recorded order. |
| `action_descriptions` | ID and action type, sorted by ID. |
| `action_executions` | Selection payload under `action_execution`, reconstructed `status`, nullable `execution_result`, and nullable `resulting_observation`, in selection order. An execution result retains its execution reference, outcome, and error. |
| `recorded_transitions` | Execution reference, test sequence, source observation, resulting observation, action description, and nullable target widget, in transition-recording order. |
| `oracle_results` | Complete result payloads, in event order. Retain omitted fields, including severity, as omitted. |
| `recorded_states` | Recorded-state IDs sorted by string value. |

A sequence's observation order is independent from another sequence's. Artifact-reference counts count references, even when image bytes are reused. Oracle-result counts include repeated assessments and all statuses. Preserve each history's finish reason in complete-payload comparisons, as well as descriptions, parents, properties, aliases, omissions, action parameters, oracle configurations, and observed values.

JSON object key order and formatting are insignificant; numeric values retain their exact JSON meaning. Verify artifact hashes and bytes. Retry each accepted event with identical identity and content and confirm that it does not change the result. Reopen or replay saved evidence and repeat the comparison.

## Rejected summary

A rejected result contains `status: rejected`, an `error`, and an `accepted_prefix`. The prefix is the accepted summary immediately before rejection. The rejected event changes neither accepted history nor stored data.

The error contains a generic `code`, rejected Evidence Event ID under `event`, and field path under `field`. Expected codes in these cases include `invalid_oracle_target`, `invalid_oracle_severity`, `invalid_reference`, `invalid_lifecycle`, and `invalid_transition`. Implementations can normalize native errors to these codes; message wording is unrestricted. Follow the specific field named by each expected result.

Stop at the first rejected event. Producer tests also verify rejection before append, then reopen the stored accepted prefix. JSON Schema checks record shape; lifecycle and reference checks apply separately as specified in [the format](../docs/evidence-format.md).

## Cases

- [Broader oracle targets](cases/broader-oracle-targets/README.md).
- [Capture properties](cases/capture-properties/README.md).
- [Available action selection](cases/available-action-selection/README.md).
- [Initial observation](cases/initial-observation/README.md).
- [Invalid oracle observation sequence](cases/invalid-oracle-observation-sequence/README.md).
- [Invalid oracle widget](cases/invalid-oracle-widget/README.md).
- [Invalid cross sequence transition](cases/invalid-cross-sequence-transition/README.md).
- [Invalid oracle sequence](cases/invalid-oracle-sequence/README.md).
- [Invalid oracle severity](cases/invalid-oracle-severity/README.md).
- [Invalid run finish](cases/invalid-run-finish/README.md).
- [Invalid sequence parent](cases/invalid-sequence-parent/README.md).
- [Multiple test sequences](cases/multiple-test-sequences/README.md).
- [Oracle severity](cases/oracle-severity/README.md).
- [Observed value kinds](cases/observed-value-kinds/README.md).
- [Run metadata omitted](cases/run-metadata-omitted/README.md).
- [Run metadata supplied](cases/run-metadata-supplied/README.md).
- [Run origin labels](cases/run-origin-labels/README.md).
- [Scripted action trace](cases/scripted-action-trace/README.md).
- [Targeted property oracles](cases/targeted-property-oracles/README.md).
- [Temporal evaluations](cases/temporal-evaluations/README.md).
- [Widget property sweep](cases/widget-property-sweep/README.md).
