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

## Test runs and sequences

A test run is one invocation of testing and can contain several sequences. A sequence owns its observation and action history. A reused browser session does not merge those histories. Each completed action connects its source observation and resulting observation through a recorded transition. No transition is implied between different sequences.

Two observations can capture equal recorded-state content and remain different moments. The recorder preserves them. Screenshots can share stored bytes without establishing state equivalence.

## Capture and oracle evidence

A tool can capture every available widget while saving only selected properties. The capture description identifies which properties are collected and, if known, explains exclusions or limitations. Complete widget capture is relative to the stated source and area. Unavailable capture does not mean an empty interface.

Properties preserve source meanings. State-level `ui.url` and `ui.title` represent the captured full address and page, screen, or window title. Widget title attributes, visible text, and accessible names remain distinct. Common names require verified source meanings. Namespaced properties preserve additional tool information.

An adapter reports what an oracle evaluated, including successful results, rather than inferring checks from captured properties. Broad results retain their broad targets; detailed property results identify the exact observation, widget, and property. Oracle status and optional source severity remain separate.

## Abstraction

The same recorded evidence can support different abstract models. A navigation objective might ignore changing timestamps; a clock objective needs those differences. Abstraction rules identify which observations and actions to group, and each abstract transition retains its recorded-transition support.

Abstraction can run during testing or after reading saved files. The trace does not require abstract-state IDs. The abstract model records its rules, configuration, selected evidence, and supporting references. A path combining several test sequences does not establish that one execution traversed that entire path.

An observation-order link shows successive observations without inventing an action. It remains separate from transitions supported by actual executions.

See [terminology](terminology.md), [record meanings](evidence-semantics.md), [the format](evidence-format.md), [oracle results](oracle-results.md), and [adapter responsibilities](adapter_gaps_and_considerations.md).
