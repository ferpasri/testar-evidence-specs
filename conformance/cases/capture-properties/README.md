# Capture Properties

Three observations preserve different capture scopes:

- A complete two-widget tree includes selected properties and explains the deliberate exclusion of styles.
- A filtered tree reaches its two-widget limit. It preserves a nested attributes object, original and alias keys for empty alternative text, an explicit null, booleans, numbers, bounds, and a list containing null. Known omissions explain unsupported styles and a description excluded by a length limit.
- Unavailable widget data leaves an empty widget list and an unknown UI. A state-level omission records that the application address could not be read.

One test oracle confirms that the second observation's `ui.alt` property exists, even though its captured string is empty. Other captured properties have no recorded evaluation. Another test oracle cannot establish the third observation's address and reports an unknown reading.

Read the events in order and compare the result with `expected.json`. Preserve the full observed payloads, including nested properties, capture descriptions, omissions, aliases, and original JSON values. [Capture and property evidence](../../../docs/capture-evidence.md) defines these optional conventions.
