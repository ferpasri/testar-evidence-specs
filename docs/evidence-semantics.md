# Recorded evidence and its meaning

The [terminology](terminology.md) defines the shared concepts. The [format](evidence-format.md) defines their fields, event ordering, and reference rules.

## Recorded states and observations

An observation identifies a captured moment in one test sequence. Its recorded state contains properties, widgets, and capture scope. A new observation keeps its own identity even if the page looks unchanged. Equal state content can share a recorded-state ID; reusing that ID requires identical content.

Widget IDs are local to a recorded state and do not establish identity across observations. Widget parents form an ordered, acyclic hierarchy. Missing properties and explicit null values remain distinct. Unknown enabled status is not false; unavailable widget capture is not an empty interface.

## Common descriptive properties

| State property | Meaning |
| --- | --- |
| `ui.url` | The full captured page address, including path parameters, query, and fragment. |
| `ui.title` | The captured page, screen, or window title. |

Read these values from the corresponding application or source API and preserve returned strings, including an empty title. Describe unavailable information rather than inventing it. Desktop applications can supply a title without a URL.

A widget's actual title attribute, visible text, and accessible name have different meanings. Keep the source property and verify its meaning before assigning a common name. A source property called Title is not sufficient to establish which of these it contains. A common name does not substitute accessible text for a missing title.

Properties can retain source prefixes such as `source.fingerprint` or `source.selector`. These identify where the information came from; consumers preserve unfamiliar properties. Adapters can declare an alias for the same captured property within one record. Equal values alone do not establish an alias or cross-tool equivalence. See [capture and properties](capture-evidence.md).

A captured widget is not necessarily shown on screen. Its own CSS visibility value can say `visible` while an ancestor hides it with `display: none`. Preserve the source meanings and widget hierarchy when interpreting these properties. See [captured widgets and visibility](capture-evidence.md#captured-widgets-and-visibility).

## Available actions and execution

An observation retains all the available actions reported by the producer, including those never selected. Available-action scope identifies generated, eligible, or unspecified lists. Ten reported choices and one selected execution remain ten choices and one occurrence. Comparing exploration at different observations requires explicit rules for relating states and actions.

Selection, the execution result, and the resulting observation are different evidence. A normal driver return says what happened at that execution boundary. A business response such as Incorrect password belongs to the resulting state; an oracle separately evaluates whether it meets an expected condition.

An execution error preserves its message and can follow partial effects. A later capture can record those effects as a separate observation without establishing a completed transition for that failed execution. Finishing a sequence marks unfinished executions incomplete.

A compound action has one execution when that is the boundary reported by the tool. Recording its internal operations separately requires the adapter to report those operations and intermediate observations. A capture without a reported action remains an observation without an invented execution.

## Oracles and coverage

Oracle results describe actual reported evaluations. A sequence with ten actions can contain two property evaluations, or a tool can evaluate an image condition separately on many widgets. Each result retains the oracle, target, status, expectation, observed value, optional severity, and supporting details supplied.

A property with no recorded result has no recorded evaluation. It may have been checked without detailed reporting. A result about a whole run, sequence, or observation does not establish individual widget-property coverage. Missing capture, established absence, and missing evaluation are separate facts. See [oracle results](oracle-results.md).

## Abstract models

Abstraction rules decide which differences matter for a particular objective. Observations can be grouped into abstract states, actions into abstract actions, and recorded transitions into abstract transitions. Original records and supporting references remain available. Grouping by equal page addresses does not establish identical content or corresponding widgets.

One trace can produce different abstract models under different rules. The recorder preserves evidence without requiring abstraction. Observation-order links show record order separately from action-supported transitions.

## Local evidence and reconstruction

The manifest identifies the evidence collection; JSONL preserves Evidence Events, and artifacts retain referenced bytes. Reading events rebuilds their records and recorded transitions in memory. This reconstruction supports validation and inspection; abstraction is a separate operation. Repeated identical event identities do not duplicate evidence, while conflicting content is rejected.

Directory names do not establish record identity or state equivalence. See the [trace-directory example](examples.md#trace-directories) for a naming convention.
