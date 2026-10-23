# Run metadata

A run can describe where its execution came from, what the caller intended to test, and which adapter collected its evidence. Store these optional descriptions in the run's `properties` object, supplied by the `run_started` event.

## Shared property names

| Property | Meaning | Supplied by |
| --- | --- | --- |
| `evidence.origin` | A descriptive name for the tool, script, or integration that supplied the execution. For example, `Example explorer` or `Loan scenario script`. | The caller or an integration that knows its source. |
| `evidence.test_objective` | What the caller intends to investigate or verify, such as `Verify that an oversized loan request is denied`. | The caller. |
| `evidence.adapter` | The name or identifier of the adapter that translated source information into evidence records. | The adapter, automatically when known. |
| `evidence.adapter_version` | The version reported by that adapter. | The adapter, automatically when known. |

Each supplied value is a string containing at least one non-whitespace character. Preserve its original text, including capitalization and spacing. Origin labels are flexible strings; there is no predefined list of tools, adapters, or execution styles. Use `test_objective` consistently for the test objective in APIs and explanations, with the naming style of the implementation language.

All four properties are optional. When information is unavailable or the caller supplies no description, omit the corresponding property. Recording still works. A generic filename label used by an example does not become a recorded test objective.

## Origin, producer, and adapter

The run's existing required `producer` field identifies the component that produced the evidence. `evidence.origin` adds the caller's descriptive source name. `evidence.adapter` identifies the translator when adapter information is available. A tool with a built-in adapter can use the same name for more than one of these descriptions when that accurately describes its integration.

For example, an exploratory tool can call a browser adapter. The origin names the exploratory tool, while the adapter properties name the browser adapter and its version. Callers do not need to supply adapter details that the adapter already knows.

One origin can perform predefined login steps and then choose actions during exploration. Its origin label remains the same across those steps. Describing how an individual action was chosen is separate from identifying the source of the run.

## Example run

This run describes a source tool's intent to investigate a loan feature:

```json
{
    "id": "example:run:1",
    "producer": "example-browser-adapter",
    "source_run": "example:session:1",
    "properties": {
        "evidence.origin": "Example explorer",
        "evidence.test_objective": "Explore the request loan feature",
        "evidence.adapter": "example-browser-adapter",
        "evidence.adapter_version": "0.2.1"
    }
}
```

The metadata is recorded once on the run. Observations, executions, and check results refer to that run. An API can expose optional `origin` and `test_objective` arguments that populate these properties; different languages can provide equivalent API forms.

## Using the descriptions

Viewers can show the descriptions and let users filter runs by them. A missing description remains unspecified; a viewer can still show the recorded producer separately. Run and event identifiers distinguish executions, including runs that share identical descriptions.

The test objective describes intent. Recorded check results show which conditions were actually evaluated and their outcomes. The objective alone does not establish that a condition was checked or that the intended result was achieved.

Directory names can use a shortened, filename-safe test objective label while the run retains the original description. Matching observations or deriving a model uses the consumer's selected rules and supporting records; these descriptions do not automatically merge traces.

These conventions use the existing extensible run properties. They add no required run fields and do not change the evidence format version.
