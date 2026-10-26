# Evidence architecture

## Responsibilities

| Component | Responsibility |
| --- | --- |
| Specifications | Define generic records, meanings, data format, and shared conformance cases. |
| Language implementations | Validate, save, and read the records supplied by adapters. Keep enough history to check references and execution order. |
| Adapters | Translate tool or framework observations, actions, oracle results, and screenshots into generic records. |
| Abstraction components | Apply explicit abstraction rules to build abstract states, actions, and transitions. |
| Viewers and analysis tools | Inspect recorded traces and abstract models, retaining links to supporting evidence. |

```text
Testing tool -> adapter -> language implementation -> recorded evidence
                                                           |
                                      +--------------------+-------------------+
                                      |                                        |
                                      v                                        v
                                Trace inspection                    Abstraction rules
                                                                               |
                                                                               v
                                                                         Abstract model
```

Adapters can live inside a tool or be distributed as framework libraries. They translate different sources into the same format. A language implementation follows a published contract without contacting this repository during testing. APIs follow their language conventions while preserving the same records and meaning.

## Recording and analysis boundaries

Language implementations preserve the records and validate their references and lifecycle. [Recorded evidence](evidence-semantics.md) explains the meanings; [the format](evidence-format.md) defines event ordering and validation.

Adapters supply captured properties, actions, and oracle outcomes. [Capture conventions](capture-evidence.md) describe source meanings, selections, and limitations. [Adapter integration](adapter_gaps_and_considerations.md) explains the connection points and verification responsibilities.

Abstraction components group recorded evidence through explicit rules, during or after testing. The resulting abstract model retains its configuration, selected evidence, and supporting references. Abstraction is separate from the trace format. Viewers and action-selection components consume that model without changing the recorded facts.
