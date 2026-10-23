# TESTAR Evidence Specifications

This repository defines how testing tools record GUI execution evidence in a shared format. An observation records what a tool captured at one moment; an execution trace connects observations through the actions performed between them.

```text
testar-evidence-specs
    |
    +-- testar-evidence-rust
    +-- testar-evidence-java
    +-- testar-evidence-python
    +-- other language implementations
                |
                v
        Generic concrete traces
```

A tool-specific adapter translates its tool's UI data into generic records. A language implementation validates, stores, and reads those records. Every visit remains a separate observation, even when the same screen is visited again. Each completed action connects its before and resulting observations, forming a state-action-state execution trace.

Observations retain discovered candidate actions, including those never selected. Executions identify the selections and their recorded outcomes. `candidate_scope` says whether the tool supplied generated actions, actions eligible for selection, or a list whose origin is unknown.

Capture descriptions explain which widgets and properties were collected, any selection rules or limits, and known omissions. A tool can capture every available widget while saving only selected properties. The capture description identifies which properties are collected and, if known, explains exclusions or limitations. Property names preserve what was actually read; adapters verify a source property's meaning before adding a common name.

Captured properties and recorded evaluations are separate evidence. A check result records the outcome and evidence from a test oracle's evaluation: the rule, target, status, and any reported reading. Property targets identify the particular observation and widget, so consumers can see which properties have recorded evaluations. A result about several visits identifies the whole run or the first and last observations it concerns.

Recording a trace does not require deciding which screens should count as equivalent. An optional abstraction component can group observations later, or while a test is running. Different testing objectives can produce different models from the same saved evidence. A viewer can display concrete traces, derived models, or both.

Releases use `0.X.Y`. The `0.X` series is shared with language implementations and related state-model projects; each project can advance its patch version `Y` independently. Release tags identify published specifications and cases, while `schema_version` identifies the evidence format. A release can add explanations and test cases while keeping the same evidence format.

## Documents

- [Architecture](docs/testar_state_model_architecture.md): responsibilities, capture limits, and data flow.
- [Adapter gaps and considerations](docs/adapter_gaps_and_considerations.md): integration gaps, possible solutions, and implementer responsibilities.
- [Recorded evidence](docs/model-semantics.md): records, action links, captured property meanings, and optional derived views.
- [Run metadata](docs/run-metadata.md): optional origin, test objective, and adapter descriptions.
- [Capture and properties](docs/capture-evidence.md): captured areas, property selection, limits, omissions, and aliases.
- [Evaluations and check results](docs/check-evaluations.md): terminology, targets, rules, outcomes, readings, and temporal assessments.
- [Conformance](conformance/README.md): shared inputs and expected results.
- [TODO](TODO.md): specification, fixture, and implementation work still required.

The contract and shared test data belong here. Language libraries, tool adapters, abstraction algorithms, services, and viewers belong in their respective implementation projects.
