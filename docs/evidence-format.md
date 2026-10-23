# Evidence format

The shared format records test runs, test sequences, observations, action executions, recorded transitions, and oracle results. The manifest and each Evidence Event use `schema_version: 3`. This identifies the data format; repository release versions identify published documents and cases.

## Portable directory

```text
manifest.json
evidence.jsonl
artifacts/          # Referenced files, when present
```

The manifest contains `schema_version` and a nonblank `evidence_id` identifying the collection. Each UTF-8 JSONL line contains one Evidence Event and ends with a newline:

```json
{
    "schema_version": 3,
    "id": "example:event:1",
    "event": {
        "event_type": "test_run_started",
        "data": {
            "id": "example:test-run:1",
            "producer": "example-adapter",
            "source_run": "source-execution:42",
            "properties": {}
        }
    }
}
```

The [manifest schema](../schemas/manifest.schema.json) and [Evidence Event schema](../schemas/evidence-event.schema.json) define record shapes. Reference and lifecycle rules below apply in addition to those schemas. Unknown structural fields are rejected; property maps and oracle details preserve arbitrary named JSON values.

Identifiers and property keys are nonblank strings without control characters. Record identifiers distinguish their records within the collection. Observation and sequence indexes and microsecond timestamps are unsigned 64-bit integers. Property numbers retain their JSON precision. Missing, null, empty strings, and unavailable information remain distinguishable.

## Events and records

All fields listed below are required except where marked optional. Property maps can be empty. Optional fields are omitted when not supplied; supplying null is allowed only where explicitly stated.

| Event type | Data fields and meaning |
| --- | --- |
| `test_run_started` | `id`, `producer`, `source_run`, `properties`. Begins one invocation of testing. The source-run string retains the source tool's own identifier. |
| `test_sequence_started` | `id`, `test_run`, `sequence_index`, `source_sequence`, `properties`. Begins one history within its parent run. Sequence indexes increase in start order within that run and start at 1 or higher. |
| `action_described` | `id`, `action_type`, `properties`. Registers a reusable action description. |
| `observation_recorded` | `id`, `test_sequence`, `observation_index`, `timestamp_micros`, `recorded_state`, `available_action_scope`, `available_actions`, `artifacts`. Indexes increase within the sequence; they can restart in another sequence. |
| `action_selected` | `id`, `test_sequence`, `source_observation`, `action_description`, `target_widget`, `parameters`. Selects an action/target pair reported at that sequence's latest observation. `target_widget` can be null. Parameters contain the actual execution arguments. |
| `action_execution_reported` | `action_execution`, `outcome`, `error`. `outcome` is `returned` or `error`. A normal return requires `error: null`; an execution error retains its reported string. |
| `transition_recorded` | `action_execution`, `resulting_observation`. Completes a normally returned execution with a later observation from the same sequence. |
| `oracle_result_recorded` | The oracle-result fields defined in [oracle results](oracle-results.md). Results can arrive while testing or after the relevant history ends. |
| `test_sequence_finished` | `test_sequence`, `reason`. Ends that history and marks its unfinished execution incomplete. |
| `test_run_finished` | `test_run`, `reason`. Ends the invocation after all its started sequences have finished. |

An Evidence Event's ID identifies the reported occurrence. The `id` inside a start, observation, description, or selection payload identifies its domain record. An oracle result uses its Evidence Event ID as its result identity.

### Recorded state and available actions

A recorded state contains `id`, `properties`, an ordered `widgets` list, and `capture`. Each widget contains `id`, nullable `parent`, and `properties`. Widget IDs are unique within the state; parents refer to widgets there and must form an acyclic hierarchy. Several roots are allowed. Reusing a recorded-state ID requires identical recorded-state content. Different observations can reference that same content.

`capture` is `complete`, `filtered`, or `unavailable`. Property selection and limitations are described separately in [capture and properties](capture-evidence.md).

Each available action contains `action_description` and nullable `target_widget`. Its description already exists; any widget reference belongs to the observation's recorded state. Duplicate description/widget pairs are rejected. The action-list scope is `generated`, `eligible`, or `unknown`. Empty lists retain their supplied scope. A scripted integration can report only its next action with unknown scope; that does not claim discovery of alternatives.

### Artifacts

An artifact reference contains `id`, relative `path`, `media_type`, lowercase hexadecimal `sha256`, and optional `width` and `height` (unsigned integers or null). The path stays inside the portable directory. Absolute paths, traversal, symbolic-link escapes, and junction escapes are rejected. Verify the referenced bytes against their SHA-256 hash when recording and reading. Storage can reuse identical bytes without merging their observations. Screenshots commonly use PNG or JPEG.

## Lifecycle and validation

1. Start the test run before starting its sequences. Each sequence belongs to one run. Sequences can be interleaved, but keep independent histories and increasing sequence indexes.
2. Describe actions before observations reference them. A run and sequence must be active to accept new observations or action selections.
3. Each sequence can have one execution awaiting its execution result or resulting observation. Selection refers to that sequence's latest observation and an available action/target pair there.
4. Report the selected action's execution outcome once. A returned call awaits its resulting observation. An error ends that attempt and permits another selection; later captures can record partial effects but do not become its completed transition.
5. A recorded transition references a later observation in the execution's own sequence. One execution supports at most one recorded transition. Equal recorded-state content is permitted at both endpoints.
6. Finish each sequence explicitly, preserving the reason and making outstanding executions incomplete. Finish the parent run only after its sequences finish. A run can finish without any sequence. History ending does not change recorded oracle statuses.
7. Oracle results can refer to existing records after a sequence or run finishes. A later report is a separate result, including when it assesses the same oracle and target.

A validation failure changes neither the accepted records nor the saved log. Repeating an identical Evidence Event ID and content is idempotent. Conflicting content for that ID is rejected. Description and recorded-state IDs also retain fixed content. A complete invalid line is an error; an unfinished final line requires explicit recovery rather than silently discarding data.

The reconstructed execution status is `awaiting_execution_result`, `awaiting_resulting_observation`, `failed`, `completed`, or `incomplete`. A failed execution is an execution problem; an oracle result separately reports whether an application condition holds. Library-internal buffering does not create another domain status.

An observation-order link is a consumer display relationship between consecutive observations without a recorded transition. It is not written as a fabricated action event.

See [conformance](../conformance/README.md) for the normalized summaries and rejected-prefix expectations implementations verify.
