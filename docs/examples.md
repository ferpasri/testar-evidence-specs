# Evidence examples

These examples illustrate the contract. Their application data, property names with a `source.*` prefix, oracle identifiers, and severity values are illustrative. Record definitions remain in the linked specifications.

## Hidden result panel

A loan page keeps both its form and its result panel in the DOM. Before submission, the result panel has `display: none`; afterward, it has `display: block` and its status text becomes `Denied`. The page address and title remain unchanged. Its child can report `visibility: visible` in both observations because its own style does not report the parent's display setting.

Comparing only the page address and title can group the form and result together. Comparing captured styles distinguishes the two layouts; comparing result text can also distinguish `Denied` from `Approved`. These comparisons do not remove hidden widgets from the original trace. See [capture meanings](capture-evidence.md#captured-widgets-and-visibility).

## Property oracle result

The oracle checks whether an image has alternative text. It establishes absence and reports failed, with source severity warning:

```json
{
    "test_run": "example:test-run:1",
    "test_sequence": "example:test-sequence:1",
    "name": "Alternative-text property is present",
    "oracle": {"id": "example:alt-present", "version": "1"},
    "oracle_target": {
        "target_type": "widget_property",
        "observation": "example:observation:1",
        "widget": "image-help",
        "property": "ui.alt"
    },
    "oracle_status": "failed",
    "observed_value": {"value_status": "absent"},
    "oracle_severity": {"label": "warning", "value": 0.4},
    "oracle_details": {}
}
```

The [oracle-result contract](oracle-results.md) defines these fields and their targets. The source supplies the severity label and numeric value.

## Optional run descriptions

```json
{
    "id": "example:test-run:1",
    "producer": "example-browser-adapter",
    "source_run": "source-invocation:42",
    "properties": {
        "evidence.origin": "Example explorer",
        "evidence.test_objective": "Explore the request loan feature",
        "evidence.adapter": "example-browser-adapter",
        "evidence.adapter_version": "example-build"
    }
}
```

The adapter-version string is an illustrative build identifier, not the version of the evidence format or an implementation dependency. See [run metadata](run-metadata.md) for the property meanings.

## Trace directories

A recorder can reserve a directory named `<sut>_<test_objective>_trace-<local timestamp>`, including milliseconds. It sanitizes filename labels, retains supplied original descriptions in metadata, and adds a numeric suffix on collision. Explicit output directories remain caller choices. The [recording contract](evidence-semantics.md#local-evidence-and-reconstruction) identifies evidence through records rather than directory labels.
