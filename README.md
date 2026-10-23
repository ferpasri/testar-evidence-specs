# TESTAR Evidence Specifications

This repository defines how testing tools record GUI execution evidence in a shared format. A test run can contain several test sequences. Each sequence preserves observations, recorded states, available actions, action executions, recorded transitions, and oracle results.

```text
testar-evidence-specs
    |
    +-- testar-evidence-rust
    +-- testar-evidence-java
    +-- testar-evidence-python
    +-- other language implementations
                |
                v
        Portable recorded evidence
```

Adapters translate tool or framework data. Language libraries validate, save, and read it. Consumers inspect traces or use abstraction rules to build abstract states, actions, and transitions. An abstract model retains references to the evidence supporting it; abstraction is separate from trace recording.

A tool can capture every available widget while saving only selected properties. Capture descriptions identify the collected information and explain known exclusions or limits. Missing capture, established absence, and no recorded oracle evaluation remain different facts.

Oracle results identify what was evaluated and retain reported statuses, expected and observed values, and optional source-defined severity. A failed condition and the importance assigned to its finding are separate information.

## Documents

- [Terminology](docs/terminology.md): shared terms and examples.
- [Architecture](docs/architecture.md): responsibilities and data flow.
- [Recorded evidence](docs/evidence-semantics.md): state, action, property, and abstraction meanings.
- [Evidence format](docs/evidence-format.md): fields, event order, references, and lifecycle.
- [Oracle results](docs/oracle-results.md): oracle targets, observed values, severity, and temporal results.
- [Run metadata](docs/run-metadata.md): optional source, test objective, and adapter descriptions.
- [Capture and properties](docs/capture-evidence.md): capture scope, selections, omissions, and aliases.
- [Adapter integration](docs/adapter_gaps_and_considerations.md): gaps and implementer responsibilities.
- [JSON Schemas](schemas/README.md): machine-readable record shapes.
- [Conformance](conformance/README.md): shared input traces and expected results.
- [TODO](TODO.md): remaining work.

## Versions

The format uses `schema_version: 3`. Release tags identify published documents, schemas, and conformance cases. The terminology and hierarchy changes are prepared for the `0.3` release series. Related projects share `0.X`; each project can advance its patch `Y` independently. Consumers check schema compatibility separately from application release versions.

The contract and generic cases belong here. Implementations, adapters, abstraction algorithms, and viewers live in their own projects. Reading these specifications does not require a particular implementation language.
