# Evidence schemas

The JSON Schemas use Draft 2020-12 and describe schema-version-3 record shapes:

- [Manifest](manifest.schema.json): collection identity and format version.
- [Evidence Event](evidence-event.schema.json): event identity, kind, payloads, and record types.

Validate each document against its schema, then validate event ordering and cross-record references as described in [the evidence format](../docs/evidence-format.md). A structurally valid event can still reference a widget from another observation or attempt to finish a run with active sequences.

Property maps and oracle-result details preserve source-defined JSON values. Optional capture descriptions and temporal-assessment metadata follow their documented conventions. The schemas do not infer source meanings or application correctness.
