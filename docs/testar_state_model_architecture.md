# State Model Architecture

## Responsibilities

| Component | Responsibility |
| --- | --- |
| Specifications | Define generic evidence, data formats, rules, and shared test cases. |
| Language implementations | Provide an API to record, validate, store, read, and query concrete execution traces. |
| Tool-specific adapters | Translate tool-specific UI observations, actions, check results, and screenshots into generic records. |
| Optional abstraction components | Decide which observations, widgets, or actions should be grouped for a particular purpose. |
| Viewers and analysis tools | Display concrete traces or derived models and follow their links back to evidence. |

A language implementation follows a version of the specifications. It does not need to contact this repository while a test runs. APIs can look different in Rust and Java while recording data with the same meaning.

## Recording flow

```text
Testing tool -> tool adapter -> language implementation -> concrete traces
                                                               |
                                 +-----------------------------+
                                 |                             |
                                 v                             v
                          Trace viewer             Optional abstraction
                                                               |
                                                               v
                                                         Derived models
```

Adapters can stay in their tool's repository or be distributed separately. The language implementation does not depend on the testing tool. For example, web, Windows, and Android adapters translate different UI sources into the same generic widget records.

The library records each observation and action execution without automatically merging visits or requiring abstract-state identifiers. A trace can be saved and inspected without running an abstraction algorithm.

## Capture limits and extra properties

A tool may capture only part of a UI. The saved evidence explains what was captured:

- **Complete:** all widgets within the stated capture area were collected.
- **Filtered:** only a selected portion was collected, such as interactive widgets or the first 2,000 elements. The adapter records the filter or limit and whether information was cut off when known.
- **Unavailable:** the tool could not capture widget data. This is different from successfully capturing an empty screen.

Individual properties can also be unknown. If an adapter cannot read whether a button is enabled, it leaves that information unknown rather than recording `false`. A later reader must be able to distinguish "not captured" from "captured and disabled."

Properties can preserve useful details from a particular tool. A name such as `toolone.fingerprint` or `tooltwo.selector` starts with a prefix identifying its source; this is a **namespace**. It helps consumers recognize the property and avoids confusing similarly named values from different tools. These are examples of optional extra properties: another adapter does not have to provide them to produce valid generic evidence.

## Captured properties and evaluations

The adapter records captured data and check results separately. An observation can contain many widget properties while test oracles evaluate only a few of them. Each check result for a property evaluation identifies its observation, widget when applicable, property, rule, status, and any reported reading.

A test oracle applied across several widgets produces separate target-level results. This lets a consumer follow the evidence for each evaluation. Evaluations can also target a whole observation, an action execution, a run, or a sequence of observations. Their scope is preserved as recorded.

The [check result format](check-evaluations.md) distinguishes an established absent property from a null value or unavailable information. Implementations validate references and preserve these distinctions; adapters supply check results from evaluations actually performed by their testing tools' test oracles.

## Concrete traces and optional abstraction

A concrete trace follows the order of an execution:

```text
Observation 1 -> Action execution -> Observation 2 -> Action execution -> Observation 3
```

The links point forward through recorded occurrences. Pressing Back can return to a previously seen screen, but that visit is still a new observation. The recorder does not add an automatic reverse link or merge the two visits. Action outcomes refer to the observation actually obtained after execution; an interrupted action can remain without a resulting observation.

Abstraction means choosing which differences matter. A navigation test might ignore a changing clock; a test of that clock must preserve its changes. The right grouping therefore depends on the application being tested, its domain, the testing objective, and the purpose of the evidence.

An abstraction component can map observations 1 and 3 to state A and observation 2 to state B. That derived graph can contain A -> B -> A while all three concrete observations remain available. Another policy can produce a different grouping from the same trace.

Derived views identify the policy and its version/configuration, describe the input evidence used, and keep links to supporting observations and executions. Original evidence is preserved when a view is rebuilt or replaced.

## When abstraction runs

Abstraction can run while actions are being executed or later from saved traces. The recording process preserves concrete evidence in either case. Grouping observations and displaying the result are separate responsibilities.

An abstraction component can run in the same process as a test or in a separate service. Its location does not change the evidence format or require the recorder to use a particular grouping policy.

## Shared tests and consumers

Conformance cases use generic data and expected results, grouped by behavior. Each implementation uses the same inputs to check recording, reading, references, and lifecycle rules. Reader tests load supplied evidence; producer tests generate equivalent records. Tool-specific conversion tests stay with their adapters.

Concrete recording tests do not require an abstraction algorithm. Optional derived-view tests check policy information and links to supporting evidence. Tests of whether a particular grouping is useful belong with that abstraction component and its intended domain.

Services and report generators consume the shared data format. Recording evidence and viewing a trace remain possible without a particular graph library, database, web application, or abstraction component.
