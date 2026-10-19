# TESTAR State Model Specifications

This repository defines how testing tools record GUI execution evidence in a shared format. An observation records what a tool captured at one moment; an execution trace connects observations through the actions performed between them.

```text
testar-state-model-specs
    |
    +-- testar-state-model-rust
    +-- testar-state-model-java
    +-- other language implementations
                |
                v
        Generic concrete traces
```

A tool-specific adapter translates its tool's UI data into generic records. A language implementation validates, stores, and reads those records. Every visit remains a separate observation, even when the same screen is visited again.

Recording a trace does not require deciding which screens should count as equivalent. An optional abstraction component can group observations later, or while a test is running. Different testing objectives can produce different models from the same saved evidence. A viewer can display concrete traces, derived models, or both.

## Documents

- [Architecture](docs/testar_state_model_architecture.md): responsibilities, capture limits, and data flow.
- [Model semantics](docs/model-semantics.md): records, concrete execution links, and optional derived views.
- [Conformance](conformance/README.md): shared inputs and expected results.
- [TODO](TODO.md): specification, fixture, and implementation work still required.

The contract and shared test data belong here. Language libraries, tool adapters, abstraction algorithms, services, and viewers belong in their respective implementation projects.
