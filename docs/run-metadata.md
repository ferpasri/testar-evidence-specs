# Test-run and test-sequence metadata

A test run describes one invocation of testing. A test sequence identifies one ordered history within it. Store descriptions in each record's `properties` map: run descriptions apply to the invocation, while a sequence can supply its own more specific origin, test objective, or adapter information.

## Shared properties

| Property | Meaning | Supplied by |
| --- | --- | --- |
| `evidence.origin` | Descriptive name of the tool, script, or integration supplying execution information. | The caller or an integration that knows its source. |
| `evidence.test_objective` | What the caller intends to investigate or verify. | The caller. |
| `evidence.adapter` | Name or identifier of the translator collecting evidence. | The adapter, automatically when known. |
| `evidence.adapter_version` | The adapter's reported version. | The adapter, automatically when known. |

All four are optional. Supplied values are strings containing a non-whitespace character; preserve original Unicode, spacing, and capitalization. Omitted properties stay absent. A consumer can display a run description as context for its sequences, but does not copy that value into sequence records. Describe APIs as `test_objective` using the language's naming convention.

The required run `producer` identifies the producing component. Optional origin describes the caller, while adapter information describes the translator. One origin can execute predefined preparation and exploratory actions. Candidate-list scope explains the reported choices separately from origin.

## Example

```json
{
    "id": "example:test-run:1",
    "producer": "example-browser-adapter",
    "source_run": "source-invocation:42",
    "properties": {
        "evidence.origin": "Example explorer",
        "evidence.test_objective": "Explore the request loan feature",
        "evidence.adapter": "example-browser-adapter",
        "evidence.adapter_version": "0.3.0"
    }
}
```

Each sequence names that run through `test_run`, has its own `id`, `source_sequence`, and increasing `sequence_index`, and retains its own properties. The invocation can contain several sequences without linking their observation histories. Source identifiers and descriptions do not replace the shared record IDs.

The test objective describes intent, not proof that an oracle evaluated it. Oracle results preserve the actual evaluations and outcomes. Matching or abstraction uses explicit rules; shared descriptions do not merge traces.
