# Omitted run metadata

Two runs finish without captured observations. The first has an empty `properties` object. The second supplies only adapter name and version. Neither supplies an origin or test objective.

Both runs are accepted. Keep the first properties object empty and keep exactly the two supplied adapter properties on the second. Missing descriptions remain missing after recording, reopening, and replay; do not add an origin, execution style, or generic test objective as a default.

The case contains four events and two finished runs. Compare the summary with `expected.json` and compare complete accepted events with the input to verify both the supplied keys and the absent keys. See [run metadata](../../../docs/run-metadata.md).
